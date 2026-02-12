
> AOP만으로 처리하는 것보다 DDD 정책 객체 + 쿼리 조건으로 구현 

> 구현 요구 조건 
- 로그인 사용자가 어디까지 조회 가능한지(user_scope)
- 임원 : level = 1 (전체 조회 가능)
- 일반 직군 : level = 2 (임원 제외 조회 가능)
---
- 단순한 체크가 아닌 데이터 범위를 필터링 
- AOP는 메서드 전/후 공통 처리에는 강하나 어떤 데이터 행을 보여줄지 같은 쿼리 수준의 제어를 몰아넣으면
	1) 숨은 로직이 되어 디버깅이 어려움 
	2) 복잡한 조회(검색, 페이징, 조인)에서 누락우회 위험 
	3) 도메인 규칙이 인프라 코드(AOP)에 섞여 유지보수성이 떨어짐 

> 이미 permissionAspect 안에서 정책까지 구현하고 어노테이션만 붙여서 사용 하고 있는데 굳이?
> 문제 이유가 납득이 되지 않음 

- 실무 기준으로 문제가 되는건 현재가 아닌 **변경 시점**
	-언제 문제가 되는가 ?
	 1) 재사용 시작 순간 
	 2) 페이징 성능 이슈 
	 3) 책임 혼합 => 정책 판단 + 데이터 처리까지 모이면 수정 시 영향 범위가 커질 수 있음

> 단기적으로는 Aspect 구현하는것이 가능 
> 중기적으로 확장 가능성이 있다면 -> Aspect(공통진입처리)/Policy(규칙판단)/repository(Where 데이터 강제)

- Scope는 변경에 약한 규칙이다 -> 초기에 분리해두는게 운영 리스크가 적음

---
> Aspect 내부 구현

```JAVA
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ScopeCheck {
}
```

```JAVA
@Aspect
@Component
@RequiredArgsConstructor
public class ScopeAspect {

    private final EmployeeRepository employeeRepository;
    private final AuthFacade authFacade;

    // 상세 조회: 접근 가능 여부를 Aspect에서 직접 판정
    @Before("@annotation(ScopeCheck) && args(targetUserId,..)")
    public void checkDetail(Long targetUserId) {
        User me = authFacade.currentUser();
        User target = employeeRepository.findById(targetUserId)
                .orElseThrow(() -> new IllegalArgumentException("not found"));

        if (me.getJobLevel() > target.getJobLevel()) {
            throw new AccessDeniedException("scope denied");
        }
    }

    // 목록 조회: 반환 후 필터링(예시)
    @AfterReturning(pointcut = "@annotation(ScopeCheck)", returning = "result")
    public void filterList(Object result) {
        User me = authFacade.currentUser();
        if (me.getJobLevel() == 1) {
            return; // 임원은 전체
        }

        if (result instanceof List<?>) {
            List<?> list = (List<?>) result;
            list.removeIf(item -> ((EmployeeDto) item).getJobLevel() == 1);
        }
    }
}

```

> 확장 기능 구현 
- Aspect : 현재 사용자 기준 scope계산 까지만 
- Policy : 누가 누구를 볼 수 있는지 규칙 담당 
- Repository : DB WHERE 로 최종 강제

```JAVA
public enum UserScopeType {
    FULL,                // 임원 포함
    EXCLUDE_EXECUTIVE    // 임원 제외
}
```

```JAVA
@Component
public class UserScopePolicy {

    public UserScopeType resolve(User me) {
        return me.getJobLevel() == 1
                ? UserScopeType.FULL
                : UserScopeType.EXCLUDE_EXECUTIVE;
    }

    public void validateDetail(User me, User target) {
        if (me.getJobLevel() > target.getJobLevel()) {
            throw new AccessDeniedException("scope denied");
        }
    }
}
```

```JAVA
@Aspect
@Component
@RequiredArgsConstructor
public class ScopeAspect {

    private final AuthFacade authFacade;
    private final UserScopePolicy userScopePolicy;

    @Around("@annotation(ApplyScope)")
    public Object applyScope(ProceedingJoinPoint pjp) throws Throwable {
        User me = authFacade.currentUser();
        UserScopeType scope = userScopePolicy.resolve(me);

        Object[] args = pjp.getArgs();
        for (int i = 0; i < args.length; i++) {
            if (args[i] instanceof UserScopeType) {
                args[i] = scope; // 서비스 파라미터로 주입
            }
        }
        return pjp.proceed(args);
    }
}

```

```JAVA
@Service
@RequiredArgsConstructor
public class EmployeeQueryService {

    private final EmployeeRepository employeeRepository;

    @ApplyScope
    public Page<EmployeeDto> search(EmployeeSearchCond cond, Pageable pageable, UserScopeType scope) {
        return employeeRepository.search(cond, pageable, scope);
    }
}

```

```JAVA
@Repository
@RequiredArgsConstructor
public class EmployeeRepositoryImpl implements EmployeeRepositoryCustom {

    private final JPAQueryFactory queryFactory;

    @Override
    public Page<EmployeeDto> search(EmployeeSearchCond cond, Pageable pageable, UserScopeType scope) {
        QEmployee e = QEmployee.employee;

        BooleanExpression scopeExpr = (scope == UserScopeType.FULL)
                ? null
                : e.jobLevel.ne(1); // 임원 제외를 DB에서 강제

        List<EmployeeDto> content = queryFactory
                .select(Projections.constructor(EmployeeDto.class, e.id, e.name, e.jobLevel))
                .from(e)
                .where(scopeExpr)
                .offset(pageable.getOffset())
                .limit(pageable.getPageSize())
                .fetch();

        long total = queryFactory
                .select(e.count())
                .from(e)
                .where(scopeExpr)
                .fetchOne();

        return new PageImpl<>(content, pageable, total);
    }
} 
```
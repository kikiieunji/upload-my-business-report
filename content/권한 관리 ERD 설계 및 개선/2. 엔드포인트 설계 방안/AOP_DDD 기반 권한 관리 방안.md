- 고려 사항
- 인터셉트/AOP => DDD기반으로 하고 있기 때문에 앞단에서 어떤식으로 권한관리를 모듈화해서 필요할때만 갖다가 쏙쏙 쓸 수 있는지 방안 고려
- 커스텀어노테이션을 어떻게 사용할 것인지

---

### AOP/DDD/MOCKING 개념

AOP(Aspect-Oriented Programming, 관점 지향 프로그래밍)

: 로깅, 트랜잭션 등 여러 모듈에 걸쳐 공통으로 사용되는 부가 기능(횡단 관심사)을 핵심 비즈니스 로직에서 분리하여 모듈화하는 프로그래밍 패러다임입니다. 코드 중복을 제거하고 유지보수성을 향상시키며, 핵심 로직의 수정 없이 기능 추가를 가능하게 한다.

[도메인 주도 설계](https://www.google.com/search?q=%EB%8F%84%EB%A9%94%EC%9D%B8+%EC%A3%BC%EB%8F%84+%EC%84%A4%EA%B3%84&sca_esv=50e270cee628dfd9&sxsrf=ANbL-n7YGY06Mx_HT1Pkyn8qByj3Cq9ZKA%3A1769662706610&ei=8uh6ad_5JL2D2roP9Obg-Ao&ved=2ahUKEwiJ3vPS-6-SAxXyslYBHUFpLJMQgK4QegQIARAC&uact=5&oq=DDD&gs_lp=Egxnd3Mtd2l6LXNlcnAiA0RERDILEAAYgAQYsQMYgwEyChAAGIAEGEMYigUyBRAAGIAEMgUQABiABDIFEAAYgAQyBRAAGIAEMgUQABiABDIFEAAYgAQyCxAAGIAEGLEDGIMBMgUQABiABEiOD1AAWMUNcAR4AZABAJgBmQGgAekFqgEDMC42uAEDyAEA-AEBmAIKoAKkBqgCCsICDRAjGIAEGCcYigUY6gLCAgoQIxiABBgnGIoFwgILEC4YgAQYsQMYgwHCAgQQABgDwgILEC4YgAQY0QMYxwHCAgQQIxgnwgIHEC4YgAQYCsICDRAuGIAEGNEDGMcBGAqYAwfxBWeDB-NJ3BEvkgcDNC42oAfCMrIHAzAuNrgHlQbCBwcwLjQuNS4xyAcqgAgA&sclient=gws-wiz-serp&mstk=AUtExfAX7DpW9Eu6tATMx0RJKCLJRdKDsGWc-ZiG7ltD0BKKKp7lsU9iExc70yI0yffNrpNKwcz-kz0au_9_0-OrdmEmYgvxAALJq41tttA4Jvw1YSwx0AcuJnpzn-VoqTuaIEkBhTTwfbv-Hy2ClQVpsuaWNN0TwVJSHrKBCJfiNzWjosD965Bp3I4Mia9zcajomJCISk5L7eLUTIASpunqCgTMfwX1LCo7iP4zqyZKL4_24dbe7bKsLgAtNEYhstUTp_85w219rFuTdKsKvrSGbBul&csui=3)[(Domain-Driven Design, DDD)](https://www.google.com/search?q=%28Domain-Driven+Design%2C+DDD%29&sca_esv=50e270cee628dfd9&sxsrf=ANbL-n7YGY06Mx_HT1Pkyn8qByj3Cq9ZKA%3A1769662706610&ei=8uh6ad_5JL2D2roP9Obg-Ao&ved=2ahUKEwiJ3vPS-6-SAxXyslYBHUFpLJMQgK4QegQIARAD&uact=5&oq=DDD&gs_lp=Egxnd3Mtd2l6LXNlcnAiA0RERDILEAAYgAQYsQMYgwEyChAAGIAEGEMYigUyBRAAGIAEMgUQABiABDIFEAAYgAQyBRAAGIAEMgUQABiABDIFEAAYgAQyCxAAGIAEGLEDGIMBMgUQABiABEiOD1AAWMUNcAR4AZABAJgBmQGgAekFqgEDMC42uAEDyAEA-AEBmAIKoAKkBqgCCsICDRAjGIAEGCcYigUY6gLCAgoQIxiABBgnGIoFwgILEC4YgAQYsQMYgwHCAgQQABgDwgILEC4YgAQY0QMYxwHCAgQQIxgnwgIHEC4YgAQYCsICDRAuGIAEGNEDGMcBGAqYAwfxBWeDB-NJ3BEvkgcDNC42oAfCMrIHAzAuNrgHlQbCBwcwLjQuNS4xyAcqgAgA&sclient=gws-wiz-serp&mstk=AUtExfAX7DpW9Eu6tATMx0RJKCLJRdKDsGWc-ZiG7ltD0BKKKp7lsU9iExc70yI0yffNrpNKwcz-kz0au_9_0-OrdmEmYgvxAALJq41tttA4Jvw1YSwx0AcuJnpzn-VoqTuaIEkBhTTwfbv-Hy2ClQVpsuaWNN0TwVJSHrKBCJfiNzWjosD965Bp3I4Mia9zcajomJCISk5L7eLUTIASpunqCgTMfwX1LCo7iP4zqyZKL4_24dbe7bKsLgAtNEYhstUTp_85w219rFuTdKsKvrSGbBul&csui=3)

:  에릭 에반스(Eric Evans)가 제안한 소프트웨어 설계 철학으로, 복잡한 비즈니스 로직(도메인)을 핵심으로 삼아 이를 모델링하고 구현하는 방식입니다. 전략적 설계(컨텍스트 매핑, 바운디드 컨텍스트)와 전술적 설계(엔티티, 벨류 객체 등) 패턴을 통해 비즈니스 도메인과 코드의 일치를 추구하며, 특히 복잡한 대규모 시스템 개발에 적합한 접근법이다.

Mocking

: 소프트웨어 개발에서 테스트 시 실제 객체 대신 가짜 객체(Mock Object)를 만들어 의존성을 분리하고 상호작용을 시뮬레이션 하는 기법이다.



### 커스텀 어노테이션(@RequirePermission)을 사용하여 Permission Enum과 Spring Security 연결하려면 ? (초안 방향으로 사용 예정)

Spring AOP(Aspect Oriented Programming)

  

1. 설계 흐름도(Flow)
    1. 요청 : 사용자가 API 호출 (/accident)
    2. 가로채기(AOP) : 컨트롤러 메서드 실행 전, AOP가 @RequirePermission을 감지
    3. 권한 추출 : 어노테이션에 있는 Permission.ACCIDENT.READ(Enum)값을 가져옴
    4. 보유 권한 확인 : SecurityContextHolder(현재 로그인한 사용자)에 저장된 권한 목록(String)과 비교
    5. 판단:
        1. 있으면 -> 통과(메서드 실행)
        2. 없으면 -> AccessDeniedException 발생 (403 Forbidden)

---

1. Permission Enum 정의

```Java


public enum Permission {
    // 사고 관리
    ACCIDENT_READ,
    ACCIDENT_WRITE,

    // 급여 관리
   PAYROLL_READ,
   PAYROLL_WRITE;          
    
  
    // Enum 이름을 그대로 권한 키로 사용
    public String getKey() {
        return this.name();
    }
}
```

  

2. 커스텀 어노테이션 생성

```Java
import java.lang.annotation.*;

@Target({ElementType.METHOD, ElementType.TYPE}) // 메서드와 클래스에 붙일 수 있음
@Retention(RetentionPolicy.RUNTIME)             // 실행 중에 읽을 수 있어야 함
@Documented
public @interface RequirePermission {
    Permission value(); // Enum을 강제함 (오타 방지)
}

```

  

3. 로그인 시 권한 로딩(UserDetailService)

```Java
// UserDetailsService 내부 로직 예시
@Override
public UserDetails loadUserByUsername(String username) {
    // 1. DB에서 User 조회
    User user = userRepository.findByUsername(username)...;
    
    // 2. 현재 접속한 UDP(프로필) 기준의 권한 조회 (매핑 테이블 조인)
    // Query: SELECT p.key_name FROM permission p ... WHERE udp.id = ?
    List<String> permissionKeys = udpRepository.findPermissionKeysByUdpId(currentUdpId);
    
    // 3. String 권한 키를 GrantedAuthority로 변환
    List<SimpleGrantedAuthority> authorities = permissionKeys.stream()
            .map(SimpleGrantedAuthority::new) // "ACCIDENT_READ" 그대로 들어감
            .collect(Collectors.toList());

    return new CustomUserDetails(user, authorities);
}

```

  

4. AOP Aspect 구현

```Java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.stereotype.Component;

import java.util.Collection;

@Aspect
@Component
public class AuthorizationAspect {

    // @RequirePermission 어노테이션이 붙은 곳을 가로챔
    @Before("@annotation(requirePermission)")
    public void checkPermission(JoinPoint joinPoint, RequirePermission requirePermission) {
        
        // 1. 어노테이션에서 필요한 권한(Enum) 추출
        String requiredKey = requirePermission.value().getKey(); // "ACCIDENT_READ"

        // 2. 현재 로그인한 사용자의 권한 목록 가져오기
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        
        if (auth == null || !auth.isAuthenticated()) {
            throw new AccessDeniedException("로그인이 필요합니다.");
        }

        Collection<? extends GrantedAuthority> userAuthorities = auth.getAuthorities();

        // 3. 권한 비교 (하나라도 일치하면 통과)
        boolean hasPermission = userAuthorities.stream()
                .anyMatch(grantedAuthority -> grantedAuthority.getAuthority().equals(requiredKey));

        // 4. 권한 없으면 예외 발생 (Spring Security가 403으로 처리함)
        if (!hasPermission) {
            throw new AccessDeniedException("해당 리소스에 접근할 권한이 없습니다: " + requiredKey);
        }
    }
}

```

  

5. 실제 사용 - Controller

```Java
@RestController
@RequestMapping("/api/accidents")
public class AccidentController {

    // 변경 전: URL 패턴 매칭 (Config 설정 복잡)
    // 변경 후: 깔끔한 어노테이션
    
    @RequirePermission(Permission.ACCIDENT_READ) // -> DB에 'ACCIDENT_READ'가 있어야 통과
    @GetMapping
    public ResponseEntity<List<AccidentDto>> getList() {
        return ResponseEntity.ok(accidentService.getAll());
    }

    @RequirePermission(Permission.ACCIDENT_WRITE) // -> DB에 'ACCIDENT_WRITE'가 있어야 통과
    @PostMapping
    public ResponseEntity<Void> create(@RequestBody AccidentDto dto) {
        accidentService.create(dto);
        return ResponseEntity.ok().build();
    }
}

```

---

[ AOP 장점 ]

- 타입 안정성(Type Safety) : 컴파일러가 Permission.ACCIDENT_READ Enum을 체크해 주기 때문에 오타를 낼 일이 없음
- 유지보수성 : 권한 로직을 수정할 필요가 없음( 비즈니스 로직 중심 권한 관리)
- 명시성 : 직관적으로 어떤 권한이 필요한지 알 수 있음

** AccessDeniedException -> GlobalExceptionHandler 로 처리하면 더 좋음

```Java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(AccessDeniedException.class)
    public ResponseEntity<String> handleAccessDenied(AccessDeniedException e) {
        // 403 Forbidden 리턴
        return ResponseEntity.status(HttpStatus.FORBIDDEN)
                             .body("접근 권한이 부족합니다.");
    }
}
```

  
### AOP/DDD 기반 권한 관리 모듈화 방안 (ver.1)

- 개발 목표

1. 앞단(Interceptor/AOP)에서 @RequirePermission 같은 선언을 붙임
2. 필요한 곳에서만 쏙 권한 검사를 모듈로 가져다 쓰고
3. 서비스 코드를 깔끔하게 유지

  

- 왜  Service단에서 권한검사가 필요한가?

** AOP는 메서드 호출을 가로 채는 기술, 시스템의 모든 실행 경로가 그 메서드를 반드시 거친다는 보장이 아님

-> AOP가 가로채는 범위는 생각보다 좁음

- Spring 프록시를 통해 들어오는 호출만 잡음
- 잡히는 경우
	- Controller -> Service 호출
	- 다른 Bean -> Service 호출

- 안잡히는 대표 케이스 : 같은 클래스 내부 호출(Self-invocation)

```Java
@Service
public class AccidentService {

    public void outer() {
        inner(); // 같은 객체 내부 호출 -> 프록시 안 탐 -> AOP 미적용 가능
    }

    @RequirePermission(Permission.ACCIDENT_READ)
    public void inner() { ... }
}

```

  

- AOP/Interceptor는 HTTP 요청만 커버하는 경우가 많음
    - 서비스는 HTTP 말고도 이런식으로 호출함
        - 배치 스케줄러
        - 이벤트 리스너
        - 메시지 컨슈머(kafka/RabbitMQ)
        - 다른 도메인 서비스에서 재사용

-> 서비스는 여러 진입점에서 호출되므로 앞단에서 한 번으로는 보장이 안된다

  

- AOP는 권한 검사를 호출해주는 장치, 이 장치가 누락/우회될 수 있기 때문에 서비스가 권한 정책의 최종 책임자가
- 한번 더 검사한다 X -> 검사가 새지 않게 책임을 분리한다
    - AOP : 편의성(붙이면 자동 체크)
    - Service(AuthorizationService) : 정책의 단일 진실
    - Repository/쿼리 : 데이터 누수 마지막 차단(udpId_스코프)

1. Spring Security -> 들어온 유저가 어떤 권한을 가지고 있는지 준비
2. AOP -> 이 메서드는 ACCIDENT_READ 필요하다고 전달
3. AuthorizationService -> 그 권한이 실제로 이 상황에서 유효하지 판단

- 검사는 1번, 판단 주체는 Service

```Java
[요청 진입]
    |
    v
(Spring Security)
- 인증 상태 확인
- 권한 목록 준비
    |
    v
(AOP / Interceptor)  ← 편의 장치 (있으면 좋음)
- @RequirePermission 읽음
- 권한 검사 "요청" 전달
    |
    v
(AuthorizationService) ← 정책의 단일 진실
- 인증 여부 판단
- 권한 보유 여부 판단
- (필요 시 scope/udpId 판단)
    |
    v
(도메인 Service)
- 비즈니스 로직
```

- 권한 검증을 각 도메인 서비스에 ? 공용 AuthorizationService로 ?
    - 공용 AuthorizationService가 맡아야할 것 (공통 규칙)

1. 인증 상태 확인(익명/비로그인 차단)
2. 권한 보유 여부 확인(Permission key 비교)
3. 스코프 보유 여부 확인(udp_scope로 접근 가능 범위인지)
4. 예시)

```Java
@Service
@RequiredArgsConstructor
public class AuthorizationService {

    private final UserContextProvider userContextProvider; // auth 꺼내기 래핑
    private final UdpScopeRepository udpScopeRepository;

    public void requireAuthenticated() {
        if (userContextProvider.isAnonymous()) {
            throw new AuthenticationCredentialsNotFoundException("로그인이 필요합니다.");
        }
    }

    public void requirePermission(Permission permission) {
        requireAuthenticated();
        if (!userContextProvider.hasAuthority(permission.getKey())) {
            throw new AccessDeniedException("권한이 부족합니다: " + permission.getKey());
        }
    }

    public void requireScope(Long udpId, Long scopeId) {
        requireAuthenticated();
        boolean allowed = udpScopeRepository.existsByUdpIdAndScopeId(udpId, scopeId);
        if (!allowed) {
            throw new AccessDeniedException("해당 범위(scope)에 접근할 수 없습니다.");
        }
    }
}


```

- 도메인 서비스가 맡을 것
    - 도메인별 업무 의미가 들어가는 순간부터는 도메인에 둬야함

```Java
@Service
@RequiredArgsConstructor
public class AccidentService {

    private final AuthorizationService authorizationService;
    private final AccidentRepository accidentRepository;

    public AccidentDetail getAccident(Long udpId, Long accidentId) {
        // 1) 공통 규칙: 권한/스코프
        authorizationService.requirePermission(Permission.ACCIDENT_READ);
        authorizationService.requireScope(udpId, /* scopeId 계산 or 매핑 */ udpId);

        // 2) 도메인 규칙: 작성자 예외 같은 것
        Accident accident = accidentRepository.findByIdAndUdpId(accidentId, udpId)
                .orElseThrow(() -> new NotFoundException("사고가 없습니다."));

        // (예) 작성자면 추가 권한 없이도 허용 같은 정책이 있으면 여기서 판단
        // -> 이건 Accident 엔티티를 알아야 하니까 도메인 서비스에서만 가능

        return AccidentDetail.from(accident);
    }
}


```

### Service 레이어에서 AOP가 적합한 사항

- 트랜잭션 관리 (`@Transactional`): 데이터 무결성을 위해 필수적이며, 이미 서비스에 적용되어 있을 경우
- 로그 (Audit Log): "누가 언제 무엇을 했다"는 업무적 로그를 남길 때.
- 권한 체크 (Method Security): 메서드 실행 전 권한 검사가 필요할 때.

### @Around | @Before

- @Before : 들어가기 전에 막기
- @Around : 감싸서 통제

-> 둘다 AOP지만 책임범위와 확장성이 다르다.


DDD 관점에서 왜 중요한지 기준으로 비교

- @Before 방식

```Java
[Controller / Caller]
        |
        v
 [Service Proxy]
        |
        v
 ┌───────────────────────┐
 │ AOP (@Before)         │
 │ - 어노테이션 읽기     │
 │ - 권한 검사 호출 요청 │
 └─────────┬─────────────┘
           v
 [AuthorizationService]
   - 인증 상태 판단
   - 권한/스코프 판단  ← ★ 정책의 단일 위치
           |
           v
 [Real Service Logic]
           |
           v
        [Return]
```

- 메서드 시작전에 검사하고 끝
- 리턴 값/예외를 조작하지 않음
- 문 앞 경비 역할
- Aspect가 커지지 않음 -> DDD 레이어가 깨지지 않음

  

- @Around 방식 (감싼다)

```Java
[Controller / Caller]
        |
        v
 [Service Proxy]
        |
        v
 ┌────────────────────────────────────┐
 │ AOP (@Around)                      │
 │ - 권한 판단                        │
 │ - scope 조회                       │
 │ - proceed() 호출 여부 결정        │
 │ - 예외 변환                        │
 │ - 리턴값 가공 가능                 │
 └───────────────┬────────────────────┘
                 v
        [Real Service Logic]
                 |
                 v
          [Return / Modified]
```

- 메서드 실행을 감싸서 통제
- 실행 전/후 로직 가능
- 리턴 값 변환, 예와 변환, 성능 로깅, 트랙잭션처럼 "랩핑"처럼 유리
- 정책이 늘어날 수록 Aspect가 커짐

기능 확장 관점

- @Before로 충분한 경우
    
    - 권한 체크
    - 로그인 여부 체크
    - 단순 접근 제어
    

-> 통과 or 거부만 있으면 끝

-> 결과/흐름을 바꿀 필요 없음

```Java
[Method Call]
     |
     v
[AOP @Before]
  - 권한 있나?
  - 없으면 Exception
     |
     v
[Method Execute]
```

  

- @Around 가 필요한 경우(실행 전체 제어)
    - 실행 시간 측정
    - 캐시 적용
    - 재시도/서킷브레이커
    - 분산락
    - 멱등성 보장

-> 메서드 실행을 감싸야만 해결되는 문제일 때

```Java
[Method Call]
     |
     v
[AOP @Around]
  - 실행 전 처리
     |
     v
 proceed() 호출
     |
     v
[Method Execute]
     |
     v
[AOP @Around]
  - 실행 후 처리
  - 결과 가공 / 로깅


```

  

### @Before | @Around 비교 표 (권한 체크 기준)

| 구분            | @Before                          | @Around             |
| ------------- | -------------------------------- | ------------------- |
| 기본 역할         | 메서드 실행 전 검사 트리거                  | 메서드 전체 실행을 감싸서 제어   |
| 개입 범위         | 실행 전 차단 or 통과                    | 실행 전 / 실행 후 / 실행 여부 |
| proceed() 호출  | ❌ 없음                             | ✅ 필수 (호출해야 실행됨)     |
| 리턴값 조작        | ❌ 불가                             | ✅ 가능                |
| 예외 변환/처리      | ❌ 제한적                            | ✅ 가능                |
| Aspect 책임     | 어노테이션 읽기 + 검사 호출                 | 판단 + 흐름 제어까지 가능     |
| 정책 위치(DDD)    | Service (`AuthorizationService`) | Aspect로 흘러가기 쉬움     |
| Aspect 비대화 위험 | 매우 낮음                            | 높음                  |
| 테스트 난이도       | 낮음 (Service 단위 테스트 가능)           | 높음 (AOP 포함 테스트 필요)  |
| 권한 체크 적합성     | ⭐⭐⭐⭐⭐ 매우 적합                      | ⭐⭐ 비추천              |
| 주 사용 사례       | 권한/인증/접근 차단                      | 캐시, 성능 측정, 분산락, 재시도 |
| 확장 시 영향       | 정책은 Service에서 확장                 | Aspect가 점점 커짐       |
| DDD 친화도       | 높음                               | 낮아지기 쉬움             |


### AOP/DDD 기반 권한 관리 모듈화 - 프로젝트 기준 ver.0

- AccidentController rlwns -> 모든 메서드마다 반복되는 try-catch 블록과 응답 객체(BaseResponse)
- Controller의 역할 : 요청을 받아 서비스에 위힘하고 결과를 적절한 HTTP 상태 코드와 포맷으로 클라이언트에 반환
- Service의 역할 : 순수 비즈니스 로직 실행 및 데이터 처리 (HTTP에 대해 알지 못해야함)

  

1. enum 타입 권한 정의

```Java
public enum PermissionKey {
    ACCIDENT_READ,   // 사고 조회
    ACCIDENT_WRITE,  // 사고 입력
    ACCIDENT_DELETE, // 사고 삭제
    // ... 기타 권한
}

```

  

2.  커스텀 어노테이션

- 컨트롤러 메서드 붙인 어노테이션

```Java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface CheckPermission {
    PermissionType[] value();
}
```

3.  권한 Aspect

- 메서드 실행을 가로채서 어노테이션을 읽고, 실제 검증은 도메인 서비스에 위임하는 AOP Aspect

```Java
@Aspect
@Component
@RequiredArgsConstructor
public class PermissionAspect {

    private final PermissionService permissionService; // Domain Service

    @Before("@annotation(checkPermission)")
    public void validatePermission(JoinPoint joinPoint, CheckPermission checkPermission) {
        Long currentUserId = SecurityContextHolder.getContext()...; 
        
        for (PermissionType permission : checkPermission.value()) {
            if (!permissionService.hasPermission(currentUserId, permission)) {
                throw new AccessDeniedException("권한이 없습니다: " + permission);
            }
        }
    }
}
```

- DDD 관점 정렬
    - Controller : 어떤 권한이 필요한지만 선언
    - AOP Aspect : 문지기 역할, 횡단 관심사를 분리
    - PermissionService : 사용자에게 실제로 그 권한이 있는지 판단하는 로직을 담당

  

- Service에서 권한을 판단하는 로직이 왜 필요한가?

1. 비즈니스 로직의 응집도 (DDD 원칙)
    1. Aspect는 어노테이션이 붙어있으면 검사해라 라는 기술적인 흐름만 담당해야한다
    2. 이 유저가 왜 이 권한을 가졌는가를 판단하는 건 순수한 비즈니스 규칙
    3. 유저가 휴직상태면 권한이 있어도 접근 불가 같은 규칙 추가 -> 도메인 서비스가 되어버림
2. 재사용성과 중복 제거
    1. 다른 서비스 로직 안에서도 권한 체크가 필요할 수 있다
    2. 예를 들어 사고를 자동 배정하는 스케줄러가 돌 때, 특정 매니저가 권한이 있는지 체크해야 한다면
    3. Aspect는 호출할 수 없지만,서비스는 어디서든 호출할 수 있다.
3. 테스트 용이성
    1. AOP를 테스트하려면 스프링 컨텍스트를 띄우고 mocking을 해야 해서 복잡함
    2. PermissionService는 순수한 자바 클래스, 단위 테스트로 비즈니스 로직만 빠르고 정확하게 검증하기 쉽다.

  

- 흐름 확인

```Java
User (API 요청: 사고 목록 조회) ⬇️

AOP (Aspect)
중간에 딱 가로채서 "어? @CheckPermission(ACCIDENT_READ)가 붙어있네?" 확인

⬇
️
PermissionService "이 유저(ID)가 ACCIDENT_READ 권한이 있는지 DB에서 확인해 줘" 요청

⬇
️
AOP결과 수신: 확인됨(True) → 통과! ✅ (원래 메서드 실행)

결과 수신: 없음(False) → 차단! 🚫 (예외 발생)

⬇

Controller -> Service (Business Logic)

  -> 권한이 확인된 후, 그제서야 비즈니스 로직 실행


```

- 프로젝트 구조

```Java
src/main/java/com/kikii/kikib
├── common   <-- [NEW] 공통 관심사를 모아두는 곳
│   └── permission
│       ├── annotation
│       │   └── RequirePermission.java      (어노테이션)
│       ├── aspect
│       │   └── PermissionAspect.java     (AOP 로직: 검문소)
│       ├── service
│       │   ├── PermissionService.java    (인터페이스)
│       │   └── PermissionServiceImpl.java(구현체: 법전)
│       └── PermissionKey.java           (권한 목록 Enum)
│
├── config
│   └── SecurityConfig.java               (기존 시큐리티 설정)
│
├── controller
│   └── AccidentController.java           (적용 대상)
│
└── domain   <-- [Existing] 도메인 영역
    └── role
        └── infrastructure...             (실제 권한 데이터 조회)
```

> 각 파일의 역할 요약
- common/permission/PermissionKey.java
	- 시스템에 어떤 권한들이 있는지 
	- ACCIDENT_READ, USER_WRITE 등 DB 코드와 1:1 매핑 되는 Enum

- common/permission/aspect/RequirePermission.java
	- 이 메서드는 권한 체크가 필요하다고 알림 
	- 컨트롤러 위에 @RequirePermission(...)
	
- common/permission/aspect/PermissionAspect.java
	- 표지판 보이면 일단 멈춰세워서 검사
	- 로직 : 요청 가로채기 -> 유저ID 꺼내기 -> Service에 물어보기 -> 통과/거절 
	
- common/permission/service/PermissionService.java**
    - 이 사람이 권한을 가지고 있는지 판별
    - **로직**: DB(GroupFunctionRepository)를 조회해서 실제 유효한 권한인지 판단.

  

- Before | Around

1. Before

- 동작 : 핵심 로직이 실행되기 직전에 딱 한 번 실행
- 용도 : 입장권 확인, 유효성 검사, 로깅(시작 시점) 등
- 특징
   - 구현이 매우 단순 
   - Exception을 던지면 핵심 로직 실행을 막을 수 있어 권한 체크 용도에 맞음
   - 핵심 로직의 리턴값이나 실행 과정 자체에는 개입할 수 없음 

```Java
@Before("@annotation(checkPermission)")

public void check() {

if (!hasPermission) {

throw new AccessDeniedException("멈춰!"); // 여기서 예외 터지면 뒤에 컨트롤러는 실행 안 됨

}

// 별도의 return이나 proceed() 호출 필요 없음. 그냥 끝나면 컨트롤러로 넘어감.

}
```

2. @Around
- 동작 : 핵심 로직의 실행 전과 후를 모두 감싸서 제어
- 용도 : ":실행 시간 측정", "트랜잭션 관리", "캐싱(결과 조작)", "재시도 로직" 등
- 특징 
   - 가장 강력한만큼 책임이 무거움 
   - 개발자가 직접 joinPoint.proceed()를 호출해줘야 핵심 로직이 실행(호출 안하면 API 먹통)
   - 리턴값을 변경하거나 생략할 수도 있음


> **권한 체크를 한다면 @Before가 적합하다**
- 권한 체크는 자격 없으면 막고, 있으면 통과라는 단순한 로직, API 실행 결과값을 조작하거나 실행 시간을 잴 필요가 없기 때문에, 굳이 더 복잡하고 실수할 여지가 있는 @Around를 사용하기에는 적합하지 않을 수도 있다.

### AOP/DDD 기반 권한 관리 모듈화 - 프로젝트 기준(new) ver.1

- AccidentController rlwns -> 모든 메서드마다 반복되는 try-catch 블록과 응답 객체(BaseResponse)
- Controller의 역할 : 요청을 받아 서비스에 위힘하고 결과를 적절한 HTTP 상태 코드와 포맷으로 클라이언트에 반환
- Service의 역할 : 순수 비즈니스 로직 실행 및 데이터 처리 (HTTP에 대해 알지 못해야함)

  

1. enum 타입 권한 정의

```Java
public enum PermissionKey {
    ACCIDENT_READ,   // 사고 조회
    ACCIDENT_WRITE,  // 사고 입력
    ACCIDENT_DELETE, // 사고 삭제
    // ... 기타 권한
}

```

  

2.  커스텀 어노테이션

- 컨트롤러 메서드 붙인 어노테이션

```Java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RequirePermission {
    PermissionType[] value();
}
```

3.  권한 Aspect

- AOP(Aspect) 에서 SecurityContext를 직접 조회하여 권한을 판단하는 로직으로 변경

```Java
@Aspect

@Component

@RequiredArgsConstructor

public class PermissionAspect {

@Before("requirePermission")

public void validatePermission(JoinPoint joinPoint, RequirePermission requirePermission) {

// 1. 필요한 권한 가져오기

PermissionKey requiredPermission = requirePermission.value();

// 2. 현재 로그인한 사용자의 권한(Authorities) 조회

Authentication auth = SecurityContextHolder.getContext().getAuthentication();

if (auth == null || !auth.isAuthenticated()) {

throw new AccessDeniedException("로그인이 필요합니다.");

}

// 3. 권한 매칭 (SecurityContext에 있는 권한과 비교)

boolean hasPermission = auth.getAuthorities().stream()

.anyMatch(grantedAuthority -> grantedAuthority.getAuthority().equals(requiredPermission.getKey()));

// 4. 없으면 예외 발생

if (!hasPermission) {

throw new AccessDeniedException("권한이 없습니다: " + requiredPermission.getKey());

}

}

}
```



- 흐름 확인

1. **🙋‍♂️ 사용자 (User)**
    
    - API 요청을 보냅니다. (예: `GET /accident/read/lists`) ⬇️
2. **🚧 AOP 가로채기 (AuthorizationAspect)**
    
    - 컨트롤러 메서드 실행 직전에 딱! 멈춰 세웁니다. ⬇️
3. **👀 어노테이션 확인**
    
    - "이 메서드에 `@RequirePermission(ACCIDENT_READ)` 표지판이 붙어있나?" 확인합니다. ⬇️
4. **👮‍♂️ 신분증 확인 (SecurityContextHolder)**
    
    - "현재 로그인한 사람의 권한 목록(Authorities)을 다 꺼내봐!"
    - DB에 갈 필요 없이 시큐리티 컨텍스트(메모리)에서 바로 가져옵니다. ⬇️
5. **⚖️ 판결 (Validation)**
    
    - **내 권한 목록**에 `ACCIDENT_READ`가 있는가? ⬇️
6. **🚦 결과 실행**
    
    - ✅ **있음 (YES)**: "통과!" 👉 **컨트롤러 메서드 실행** (비즈니스 로직 수행)
    - 🚫 **없음 (NO)**: "멈춰!" 👉 **`AccessDeniedException` 예외 발생** (403 Forbidden 응답)


> 프로젝트 구조 

```Java
src/main/java/com/kikii/kikib

├── common <-- [NEW] 공통 관심사

│ └── permission

│ ├── annotation

│ │ └── RequirePermission.java (어노테이션)

│ ├── aspect

│ │ └── PermissionAspect.java (AOP 로직: 검문소)

│ └── PermissionKey.java (권한 목록 Enum)

│

├── config

│ └── SecurityConfig.java (기존 시큐리티 설정)

│

└── domain

└── role ... (DB 데이터는 로그인 시 시큐리티 컨텍스트에 로딩됨)
```


- Before | Around

1. Before

- 동작 : 핵심 로직이 실행되기 직전에 딱 한 번 실행
- 용도 : 입장권 확인, 유효성 검사, 로깅(시작 시점) 등
- 특징
   - 구현이 매우 단순 
   - Exception을 던지면 핵심 로직 실행을 막을 수 있어 권한 체크 용도에 맞음
   - 핵심 로직의 리턴값이나 실행 과정 자체에는 개입할 수 없음 

```Java
@Before("@annotation(checkPermission)")

public void check() {

if (!hasPermission) {

throw new AccessDeniedException("멈춰!"); // 여기서 예외 터지면 뒤에 컨트롤러는 실행 안 됨

}

// 별도의 return이나 proceed() 호출 필요 없음. 그냥 끝나면 컨트롤러로 넘어감.

}
```

2. @Around
- 동작 : 핵심 로직의 실행 전과 후를 모두 감싸서 제어
- 용도 : ":실행 시간 측정", "트랜잭션 관리", "캐싱(결과 조작)", "재시도 로직" 등
- 특징 
   - 가장 강력한만큼 책임이 무거움 
   - 개발자가 직접 joinPoint.proceed()를 호출해줘야 핵심 로직이 실행(호출 안하면 API 먹통)
   - 리턴값을 변경하거나 생략할 수도 있음


> **권한 체크를 한다면 @Before가 적합하다**
- 권한 체크는 자격 없으면 막고, 있으면 통과라는 단순한 로직, API 실행 결과값을 조작하거나 실행 시간을 잴 필요가 없기 때문에, 굳이 더 복잡하고 실수할 여지가 있는 @Around를 사용하기에는 적합하지 않을 수도 있다.
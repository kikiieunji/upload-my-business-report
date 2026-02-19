#### **1. API 요청 (Controller)**

- **클라이언트**: `/settlement/targets?companyId=5&jobTypeId=6(임원직)` API를 호출합니다.
- **어노테이션**: `@ScopeCheck(companyId="companyId", jobTypeId="jobTypeId")`가 붙어 있어 AOP가 발동됩니다.

#### **2. AOP 개입 (ScopeCheckAspect)**

- **파라미터 추출**:
  build.gradle의 `-parameters` 옵션 덕분에, AOP는 메서드 파라미터 이름("companyId")을 정확히 인식함
  ScopeCheckParser는 이름 기반으로 파라미터 값(`5`, `6`)을 추출합니다. (SpEL 제거로 단순화됨)
- **검증 요청**: 추출된 `companyId(5)`와 `jobTypeId(6)`를 ScopeChecker에게 넘김

#### **3. 권한 검증 (ScopeChecker)**

- **Target 확인**:
  - "조회하려는 직군(jobTypeId=6)이 뭐지?" -> **`EXECUTIVE` (임원직)**
  - "임원직이 아니면 통과, 임원직이면 검사" -> **검사 필요**
- **내 권한 조회 (Query)**:
  - "사용자(`userId`)가 5번 회사(`companyId`)에서 가진 **모든 직책(UDP)**을 뒤져보자."
  - "그 직책들에 부여된 권한(`user_scope`) 목록을 다 가져와!"
  - **최적화된 쿼리**: `Hibernate`가 User 테이블 조인 없이 `UDP` 테이블의 FK만으로 빠르게 조회
- **판결**:
  - "내 권한 목록에 `EXECUTIVE`가 있는가?"
  - **YES**: 통과 (Return)
  - **NO**: 차단 (`IllegalArgumentException` 발생 -> 400/403 응답)

#### **4. 비즈니스 로직 (SettlementService)**

- 검증을 통과했다면 급여 로직이 실행
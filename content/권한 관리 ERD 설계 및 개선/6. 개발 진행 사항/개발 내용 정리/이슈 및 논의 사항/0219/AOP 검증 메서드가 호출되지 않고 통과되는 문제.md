
1. 임원직 정보에 대한 접근을 제한하고자 @ScopeCheck 어노테이션 + AOP 검증 로직 도입 
2. @ScopeCheck 어노테이션은 호출되었으나 검증하지 않고 통과 되어 임원직을 볼 수 없는 일반 관리자가 임원직 정보를 그대로 볼 수 있는 문제가 발생함 
3. ScopeChecker 로직 점검 필요 

#### Issue 1 : AOP 로직 실행 X 
- 현상 : @ScopeCheck(companyId = "company_id") 어노테이션을 붙였으나, 검증 메서드가 호출 되지 않고 통과죔 
- 원인 
	- Spring AOP가 메서드 파라미터 이름을 해석할 때, 자바 컴파일러 최적화로 인해 파라미터 이름(companyId)이 arg0, arg1 등으로 변환
	- ScopeCheckParser가 **companyId** 를 찾지 못해 null을 반환 -> Aspect는 값이 없다고 인식하여 검증을 건너뜀 
- 해결 
	- **build.gradle 에 옵션 추가** 
		- 컴파일 시 파라미터 이름을 보존하여 AOP가 변수명을 정확히 인식하게 조치
		- why? -> 자바 컴파일러는 기본적으로 메서드 파라미터의 이름을 저장하지 않음(최적화를 위해 불필요한 메타데이터 제거)
			- 옵션을 적용하게 되면 :: 파라미터 이름을 지우지 말고 .class 파일에 남겨두라고 지시
			- companyId라는 이름이 살아있게 됨
		```JAVA
		// 파라미터명 보존
		tasks.withType(JavaCompile) {
		
		options.compilerArgs << "-parameters"
		```

#### Issue 2 : Parser 로직의 복잡성
- 현상 : ScopeCheckParser가 SpEL 을 사용하여 파싱을 시도
- 해결 : 단순 변수 조회 방식으로 리팩토링하여 코드 복잡도를 낮추고 성능 개선

#### Issue 3 : 데이터 접근 권한 검증 로직 미흡 
- 현상 : 사용자의 권한을 확인할 때 userId 기반으로 모든 회사의 권한을 조회하여 A 회사 임원 권한이 B회사 조회 시에도 적용될 위험 
- 해결 : userId + companyId 조합으로 해당 회사의 직책에 부여된 권한만을 조회할 수 있도록 변경
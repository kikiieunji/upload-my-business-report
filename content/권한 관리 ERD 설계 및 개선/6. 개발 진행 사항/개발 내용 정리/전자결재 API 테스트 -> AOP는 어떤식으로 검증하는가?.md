
[전자결재 양식 목록 조회]

	1. 컨트롤러가 실행되기 전 AOP가 가로채서 검증을 진행한다 
		- readApprovalTemplateList 메서드에 붙은 @RequirePermission을 감지하고 검증 시작 
	2. Extracted ID : Null 확인 
		- 템플릿 양식 목록 조회 일경우 -> TemplateId 가 넘어오지 않으므로 Null
	3. ID is null, verifying basic role check 
		 - 특정 양식에 대한 권한 대신 사용자가 role 권한을 가지고 있는지 체크 
	4. Basic role check passed
		- role 권한이 확인되어 검증을 통과하고 controller 실행 


[전자결재 양식 상세 조회]

	1. 컨트롤러가 실행되기 전 AOP가 가로채서 검증을 진행한다 
		- readApprovalTemplateList 메서드에 붙은 @RequirePermission을 감지하고 검증 시작 
	2. Extracted ID : 5 (예시)
		- requirepermission 파라미터 설정을 통해 ID : 5를 추출함 
	3. Target Template ID : 5
		- 검사 대상 템플릿 ID 확정 
	4. **User Role** Ids : [1]
		- 해당 사용자가 ACCIDENT_EDITOR 를 가지고 있는지 확인 
	5. Access Granted.
		- 5번 템플릿 -> DB 조회 -> 5번 템플릿은 Role 1번에게 권한이 있다 확인 
	6. 해당 사용자에게 Role이 있으므로 접근을 허용하고 템플릿을 출력
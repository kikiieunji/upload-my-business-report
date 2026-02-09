
1) @RequirePermission API 호출 
	- AOP가 체크 퍼미션 진입

2) 사용자 로그인 ID 확인 
	- SecurityContext에서 userId 추출 

3) 사용자 roleId 수집 
	- 사용자 udp들 조회 
	- udp별 udp_role 조회 
	- 최종 role_id 리스트를 생성
	- role이 없을 경우 -> AccessDeniedException 발생 

4) 기능 권한 검증 (CheckFunctionPermission)
	- 어노테이션의 permission을 키 리스트로 만듬
	- MatchType == ANY일 경우
		- existsAnyByRoleIdAndPermissionKey(RoleIds, keys) 실행
			- 사용자 role 중 하나라도 요청된 권한 키와 매칭되면 통과
	- MatchType == ALL일 경우
		- CountDistinctByRoleIdsAndPermissionKeu(RoleIds, keys) 실행 
			- 요청한 권한 키 개수와 일치해야 통과 
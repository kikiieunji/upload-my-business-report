
---
- ### ScopeJpaRepository 변경 사항
	-  \*\*변경 전\*\*
		```SQL
		SELECT ...
		FROM user_scope us
		
		JOIN user_department_position udp ON ...
		
		JOIN user u ON udp.user_id = u.id -- 굳이 조인함
		
		WHERE u.id = ?
		```
	- 명시적으로 join udp.user u 를 적는 경우 하이버네이트는 user 테이블의 필드를 쓴다고 생각하고 무조건 INNER JOIN user 를 날림
	
	
	
		- \*\*변경 후\*\*
			```SQL
			SELECT ... 
			FROM user_scope us 
			JOIN user_department_position udp ON ...
			-- User 테이블 조인 없이 바로 비교
			WHERE udp.user_id = ?
			```
	
	- 하이버네이트는 조회/비교하려는 속성이 연관관계의 주인(FK)에 이미 있는지를 확인
	- userDepartmentPosition 테이블에 user_id컬럼이 있으니 User 테이블을 조인해서 id를 확인할 필요가 없다는 것을 알고 쿼리를 최적화 해줌
	- \*\*udp.user_id라고 쓰면\*\*
		1. 사용자가 user.id(PK)만 비교하려고 함 
		2. UserDepartmentPosition 테이블에서 user_id(FK) 컬럼 확인 
		3. user 테이블 조인 필요없이 udp_id 확인 가능 -> join 생략
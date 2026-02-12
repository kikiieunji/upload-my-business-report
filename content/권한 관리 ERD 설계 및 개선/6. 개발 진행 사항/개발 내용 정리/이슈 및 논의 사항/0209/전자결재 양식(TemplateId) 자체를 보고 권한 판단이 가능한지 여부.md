
---
> 구현 계획 : 전자결재를 위한 AOP 권한 부여
1. TemplateId를 기반으로 템플릿이 요구하는 Role 식별
2. 현재 로그인한 사용자 -> udp_role 테이블을 통해 해당 role을 보유하고 있는지 검증 
---
> **주의할 점**
- 현재 ApprovalTemplateAuthority 테이블 -> GroupFunction과 연결되어 있는데 GroupFunction 대신 Role 테이블을 참조할 수 있도록 스키마 변경이 필요 
	- ApprovalTemplateAuthority 엔티티에 role 필드 변경

#중요 
 - 기존에 GroupFunction으로 설정된 템플릿 권한 데이터들을 Role로 변환하여 저장해야한다.

---
> **가능 여부 판단**
- 가능 여부 : 가능함 
- 이유 :
	 - domain/permission 패키지에 UdpRole, Role, UserDepartmentPosition 등 필요 엔ㄷ티티와 리포지토리가 갖추어져 있으며 
	 - UdpRole을 통해 사용자와 Role간의 연결이 명확하여 GroupFunction 없이도 직관적인 권한 관리가 가능해짐 
	 - 대신, ApprovalTemplateAuthority 테이블의 컬럼 변경(Group_function Id -> Role Id)로 먼저 선행되어야 구현 가능
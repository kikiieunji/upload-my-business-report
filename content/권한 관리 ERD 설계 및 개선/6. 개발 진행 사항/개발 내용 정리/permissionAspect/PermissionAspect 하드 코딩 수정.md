1) **제안 방향**
  - RequirePermission을 범용화하여 기존 tragettype과 isparamName 유지 및 기존 API 또한 깨지지 않게 유지
  - PermissionAspect 2단계로 체크 분리
    - 1단계 : 사용자 Role 기반 permission 검사
    - 2단계 : 전자 결재 양식 템플릿에 접근하는 경우(필요할때만) 기존 전자결재 양식 템플릿 리소스 검사

2) **PermissionKey enum -> 상수 클래스로 변경**
  - enum과 상수 둘다 이름을 정해둔 권한 이름표 :: 차이는 검사방식
  - enum
    - 등록된 키만 인식
    - 오타가 있으면 컴파일 시 에러
    - 새 권한 추가 시 수정/배포 필요
  - 상수 클래스
    - 수기 명단처럼 유연함
    - 문자열로 바로 비교가 가능하여 DB값과 맞추기 쉬움
    - 철자 실수 시 컴파일러가 못잡음
  - DB와 쉽게 맞출 수 있다는 점에서 상수클래스로 변경

3) **target_type = NONE 추가**
  - NONE 타입 없음 -> 리소스 체크를 끄기 위해 isparamName = "" 우회 필요
  - 리소스 권한체크가 필요없는 API임을 명시하기 위하여 추가(TmplateId 확인을 안하는 목록 조회)

4) **ANY/ALL 쿼리문(PermissionMatchType)** 
  - **ANY** : 요청 권한 중 하나라도 있으면 통과
    - roleIds(권한) 과 key(요청 권한들) 교집합이 한개라도 있으면 true
    - 예시) ACCIDENT_VIEWER = ACCIDENT_READ 권한 보유
    - ACCIDENT_EDITOR = ACCIDNET_READ/WRITE 권한 보유
    - 요청 권한 = ACCIDENT_READ 일 경우 VIEWER/EDITOR 모두 통과
```java
    @Query("SELECT COUNT(rp) > 0 " +
    "FROM RolePermission rp " +
    "WHERE rp.roleId.id IN :roleIds " +
    "AND rp.permissionKey.keyName IN :keys")
    boolean existsAnyByRoleIdsAndPermissionKeys(@Param("roleIds") List<Long> roleIds,
    @Param("keys") List<String> keys);
```

  - **ALL** : 요청 권한을 전부 가지고 있으면 통과
    - 사용자가 가진 권한 중 요청 목록이 해당하는 권한 개수를 체크
    - 요청 권한 = ACCIDENT_READ/WRITE 권한 둘다 필요 EDITOR만 통과
```java
    @Query("SELECT COUNT(DISTINCT rp.permissionKey.keyName) " +
    "FROM RolePermission rp " +
    "WHERE rp.roleId.id IN :roleIds " +
    "AND rp.permissionKey.keyName IN :keys")
    long countDistinctByRoleIdsAndPermissionKeys(@Param("roleIds") List<Long> roleIds,
    @Param("keys") List<String> keys);
```
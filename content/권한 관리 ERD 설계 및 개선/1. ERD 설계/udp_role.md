- 역할/권한을 가지고 있는지 확인하기 위한 user 정보 테이블
- = `udp_group_function` 참고

|컬럼명|타입|설명|
|---|---|---|
|`id`|bigint(PK)|고유 식별자|
|`udp_id`|bigint(FK)|udp id|
|`role_id`|bigint(FK)|user에게 role 부여|
|`created_at`|timestamp|생성 일시|

  

|id|udp_id|role_id|created_at|
|---|---|---|---|
|1|1 (김띵똥)|1 (ACCIDENT_EDITOR)|2026-01-22 09:49:00|
|2|2 (김띵똥)|2 (ACCIDENT_VIEWER)|2026-01-22 09:49:00|

- UDP 1번은 사고 관리자 역할을 가진다
- user_id가 같더라도 udp_id에서 직군이 다르다면 권한을 다르게 부여할 수도 있다
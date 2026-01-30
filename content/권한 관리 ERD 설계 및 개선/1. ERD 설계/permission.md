- 권한 관리 테이블

|컬럼명|타입|설명|
|---|---|---|
|`id`|bigint(PK)|고유 식별자|
|`key_name`|varchar(50)|권한 키(Enum)<br><br>PAYROLL_READ<br><br>PAYROLL_WRITE<br><br>ACCIDENT_READ<br><br>ACCIDENT_WRITE|
|description|varchar(50)|사고 관리 조회 \| 사고 관리 등록 \| ...|
|`created_at`|timestamp|생성 일시|

[예시]

|id|key_name|created_at|
|---|---|---|
|1|ACCIDENT_READ|2026-01-22 09:49:00|
|2|ACCIDENT_WRITE|2026-01-22 09:49:00|
|3|PAYROLL_READ|2026-01-22 09:49:00|
|4|PAYROLL_WRITE|2026-01-22 09:49:00|

-> key 자체를 String으로 생각

- permission 구조 PK를 String으로 가져가려는 이유
- write / read 상하 관계를 줄지 or write, read를 각자 줄지
- 유비쿼터스 랭귀지 : DDD 설계시 공통적으로 봤을 때 이해할 수 있는 단어
- enum 타입이기 때문에 key 자체를 string 으로 가져가는게 적적할 수 있음
    - PK를 bitint로 했을 때 > 이걸 한번 더 걸러서 뭔지 확인해야하는 번거로움이 있을 수 있음
- 단순 로그 기록용, 선택 사항

|컬럼명|타입|설명|
|---|---|---|
|`id`|bigint(PK)|고유 식별자|
|`endpoint`|varchar(100)|/api/payments|
|`method`|varchar(10)|GET \| POST \| PATCH \| ...|
|`mapped_permission_key`|bigint(FK)|PAYMENT_READ (단순 기록용)|
|`created_at`|timestamp|생성 일시|
|`updated_at`|timestamp|갱신 일시|
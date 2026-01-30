| 컬럼명           | 타입           | 설명                                                                       |
| ------------- | ------------ | ------------------------------------------------------------------------ |
| `id`          | bigint(PK)   | 고유 식별자                                                                   |
| `code`        | varchar(50)  | ACCIDENT_EDITOR / ACCIDENT_VIEWER<br><br>PAYROLL_EDITOR / PAYROLL_VIEWER |
| `name`        | varchar(100) | 사고 총괄 관리자, 사고 조회 전용<br><br>급여 총괄 관리자, 급여 조회 전용                           |
| `description` | varchar(255) | 설명                                                                       |
| `created_at`  | time_stamp   | 생성 일시                                                                    |

[테이블 예시]

|id|code|name|description|created_at|
|---|---|---|---|---|
|1|ACCIDENT_EDITOR|사고 총괄 관리자|사고를 관리하는 사람|2026-01-22 09:49:00|
|2|ACCIDENT_VIEWER|사고 조회 전용|사고 조회 가능한 사람|2026-01-22 09:49:00|
|3|PAYROLL_EDITOR|급여 총괄 관리자|급여를 관리하는 사람|2026-01-22 09:49:00|
|4|PAYROLL_VIEWER|급여 조회 전용|급여 조회 가능한 사람|2026-01-22 09:49:00|
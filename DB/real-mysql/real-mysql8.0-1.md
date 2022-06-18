# 개요

- `Real MySQL 8.0 1권(개정판)`을 읽고 정리합니다

- [개요](#개요)
  - [MySQL 격리 수준](#mysql-격리-수준)

## MySQL 격리 수준

- `READ UNCOMMITTED`
  - 각 트랜잭션의 COMMIT, ROLLBACK에 상관 없이 해당 트랙잭션의 변경 내용에 대해서 SELECT 함(Dirty read 가능)
  - Dirty read를 유발하는 READ UNNCOMMITTED 격리 수준은 정합성에 문제가 많고 RDBMS 표준에서 트랜잭션 격리 수준으로 인정하지 않을 정도이다
- `READ COMMITTED`
  - 온라인 서비스에서 가장 많이 선택되는 격리 수준
  - Dirty read가 발생하지 않음 -> 무조건 COMMIT된 데이터를 조회
  - `NON-REPEATABLE READ`라는 부정합 존재 -> 트랜잭션이 COMMIT 되어야만 해당 데이터를 정확하게 조회할 수 있으므로 다른 하나의 트랜잭션 내에서 똑같은 SELECT를 했을 때 다른 데이터를 조회할 수 있음(REAPEATABLE READ 시 같은 데이터를 보장하지 않음)
  - 금전적인 처리와 같이 데이터 정합성이 중요한 경우 해당 격리 수준보다 높은 격리 수준을 사용
- `REPEATABLE READ`
  - MySQL InnoDB 스토리지 엔진에서 기본으로 사용하는 격리 수준
  - 바이너리 로그를 가진 MySQL 서버에서는 REPEATABLE READ 격리 수준 이상을 사용해야함
  - READ COMMITED에서 발생하는 `NON-REPEATABLE READ` 부정합이 발생하지 않음
  - 언두 로그를 이용해 REPEATABLE READ를 보장한다 -> 한 트랜잭션에서 같은 데이터 조회 시 다른 트랜잭션이 해당 데이터를 수정하더라도 언두 로그에 존재하는 데이터를 조회하게 됨
  - `PHANTOM READ` 부정합 발생
    - 다른 트랜잭션에서 데이터 수정 발생 시 똑같은 READ에서도 레코드가 보였다 안 보였다 하는 현상으로 `SELECT ... FOR UPDATE` 쿼리에서 발생 가능
    - `FOR UPDATE`, `LOCK IN SHARE MODE` 쿼리는 레코드에 쓰기 잠금을 걸어야하지만, 언두 로그에는 잠금을 걸 수 없으므로 언두 영역의 변경 전 데이터를 가져오는 게 아니라 현재 레코드의 값을 가져오게 됨
- `SERIALIZABLE`
  - 가장 단순하면서 가장 엄격한 격리 수준
  - 동시 처리 성능이 다른 격리 수준보다 떨어짐
  - PHANTOM READ 발생 X
  - InnoDB 스토리지 엔진에서는 갭 락과 넥스트 키 락 덕분에 REPEATABLE READ 격리 수준에서도 이미 PHANTOM READ가 발생하지 않아 FOR UPDATE, LOCK IN SHARE MODE의 경우를 제외하고는 굳이 SERIALIZABLE을 사용할 필요성은 없음

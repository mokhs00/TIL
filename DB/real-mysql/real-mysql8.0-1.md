# 개요

- `Real MySQL 8.0 1권(개정판)`을 읽고 정리합니다

- [개요](#개요)
  - [MySQL 격리 수준](#mysql-격리-수준)
  - [DB 성능에 영향을 주는 요소](#db-성능에-영향을-주는-요소)
  - [인덱스](#인덱스)
    - [인덱스 레인지 스캔](#인덱스-레인지-스캔)
    - [인덱스 풀 스캔](#인덱스-풀-스캔)
    - [루스 인덱스 스캔 & 인덱스 스킵 스캔](#루스-인덱스-스캔--인덱스-스킵-스캔)
    - [다중 컬럼 인덱스](#다중-컬럼-인덱스)
  - [MySQL 쿼리 실행 절차](#mysql-쿼리-실행-절차)
  - [커버링 인덱스](#커버링-인덱스)
  - [테이블 명 영문 대소문자 구분](#테이블-명-영문-대소문자-구분)

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

## DB 성능에 영향을 주는 요소

- DB 성능과 관련하여 디스크 I/O가 대부분의 비율을 차지하며 대부분은 DBMS는 디스크 I/O의 빈도를 줄여 성능을 높인다
- 인덱스 활용, 버퍼를 이용해 메모리에 미리 데이터를 올려두는 등 디스크 I/O를 줄이는 방법 등이 존재함

## 인덱스

- 해당 내용은 B-Tree 인덱스 기준으로 작성한다
- **인덱스를 사용할 수 없는 경우**
  - B-Tree 인덱스를 이용한 검색은 `100% 일치` 또는 `앞부분(Left-most part)만 일치`하는 경우에 사용할 수 있다
  - 부등호(`<`, `>`) 비교 조건에서도 인덱스를 활용 가능
  - `IS Null`도 인덱스 활용 가능 MySQL에서는 Null도 인덱스에 저장됨
  - 인덱스의 키 값에 변형이 가해진 후 비교되는 경우는 사용 불가능 e.g. 함수나 연산을 수행한 결과로 정렬한다거나 검색하는 작업
  - 앞부분 일치 조건이 아닌 경우 e.g. `LIKE '%??'` 
  - NON-EQUAL로 비교된 경우(`<>`, `NOT IN`, `NOT BETWEEN`, `IS NOT NULL`)
  - 데이터 타입, 콜레이션(인코딩)이 다른 경우 (인덱스 칼럼의 타입을 반환해야 비교가 가능한 경우)
  - *InnoDB 테이블에서 지원하는 레코드 잠금이나 넥스트 키락(갭락)은 검색을 수행한 인덱스를 잠근 후 테이블의 레코드를 잠그는 방식으로 구현되어 있음
    - -> UPDATE, DELETE 문장이 실행될 때 테이블에 적절히 사용할 수 있는 인덱스가 없으면 불필요하게 많은 레코드를 잠금. 심지어 테이블의 모든 레코드를 잠글 수도 있음
    - InnoDB 스토리지 엔진에서 인덱스 설계가 중요하고 많은 부분에 영향을 미친다는 걸 의미하니 주의하자
- **인덱스 성능**
  - 인덱스를 구성하는 칼럼의 크기와 레코드의 건수, 유니크한 인덱스 키 값의 개수 등이 검색이나 변경 작업에 영향을 줌
  - 그리고 B-Tree의 탐색 성능에 비례하게 된다
  - B-Tree의 탐색 성능과 연관된 것을 나열해보면 다음과 같다
    - `B-Tree 깊이`: 상당히 중요하긴 하지만 직접 제어할 방법은 없음. 보통 5 Depth 이상까지 깊어지는 경우는 흔치 않음
    - `선택도(Selectivity)` or `기수성(Cardinality)`: 선택도와 기수성은 거의 같은 의미로 사용되며, 인덱스 키 값 중 유니크한 값의 수를 의미 -> 선택도가 높을 수록 검색 대상이 줄어듬
- **옵티마이저가 인덱스를 사용하지 않을 수도..?**
  - 일반적인 DBMS의 옵티마이저에서는 인덱스를 통해 레코드 1건을 읽는 것이 테이블에서 직접 레코드 1건을 읽는 것보다 4~5배 정도의 비용이 더 많이 드는 작업인 것으로 예측
  -> 인덱스의 리프 노드에서 검색 조건에 일치하는 레코드 한 건 당 랜덤 I/O가 한 번씩 일어나게 되기 때문
  - 이에 따라 옵티마이저가 판단한 인덱스를 통해 읽어야할 레코드의 예상 건수가 전체 테이블 레코드의 20~25%(손익분기점)를 넘어선다면 인덱스를 이용하지 않는다
  - 강제로 인덱스를 사용하도록 힌트를 추가해도 옵티마이저가 힌트를 무시하고 테이블을 직접 읽는 방식으로 처리하기에 성능상 얻을 수 있는 이점이 없음
  - 위 같이 읽어야할 레코드 수가 전체 테이블 레코드의 20~25%를 넘어서면 인덱스를 이용하지 않고 테이블을 모두 직접 읽어서 필요한 레코드만 필터링하는 방식이 좀 더 효율적

### 인덱스 레인지 스캔

- 검색해야할 인덱스의 범위가 결정되었을 때 사용하는 방식
- 검색하려는 값의 수나 검색 결과 레코드 건수와 상관없이 레인지 스캔이라고 표현함
- 보통 인덱스 레인지 스캔이 일어나는 3단계는 다음과 같음
  1. **인덱스 탐색(index seek)**: 인덱스에서 조건을 만족하는 값이 저장된 위치를 찾음
  2. **인덱스 스캔(index scan)**: 1번에서 탐색한 위치부터 필요한 만큼 인덱스를 차례로 읽음
  3. 2번에서 읽어온 인덱스 키와 레코드 주솔르 이용해 레코드가 저장된 페이지를 가져오고, 최종 레코드를 읽어 옴
- 위 3단계에서 쿼리가 필요로하는 데이터에 따라 3번 과정은 `커버링 인덱스`를 이용하면 필요하지 않을 수 있음
- `커버링 인덱스`로 처리되는 쿼리는 디스크의 레코드를 읽지 않아도 되기 때문에 랜덤 읽기가 상당히 줄어들고 성능이 향상됨
  
### 인덱스 풀 스캔

- 인덱스를 사용하지만 인덱스의 인덱스 레인지 스캔과 달리 처음부터 끝까지 읽는 방식
- 대표적으로 쿼리의 조건절에 사용된 컬럼이 인덱스의 첫번째 컬럼이 아닌 경우 인덱스 풀 스캔 방식이 사용됨 e.g. 순서가 있는 다중 인덱스(A, B)에서 B컬럼을 조건절에 이용한 경우
- 인덱스 레인지 스캔보다 빠르진 않지만, 테이블 풀 스캔보다는 효율적
  -> 인덱스에 포함된 컬럼만으로 쿼리를 처리할 수 있는 경우 인덱스를 읽으면 되므로 테이블의 레코드를 읽을 필요가 없고 테이블 자체의 크기보다는 인덱스의 전체 크기가 훨씬 작으므로 테이블 풀 스캔보다 효율적!
- `인덱스 풀 스캔`도 **인덱스를 사용한다**고 볼 수 있지만, 인덱스를 효율적으로 사용하는 것은 아님 되도록이면 지양하자

### 루스 인덱스 스캔 & 인덱스 스킵 스캔

- `루스 인덱스 스캔`
  - 말 그대로 느슨하게, 듬성듬성하게 인덱스를 스캔하는 것을 의미
  - 오라클에서 `인덱스 스킵 스캔`이라고 하는 기능과 작동 방식이 유사함
  - `인덱스 레인지 스캔`, `인덱스 풀 스캔`은 루스 인덱스 스캔과 상반된 의미에서 `타이트(tight) 인덱스 스캔`으로 분류하기도 함
  - 일반적으로 `GROUP BY` 또는 집합 함수 중 `MAX()`, `MIN()` 함수에 대해 최적화하는 경우 사용됨
- `인덱스 스킵 스캔`
  - MySQL 8.0 버전부터 옵티마이저가 인덱스 풀 스캔의 경우에 인덱스 스킵 스캔이 가능하도록 하는 최적화 기능이 도입되었고 기본 설정을 되어 있음
  - e.g. T라는 테이블에 인덱스(user_id, action_date)가 존재한다는 가정 하에 다음 예시를 살펴보자
    - `skip_scan 비활성화`
      1. `SET optimizer_switch='skip_scan=off;` 쿼리 실행으로 skip_scan 비활성화
      2. `EXPLAIN SELECT user_id, action_date FROM T WHERE action_date >= '2000-09-15';`
      3. 2번 쿼리의 실행 계획 조회 시 `type: index` 즉 인덱스 풀 스캔을 하는 걸 알 수 있음
    - `skip_scan 활성화`
      1. `SET optimizer_switch='skip_scan=on;` 쿼리 실행으로 skip_scan 활성화 (기본 활성화 되어 있음)
      2. `EXPLAIN SELECT user_id, action_date FROM T WHERE action_date >= '2000-09-15';`
      3. 2번 쿼리의 실행 게획 조회 시 `type: range`, `Extra: Using index for skip scan`을 확인 가능하고 이는 인덱스 스킵 스캔을 하는 걸 알 수 있음
  - **단점**
    - MySQL 8.0 버전에 새로 추가된 기능이여서 아직 이 존재함
    - WHERE 조건절에 조건이 없는 인덱스의 선행 칼럼의 유니크한 값의 개수가 적어야함
      -> 유니크한 값의 개수가 매우 많다면 MySQL 옵티마이저는 인덱스에서 스캔 시작 지점을 검색하는 작업이 많이 필요해지고 오히려 쿼리 처리 성능이 저하될 수 있음
    - 쿼리가 인덱스에 존재하는 컬럼만으로 처리 가능해야 함(커버링 인덱스)
      -> MySQL 8.0 버전 기준으로 인덱스에 존재하지 않느 컬럼을 조회하려면 인덱스 스킵 스캔을 사용하지 못 하고 풀 테이블 스캔으로 실행 계획이 결정됨. 이는 옵티마이저 개선 시 충분히 해결될 수 있음

### 다중 컬럼 인덱스

- 2개 이상의 컬럼을 구성된 인덱스를 `다중 컬럼 인덱스`(또는 복합 컬럼 인덱스)라고 하며, 컬럼이 연결되었다고 해서 Concatenated Index라고도 함
- 실무에서는 보통 2개 이상의 컬럼을 포함하는 인덱스가 더 많이 사용됨
- 다중 컬럼 인덱스는 인덱스 순서가 중요하여 신중하게 결정해야한다
-> 인덱스 정렬 시에 뒤에 있는 인덱스는 앞 순서에 있는 인덱스에 의존되어 정렬되기 때문
- 이는 인덱스 풀 스캔과도 관련이 있으며 보통 조건 절에 자주 사용되는 것을 인덱스의 앞 순서에 둠

## MySQL 쿼리 실행 절차

- MySQL의 쿼리 실행 절차를 나누고 이에 대해 정리해보자. 이는 추후 쿼리 실행 계획을 이해하는데 도움이 된다
  1. 사용자로부터 요청된 SQL 문장을 잘게 쪼개서 MySQL 서버가 이해할 수 있는 수준으로 분리(파스 트리로 만듬)
  2. SQL의 파싱 정보(파스 트리)를 확인하면서 어떤 테이블부터 읽고 어떤 인덱스를 이용해 테이블을 읽을지 선택함
     - 계획 수립 단계로 `옵티마이저`가 1번에서 만들어진 SQL 파스 트리를 참조하여 다음 작업을 처리함
       - 불필요한 조건 제거 및 복잡한 연산 단순화
       - 테이블 조인 여부를 검사하고 조회 연산 순서 결정
       - 쿼리 검색 조건과 인덱스 통계 정보를 이용해 사용할 인덱스 결정
       - 가져온 레코드를 임시 테이블에 넣고 재가공해야하는 지 결정
       - 등등 ...
  3. 두 번째 단계에서 결정된 테이블의 읽기 순서나 선택된 인덱스를 이용해 스토리지 엔진으로부터 데이터를 가져옴

## 커버링 인덱스

- `커버링 인덱스` : 쿼리를 충족하는데 필요한 모든 데이터를 갖는 인덱스 = 연산에 활용되는 모든 컬럼이 인덱스의 구성 요소인 경우 커버링 인덱스라 칭함
- 즉 인덱스에 있는 값들만으로 쿼리를 충족하는 것을 의미한다
- 이게 왜 중요하나면 InnoDB에서 PK는 보통 Clustered Key로 생성되고(커스텀 가능. 단 테이블당 하나만 존재), 인덱스는 Non-Clustered Key로 생성된다
- 그리고 인덱스를 타더라도 인덱스에 포함되지 않는 데이터를 SELECT 한다면 Non-Clustered Key에서 PK를 찾아 Clustered Key를 다시 조회한다 즉 연산이 2번 수행된다는 의미다. 즉, 커버링 인덱스를 잘 이용하면 이를 최적화 할 수 있다

## 테이블 명 영문 대소문자 구분

- MySQL 서버는 설치된 운영체제에 따라 테이블명의 대소문자를 구분한다 (DB나 테이블이 디스크의 디렉토리나 파일로 매핑되기 때문)
- 그래서 이로 인해 가끔 윈도우에서 운영되었던 MySQL 데이터를 리눅스로 가져오거나 하는 경우에 문제가 발생할 수 있다
- MySQL의 설정 파일에 `lower_case_table_names`변수를 설정하면 이를 구조적으로 통제 가능하다
  - `lower_case_tabe_names` 변수 값에 따른 설정
    - 0 : (default) 테이블 명에 대해 대소문자 구분
    - 1 : 모두 소문자로만 저장 = 대소문자 구분 X
    - 2 : 윈도우와 macOS에서 가능. 대소문자를 구분하지만 쿼리에서는 대소문자 구분 X
- 하지만 이렇게 설정 자체를 떠나서 팀 내 컨벤션을 정해 구조적으로 대문자 또는 소문자로 통일해서 사용하는 것이 좋다

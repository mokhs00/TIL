# Redis Data Type 이해

## Strings

- 가장 기본적인 데이터 타입으로 제일 많이 사용됨
- 바이트 배열을 저장(binary-safe)
- **바이너리로 변환할 수 있는 모든 데이터를 저장 가능**(JPG와 같은 파일 등)
- **최대 크기는 512MB**
- 주요 명령어
  - SET
    - 특정 키에 문자열 값을 저장
    - `SET key1 value1`
  - GET
    - 특정 키의 문자열 값을 조회
    - `GET key1`
  - INCR
    - 특정 키의 값을 Integer로 취급하여 1증가
    - **atomic 지원 = race condition에 걸리지 않음 동시성 문제가 없다**
    - `INCR mycount`
  - DECR
    - 특정 키의 값을 Integer로 취급하여 1증가
    - **atomic 지원 = race condition에 걸리지 않음 동시성 문제가 없다**
    - `DECR mycount`
  - MSET
    - 여러 키에 대한 값을 한번에 저장 (bulk)
    - `MSET key3 value3 key4 value4`
  - MGET
    - 여러 키에 대한 값을 한번에 조회
    - `MGET key3 key4`

## Lists

- Linked-list 형태의 자료구조(인덱스 접근은 느리지만 데이터 add/delete 가 빠름)
- Queue, Stack으로 활용 가능
- 양쪽 끝을 기준으로 데이터를 수정할 수 있음
- 명령어
  - LPUSH, RPUSH
    - 리스트의 left, right에 새로운 값을 추가
    - `LPUSH mylist value1`
    - `RPUSH mylist value2`
  - LLEN
    - 리스트에 저장된 아이템 개수를 반환
    - `LLEN mylist`
  - LRANGE
    - 리스트의 특정 범위를 반환
    - `LRANGE mylist 0 -1`
      - -1은 마지막부터 첫번쨰
  - LPOP, RPOP
    - 리스트의 left, right에서 값을 삭제하고 반환(pop)
    - `LPOP mylist`
    - `RPOP mylist`

## Sets

- 순서가 없는 유니크한 값의 집합
- 검색이 빠름
- 개별 접근을 위한 인덱스가 존재하지 않음
- 집한 연산이 가능(교집합, 합집합 등)
  - **단순히 데이터 존재 여부를 확인하는 용도로 쿠폰 발급 여부 등에 활용할 수 있음**
- 명령어
  - SADD
    - SET에 데이터 추가
    - `SADD myset v1`
  - SREM
    - SET에서 데이터를 삭제
    - `SREM myset v1`
  - SCARD
    - SET에 저장된 아이템 개수를 반환
    - `SCARD myset`
  - SMEMBERS
    - Set에 저장된 아이템들을 반환
    - `SMEMBERS myset`
  - SISMEMBER
    - SET에 특정 값이 포함되어 있는지 여부를 반환
    - `SISMEMBER myset v1`

## Hashes

- 하나의 key 하위에 여러개의 field-value 쌍을 저장
- 여러 필드를 가진 객체를 저장하는 것으로 볼 수 있음
- HINCRBY 명령어를 사용해 카운터로 활용 가능
- 명령어
  - HSET
    - 한개 또는 다수의 필드에 값을 저장
    - `HSET user1 name mokhs age 10`
  - HGET
    - 특정 필드의 값을 반환
    - `HGET user1 name`
  - HMGET
    - 한개 이상의 필드 값을 반환
    - `HMGET user1`
  - HINCRBY
    - 특정 필드의 값을 integer로 취급하여 숫자 증가 INCR 명령어와 같음
    - `HINCRBY user1 viewcount 1`
  - HDEL
    - 한개 이상의 필드를 삭제
    - `HDEL user1 name age`
  - HKEYS
    - 특정 키 값의 모든 필드 조회
    - `HKEYS user1`

## Sorted Sets

- Set과 유사하게 유니크한 값의 집합
- score를 가지고 정렬되어 있음
- 정렬된 상태이기에 빠르게 최소/최대값을 구할 수 있음
- 순위 계산, 리더보드 구현 등에 활용
  - user1 (score: 10)
  - user2 (score: 20)
- 명령어
  - ZADD
    - 한 개 또는 다수의 값을 추가 또는 업데이트
    - `ZADD myrank 10 user1 20 user2`
  - ZRANGE
    - 특정 범위의 값을 반환(오름차순으로 정렬된 기준)
    - `ZRANGE myrank 0 1`
  - ZRANK
    - 특정 값의 위치(순위)를 반환(오름차순 정렬 기준)
    - `ZRANK myrank user1`
  - ZREVRANK
    - 특정 값의 위치(순위)를 반환 (내림차순 기준)
    - `ZREVRANK myrank user1`
  - ZREM
    - 한 개 이상의 값을 삭제
    - `ZREM myrank user1`

## Bitmaps

- 비트 벡터를 사용해 N개의 Set을 공간 효율적으로 저장
- 하나의 비트맵이 가지는 공간은 약 42억(2^32-1)
- 비트 연산 가능
- 유저의 방문 현황 같은 경우 integer와 같은 4바이트 크기로 42억 명의 방문 여부를 저장할 수 있음
- 명령어
  - SETBIT
    - 비트맵의 특정 오프셋에 값을 변경
    - `SETBIT visit 10 1`
  - GETBIT
    - 비트맵의 특정 오프셋의 값을 반환
    - `GETBIT visit 10`
  - BITCOUNT
    - 비트맵에서 set(1) 상태인 비트의 개수를 반환
    - `BITCOUNT visit`
  - BITOP
    - 비트맵들간의 비트 연산을 수행하고 결과를 비트맵에 저장
    - BITOP (비트연산) (결과) (연산 대상…)
    - `BITOP AND result today yesterday`

## HyperLogLog

- 대용량 데이터에 대해서 유니크한 값의 개수를 효율적으로 얻을 수 있음
  - 단순 카운팅을 위한 자료구조
  - 2^64개의 유니크 값을 계산 가능
- Bitmaps는 userID 등 측정 offset을 활용해야 하지만 HyperLogLog는 문자열을 활용할 수 있음
- 확률적 자료구조로서 오차가 있으며, 매우 큰 데이터를 다룰 때 사용
  - 값을 추가한다고 그 값이 그대로 실제로 저장되는 건 아니고 확률적 자료구조의 데이터 일부가 됨
- 12KB까지 메모리를 사용하며 0.81%의 오차율을 허용
- 명령어
  - PFADD
    - HyperLogLog에 값들을 추가
    - `PFADD visit Jay Peter Jane`
  - PFCOUNT
    - HyperLogLog에 입력된 값들의 cardinality(유일값의 수)를 반환
    - `PFCOUNT visit`
  - PFMERGE
    - 다수의 HyperLogLog를 병합
    - `PFMERGE result visit1 visit2`

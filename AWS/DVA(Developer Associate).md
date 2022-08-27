- [개요](#개요)
  - [RDS](#rds)
    - [Encryption](#encryption)
    - [Aurora DB](#aurora-db)
    - [ElastiCache](#elasticache)

# 개요

- AWS DVA(Developer Associate) 자격증 공부한 내용을 기록합니다.

## RDS

- 동일 리전 레플리카 Sync 추가 비용 X  <=> 다중 리전 레플리카 Sync 추가 비용 O(Network cost)
- 마스터 DB의 리전 외에 다른 리전에 스탠바이 RDS를 두어 가용성 확보 가능
- RDS 읽기 전용 복제본은 비동기식 복제, 다중 AZ의 복제는 동기식 복제를 사용

### Encryption

- DB instance를 처음 생성할때만 가능. 처음 생성 시에 암호화하지 않았다면 스냅샷을 생성해서 교체 가능
- AWS KMS를 이용해 암호화 가능
- master를 암호화하지 않으면 read replicas를 암호화 불가능
- SSL
- `Encryption Operations e.g...`
  - Encrypting RDS backups
    - Snapshots of un-encrypted RDS databases are un-encrypted
    - Snapshots of encrypted RDS databases are encrypted
    - Can copy a snapshot into an encrypted one
  - To encrypt an un-encrypted RDS database
    - Create a snapshot of the un-encrypted database
    - Copy the snapshot and enable encryption for the snapshot
    - Restore the database from the encrypted snapshot
    - Migrate applications to the new database, and delete the old database

### Aurora DB

- Automatic fail-over
- Backup and Recovery
- isolation and security
- Industry compliance
- Auto scaling(Push-button scaling)
- Automated Patching with Zero Downtime
- Advanced Monitoring
- Routine Maintenance
- Backtrack: restore data at any point of time without using backups

- `Writer Endpoint`
  - master 노드를 가리킴 마스터 노드 장애 시에도 Wirhter Endpoint와 통신해 가용한 인스턴스로 리디렉션됨
- `Reader Endpoint`
  - read replica에 대한 Reader Endpoint를 제공하여 로드밸런싱 지원
  - auto scaling으로 인해서 read replica에 대한 Endpoint 추적이 어려운 것을 해결할 수 있음

- Security
  - 다른 RDS와 유사
  - SSL, KMS 이용 암호화, SSL, IAM 인증… etc..

### ElastiCache

- `Redis vs Memcached`
  - **Redis**
    - Multi AZ with Auto-Failover
    - Read Replicas to scale reads and have high availability
    - Data Durability using AOF(Append Only File) persistence
    - Backup and restore features
    - RDS와 매우 유사
    - =고가용성, 백업, replication 가능
  - **Memcached**
    - Multi-node for partitioning of data (=sharding)
    - No high Availability (replication)
    - Non persistent (지속적 캐시가 아님)
    - No backup and restore
    - Multi-threaded architecture
    - 데이터를 손실할 수 없는 고성능 분산 캐시, 가용성 낮음, no backup and restore
  - **Redis는 데이터베이스로 사용할 수 있지만 Memcached는 단순 캐시용으로만 사용함**
- `caching best-practices`
  - [https://aws.amazon.com/ko/caching/best-practices/](https://aws.amazon.com/ko/caching/best-practices/)
  - **Lazy Loading(**=Cache-Aside=Lazy Population)
    - 먼저 캐시를 hit하고 없다면(cache miss) 데이터를 RDS로 가서 조회하고 해당 데이터를 cache write함
    - 데이터가 없을 시 connection을 3번(cache hit, rds read, cache write)해야함
    - 대부분의 상황에서 사용되며 굉장히 쉬움
  - **Write Through**(=Add or Update cache when database is updated)
    - 캐시에서만 데이터를 조회하고 RDS에서 update, create가 발생했을 때 해당 데이터를 cache에 저장
    - RDS에 데이터 추가 → cache sync 과정에서 sync 이전에 데이터를 조회하는 경우가 있을 수 있음 (= 데이터 유실) 그래서 Lazy Loading 전략과 결합하여 사용함
    - 많은 데이터를 저장해 일부 데이터가 읽히지 않는 경우 리소스 손실
  - **Cache Evictions and Time-to-live (TTL)**
    - 캐시를 제거하여 갱신
    - 메모리가 꽉차면 LRU 방식으로 데이터를 제거하거나
    - TTL을 두어 일정 시간이 지나면 제거되도록함
    - 애플리케이션에 적합한 TTL을 찾아야함
  - 캐시는 사용하기 어려운 리소스이다. **필요한 데이터에만 캐싱을 적용하자**(동기화 레벨 고려)
- `cluster mode(Disabled vs Enabled)`
  - **Disabled**
    - One primary node, up to 5 replicas. 하나의 기본 노드, 5개까지 레플리카 가능
    - Asynchronous Replication
    - primary node는 readWriteDB, 나머지 레플리카는 readOnlyDB
    - One shard. all nodes have all the data
    - Guard against data loss if node failure
    - 장애 대응을 위해 다중 AZ 기본 활성화
  - **Enabled**
    - Data is partitioned across shards (helpful to scale write) = 쓰기 확장에 용이
    - =N개의 샤드에 데이터가 분산되어 저장
    - Disable mode와 동일하게 One primary node, up to 5 replicas.
    - Multi-AZ capability
    - 클러스터당 최대 500개 node 생성 가능. replica 수에 따라 가능한 샤드 수가 조정됨
      - single master = 500개의 샤드 가능
      - 1 master, 1 replica = 250개의 샤드 가능
      - 1 master and 5 replicas = 83개의 샤드 가능
- more..
  - aws caching best-practices [https://aws.amazon.com/ko/caching/best-practices/](https://aws.amazon.com/ko/caching/best-practices/)
  - NHN 캐시성능 향상기  [https://meetup.toast.com/posts/251](https://meetup.toast.com/posts/251)

- [개요](#개요)
  - [RDS](#rds)
    - [Encryption](#encryption)
    - [Aurora DB](#aurora-db)
    - [ElastiCache](#elasticache)
  - [Route 53](#route-53)
  - [VPC](#vpc)
    - [Subnet](#subnet)
    - [Internet Gateway & NAT Gateways](#internet-gateway--nat-gateways)
    - [Network ACL & Security Groups](#network-acl--security-groups)
    - [VPC Flow Logs](#vpc-flow-logs)
    - [VPC Peering](#vpc-peering)
    - [VPC Endpoints](#vpc-endpoints)
    - [Site to Site VPN & Direct Connect](#site-to-site-vpn--direct-connect)

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

## Route 53

- 확장 가능한 고가용성 fully managed and Authoritative DNS
  - Authoritative = DNS records를 직접 완전히 제어할 수 있음
- Route 53 is also a Domain Registrar
- 리소스 관련 health check 가능
- 100% SLA(*Service Level Agreement)* 가용성을 제공하는 유일한 AWS 서비스
- 이름이 53인 이유는 전통적인 DNS port가 53라서..

- `Records`
  - 특정 도메인으로 라우팅하는 방법을 정의
  - 다음 정보를 포함
    - Domain/subdomain Name  - e.g. google.com
    - RecordType - e.g. A or AAAA
    - Value - e.g. 123.456.123.123
    - Routing Policy - query에 대해 Route 53이 어떻게 응답할지
    - TTL - DNS Resolver 캐시 유지 시간

- `Record Types`
  - A : maps a hostname to IPv4
  - AAAA - maps a hostname to IPv6
  - CNAME - maps a hostname to another hostname
    - A나 AAAA 타입 레코드 대상으로 함
    - DNS namespace 상위 노드에 대한 CNAME record를 생성할 수 없음 (=Zone Apex)
      - =example.com에 대한 CNAME 레코드는 만들 수 없으나 www.example.com에 대한 CNAME 레코드는 생성 가능
  - NS - name servers for the **hosted zone**
    - traffic 라우팅 제어 가능(routing policy)

- `Hosted Zones`
  - A container for records that define how to route traffic to a domain and its subdomains
  - VPC를 이용한 외부 공개 여부에 따라 **Public Hosted Zones** / **Private Hosted Zones**로 나누어 볼 수 있음
  - 어떤 Hosted Zone이든 달에 $0.50 비용 지불

- `CNAME vs Alias`
  - CNAME, Alias는 보유한 도메인에 hostname을 매핑하고자할 때 선택방안이 될 수 있음.
    아래 차이점이 있으므로 적절한 선택이 필요
  - **CNAME**
    - Points a hostname to any other hostnaem
    - 루트 도메인 불가능
  - **Alias**
    - Points a hostname to an **AWS Resource**
    - **루트 도메인 가능**
    - Free of charge
    - health check 가능
- `Alias Records`
  - Maps a hostnaem to an AWS resource
  - DNS 확장 기능으로 시중의 모든 DNS에서 가능
  - **Automatically reconizes changes** in the resource’s IP Address
  - CNAME과 달리 DNS namespace의 최상위 노드에 연결할 수 있음(Zone Apex) e.g. example.com
  - Record type은 항상 A/AAAA for AWS resources (IPv4 / IPv6)
  - TTL 커스텀 불가능. Route53에 의해 자동 설정
  - **Alias Records Targets(AWS resource)**
    - `ELB`, `CloudFront Distributions`, `API Gateway`, `Elastic Beanstalk environments`, `S3 Websites`, `VPC Interface Endpoints`, `Global Accelerator accelerator`, `Route 53 record in the same hosted zone`,
    - EC2 DNS name에는 불가능
- `Routing Policies`
  - Define how Route 53 responds to DNS queries
  - 여기서의 라우팅은 로드밸런서 라우팅과는 다름. (=DNS does not route any traffic)
  - 단순 DNS 쿼리에 대한 결과를 route
  - `Polices`
    - **Simple**
      - route traffic to a single resource
      - 다중 값을 받은 경우 client에서 랜덤으로 하나를 지정
      - no health check
    - **Weighted**
      - 가중치 기반 route
    - **Failover**
      - primary, secondery resource를 연결하여 장애 조치 가능
    - **Latency based**
      - 지연시간 기반 region에 따라 지연시간이 가장 적은 곳으로 route
    - **Geolocation**
      - **Latency-based 정책과 다름** user location에 기반하여 지정된 위치로 routing함
      - Use cases: **웹사이트 현지화**(website localization), 콘텐츠 분산 제한(restrict content distribution), load balancing…
      - 해당 유저가 어떤 나라, 어떤 도시에 있는지 가장 정확한 위치를 선택하여 routing함
      - Should create a `deafult` record (in case there’s no match on location)
      - Can be associated with Health Checks
    - **Multi-Value answer**
      - 다중 값을 응답하여 client-side 로드밸런싱(ELB를 대체할 수는 없음)
      - health checks와 연결하면 healthy resource만 return
      - return 최대 8개의 healthy records
    - **Geoproximity (using Route 53 Traffic Flow feature)**
      - 리전마다 편향값(bias)을 두어 실제 유저의 거리 기반으로 편향값이 높은 쪽으로 더 많은 트래픽을 분배함
      - 즉 편향값에 따라 트래픽 분배 여부 분기점이 편향되어 적용됨
      - **특정 리전에 더 많은 트래픽을 더 보내야 할때 유용함** 따라서 편향값을 증가시켜 한 리전에서 다른 리전으로 트래픽을 보낼 때 유용함
      - 해당 Policy를 위해 Route 53 traffic flow feature를 사용해야함
  - `Health Check`
    - HTTP Health check은 public resource에서만 가능하고
    - CloudWatch 메트릭에서 통합하여 볼 수 있음
    - 15 global health checkers가 endpoint health를 체크하게 됨
      - Healthy/Unhealthy Threshold : 3초 (default)
      - Interval - 30 sec (10초로 설정해서 fast health check 가능=비용 추가)
      - 15 global health checkers 중 18%이상이 해당 endpoint가 Healthy하다고 판정하면 Route 53은 이를 Healthy하다고 판정
      - 상태확인에 사용될 location도 설정 가능
    - Health Checks pass only when the endpoint responds with the 2xx and 3xx status codes
    - 텍스트 기반 응답일 경우 처음 5120bytes를 확인해서 응답에 특정 text가 포함되는지 확인하고 이를 pass/fail 기준으로 사용할 수 있음
    - `Calculated Health Checks`
      - Combine the results of multiple Health Checks into a single Health Check
      - OR, AND, NOT 가능
      - 256 Child Health Checks까지 모니터링 가능
      - 상위 health check pass를 위해 하위 pass를 몇 개 받아야하는지 지정할 수 있음
    - `How to monitor Private Hosted Zones`
      - Route 53 health checker는 외부 VPC에 있기에 외부 접근이 불가능한 private VPA, on-premises resource에 접근 불가능
      - 대신에 CloudWatch Metric기반으로 CloudWatch Alarm을 만들어 이를 Route 53 Health checker가 모니터링 하도록 할 수 있음

## VPC

- Virtual Private Cloud
- private network to deploy your resources (regional resource)

### Subnet

- Subnets allow you to partition your network inside your PC(Availability Zone resource)
  - public subnet is a subnet that is accessible from the internet
  - private subnet is a subnet that is not accessible from the internet
- To define access to the internet and between subnets, we use **Route Tables**

### Internet Gateway & NAT Gateways

- Internet Gateways helps our VPC instances connect with the internet
  - Public Subnets have a route to the internet gateway
- NAT Gateways (AWS-managed) & NAT Instances (self-manageed) allow your instances in your Private Subnets to access the internet while remaining private
  - 외부에서의 접근은 막고 해당 인스턴스에서 외부로 접근하는 것을 허용하기 위해사용
  - NAT 게이트웨이는 public subnet에 두고 private subnet에 있는 인스턴스를 연결함

### Network ACL & Security Groups

- **Network ACL (NACL)**
  - **Stateless, subnet rules for inbound and outbound**
  - A firewall which controls traffic from and to subnet
  - Can have ALLOW and DENY rules
  - Are attached at the Subnet level
  - Rules only include IP address
  - 기본 VPC에서 기본 NACL은 in-out 트래픽을 모두 허용함
- Security Groups
  - **Stateful, operate at the EC2 instance level or ENI**(Elastic Network Interface)
  - A firewall that controls traffic to and from an **ENI** / an EC2 Instance
  - Can have only ALLOW rules
  - Rules include IP addresses and other security groups

### VPC Flow Logs

- Capture informationo about IP traffic going into your interfaces
  - VPC Flow logs
  - Subnet Flow logs
  - Elastic Network Interface Flow logs
- Helps to monitor & troubleshoot connectiity issues
- Captures network information from AWS managed interfaces too: ELB, ElastiCache, RDS, Autora, etc…
- VPC Flow logs data can go to S3 / Cloud Watch Logs

### VPC Peering

- AWS network를 이용해 비공개로 2개의 VPC를 연결
- 연결된 VPC가 같은 network에 있는 것처럼 만들어줌
- 각 VPC에 정의된 IP address 범위가 겹치지 않는지 확인해야함 만약 겹치면 네트워크가 어디로 가야할지 찾을 수 없음
- VPC Peering은 전이되지 않으며 A, B, C 3개의 VPC를 연결하고 싶다면 각각 연결해줘야함
(A-B, B-C, A-C)

### VPC Endpoints

- VPC에서 AWS service로의 비공개 접속을 원할 때 사용
- VPC Endpoitns는 public www network 대신에 private network를 사용하여 AWS Service에 연결하는 것을 허용
- 따라서 이는 AWS service에 access 하는데 Lower latency와 보안성을 제공함
- VPC Endpoint Gateway: S3 & DynamoDB 연결 시 사용
- VPC Enpoint Interface: 나머지 리소스 연결 시 사용

### Site to Site VPN & Direct Connect

- Site to Site VPN
  - 온프레미스 VPN장치와 AWS를 연결
  - connection은 자동으로 encryted됨
  - public internet상에서 요청 전송
- Direct Connect (DX)
  - 온프레미스와 AWS를 물리적으로 연결
  - connection is private, secure and fast
  - private network
  - 구성이 오래걸림 (Takes at least a month to establish)
- Site-to-Site VPN, Direct Connect는 VPC endpoints에 연결 불가능

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
  - [ECS](#ecs)
    - [ECS Service Auto Scaling](#ecs-service-auto-scaling)
    - [ECS Task Definitions](#ecs-task-definitions)
    - [ECS Tasks Placement](#ecs-tasks-placement)
  - [ECR](#ecr)
  - [EKS](#eks)
  - [ELB](#elb)
    - [ALB](#alb)
    - [ALB Target Groups](#alb-target-groups)
    - [NLB](#nlb)
    - [NLB target gruops](#nlb-target-gruops)
    - [GWLB :Gateway Load balancer](#gwlb-gateway-load-balancer)
    - [GWLB Target Groups](#gwlb-target-groups)
    - [Sticky Sessions (Session Affinity)](#sticky-sessions-session-affinity)
    - [Cross-Zone Load Balancing](#cross-zone-load-balancing)
    - [SSL/TLS](#ssltls)
    - [Connection draining](#connection-draining)
    - [Auto Scaling Group](#auto-scaling-group)
  - [S3](#s3)
    - [Bucket](#bucket)
    - [Objects](#objects)
    - [Versioning](#versioning)
    - [S3 Encryption](#s3-encryption)

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

## ECS

- **E**lastic **C**ontainer **S**ervice
- `EC2 Launch Type`
  - Launch **ECS Tasks** on ECS Clusters
  - ECS cluster에서 해당 Launch Type 선택 시 EC2 instance를 관리해야함
  - must run the ECS Agent to register in the ECS Cluster
- `Fargate Launch Type`
  - 관리할 EC2 인스턴스가 없음 do not provision the infrastructure
    (no EC2 instances to manage)
  - All Serverless
  - scaling하기 위해서는 간단하게 task의 수를 조정하기만 하면 됨
- `IAM Roles for ECS`
  - **EC2 Instance Profile (EC2 Launch Type only)**
    - Used by the ECS agent
    - Makes API calls to ECS service
    - Send contrainer logs to CloudWatch Logs
    - Pull Docker image from ECR
    - Reference sensitive data in Secrets Manager or SSM Parameter Store
  - **ECS Task Role**
    - Allows each task to have specific role
    - Use different roles for the different ECS Services you run
    - Task Role is defined in the task definition
- `Load Balancer Integrations`
  - **ALB** suppported and works for most use cases
  - **NLB** recommended only for high throughput / high performance use cases, or pair it with AWS Private Link
  - **ELB supported but not recommend (no advanced features - no Fargate)**
- `Data volumes (EFS)`
  - Mount EFS file systems onto ECS tasks
  - Works for both EC2 and Fargate launch types
  - Tasks running in any AZ will share the same data in the EFS file system
  - Fargate + EFS = Serverless 이 조합 좋다.. no manage server
  - Use caes: persistent multi-AZ shared storage for your containers
    - **But, Amazon Fsx For Lustre is not supported**
    - **S3 cannot be mounted as a file system for ECS task**

### ECS Service Auto Scaling

- ECS auto scaling uses **AWS Application Auto Scaling**
  - **다음 지표들 확인가능**
    - ECS Service Average CPU Utilization
    - ECS Service Average Memory Utilization - Scale on RAM
    - ALB Request Count Per Target - metric coming from the ALB
- 종류
  - **Target Tacking** - scaled based on target value for a specific CloudWatch metric
  - **Step Scaling** - scale based on specified CloudWatch Alarm
  - **Scheduled Scaling** - scale based on specified date/time (predictable changes)
- note;
  - ECS Service Auto Scaling (task level) ≠ EC2 Auto Scaling (EC2 instance level)
  - Fargate Auto scaling is much easier to setup ⇒ because serverless
- EC2 Launch Type

### ECS Task Definitions

- Task definitions are metadata in JSON form to tell ECS how to run a Docker container
- It contains crucial information, such as:
  - Image Name
  - Port Binding for Container and Host
  - Memory and CPU required
  - Env variables
  - Networking information
  - IAM Role
  - Logging configuration (ex CloudWatch)
- Can define up to 10 containers in a Task Definition
- `태스크 정의 당 IAM 역할이 할당됨 One IAM Role per Task Definition`
  - 각 태스크는 해당 IAM 역할을 자동으로 상속하게 됨
- `ECS Load Balancing` when using Task Definitions
  - **EC2 Launch Type**
    - We get a Dynamic Host Port Mapping if you define only the container port in the **task definition**
    - The ALB finds the right port on your EC2 instances **using Dynamic Host Port Mapping**
    - you must allow on the EC2 instance’s Security Group any port from the ALB’s Security Group
  - **Fargate Launch Type**
    - Each Task has a unique private IP using ENI and each task have all same port
    - Only define the container port (host port is not applicable)
    - e.g.
      - ECS ENI SG
        - Allow port 80 from the ALB
      - ALB SG
        - Allow port 80/443 from web
- `Environment Variables`
  - Variable
    - Hardcoded - e.g. URLs
    - SSM Parameter Store - sensitive variables (e.g. API keys, shared configs)
    - Secrets Manager - sensitive variables (e.g. DB passwords)
  - loading from Files (=bulk loading) -  Amazone S3
- `Data Volumes (Bind Mounts)` (ECS task간 공유)
  - Share data between multiple containers in the same Task Definition
  - Works for both EC2 and Fargate tasks
  - sidecar pattern 이용 시 `/var/logs` 라는 임의의 shared storage를 bind mount해서 사용할 수 있음
  - EC2 Tasks -  using EC2 instance storage
    - Data are tied to the lifecycle of the EC2 instance
  - Fargate Tasks - using ephermeral storage
    - Data are tied to the container using them
    - 20 GiB - 200 GiB (default 20GiB)
  - Use cases:
    - Share ephemeral data between multiple containers
    - sidecar container pattern, where the sidecar container used to send metrics/logs to other destinations (separation of concerns)

### ECS Tasks Placement

- ECS task 배치 전략에 대한 정의
- **EC2 launch type**에서만 유효함
- task를 어떤 instance에 배치하고 어떤 instance terminate할지(scale in)를 결정
- `task placement strategy`, `task placement constraints`를 정의할 수 있음
- task placement process는 다음과 같음
    1. task definition에 요구하는 CPU, memory, port에 만족하는 instance를 식별
    2. task placement constraint(배치 제한)을 기반으로 instance를 식별
    3. task plcaement strategies를 기반으로 instance를 식별
- `task placement strategies`
  - **Binpack**
    - Place tasks based on the least available amount of CPU or memory
    - This minimizes the number of instances in use (cost savings)
    - 한 EC2 instance에 배치 가능한만큼 컨테이너를 꽉 채운 후 다음 instance에 컨테이너를 배치
    - e.g.

            ```json
            "placementStrategy": [
             {
              "field": "memory",
              "type": "binpack"
             }
            ]
            ```

  - **Random**
    - just place the task random
    - e.g.

            ```json
            "placementStrategy": [
             {
              "type": "random"
             }
            ]
            ```

  - **Spread**
    - 특정값을 기반으로 분산 배치
    - 특정값은 instanceId, attribute:ecs.availability-zone 등이 될 수 있음
    - e.g.

            ```json
            "placementStrategy": [
             {
              "field": "attribute:ecs.availability-zone",
              "type": "spread"
             }
            ]
            ```

  - **위 배치 전략들을 섞어서 사용할 수도 있음**
    - e.g.

            ```json
            "placementStrategy": [
             {
              "field": "attribute:ecs.availability-zone",
              "type": "spread"
             },
             {
              "field": "instanceId",
              "type": "spread"
             }
            ]
            
            // other case
            "placementStrategy": [
             {
              "field": "attribute:ecs.availability-zone",
              "type": "spread"
             },
             {
              "field": "memory",
              "type": "binpack"
             }
            ]
            ```

- `task placement constraints`
  - **distinctInstance**
    - place each task on a different contrainer instance
    - e.g.

            ```json
            "placementConstraints": [
             {
              "type": "distinctInstace"
             }
            ]
            ```

  - **memberOf**
    - place task on instances that satisfy an expression
    - uses the cluster query language
    - e.g.

            ```json
            "placementConstraints": [
             {
              "expression": "attributes:ecs.instance-type =~ t2.*",
              "type": "distinctInstace"
             }
            ]
            ```

## ECR

- Elastic Container Registry
- Store and manage Docker images on AWS
- public repo도 가능 (Amazon ECR Public Gallery)
- 모든 이미지는 Amazon S3에 저장됨
- ***Access is controlled through IAM**
- 이미지 취약점 스캐닝, 버저닝, 태그, 수명 주기 확인 지원

## EKS

- Elastic Kubernetes Service
- managed Kubernetes clusters on AWS
- EKS supports EC2 if you want to deploy worker node, Fargate to deploy serverless containers

## ELB

### ALB

- Application Load Balancer on layer 7 (HTTP)
- load balancing to multiple HTTP applications across machines (target groups)
- load balancing to multiple applications on the same machine (ex: containers)
- Support HTTP/2 and WebScoket
- Support redirects
- Routing tables to different target groups(=nginx에서 지원하던 기능과 비슷
  - path in URL
  - hostname in URL
  - Query String, Headers
- ALB are a great fit for micro services & container-based application (example: Docker & Amazon ECS)
- Has a port mapping feature to redirect to a dynamic port in ECS
- Im comparison, we’d need multiple Classic Load Balancer per application

### ALB Target Groups

- EC2 instances (can be managed by an ASG(Auto Scaling Group)
- ECS tasks (managed by ECS itself)
- Lambda function: HTTP request is translated into a JSON event
- IP Addresses : must be private IPs
- ALB can route to multiple target groups
- **Health checks are at the target group level**
- `Good to Know`
  - Fixed hostname
  - The application servers don’t see the IP of the client directly
    - The true IP of the client is inserted in the **header `X-Forwarded-For`**
      - ALB가 연결된 target group과 connection할 때는 private IP를 이용하기 때문에 실제 client의 IP를 바로 알 수 없고 HTTP header에서 확인해야 한다
    - We can also get Port (X-Forwarded-Port) and proto (X-Forwarded-Proto)
- `Query Strings/Parameters Routing` 가능

### NLB

- Network Load Balancer (Layer 4)
  - Forward TCP & UDP traffic to your instances
  - Handle millions of request per seconds
  - Less latency ~ 100 ms (vs 400 ms for **ALB**)
- NLB has **one static IP per AZ**, and supports assigning Elastic IP (helpful for whitelisting specific IP)
  - CLB, ALB 는 고정 IP가 없고 고정 hostname만 존재
- NLB are used for extreme performance, TCP or UDP traffic

### NLB target gruops

- EC2 instances
- IP Addresses: must be private IPs
- ALB(NLB를 이용해 고정 IP를 가질 수 있어서 사용함)
- **NLB를 TCP 기반으로 생성하면 security group으로 활용하던 건 연결된 target gruop(EC2)의 보안그룹이다. health check에서 의도치않게 unhealthy로 보이는 이상이 있다면 이 점을 유의하자**

### GWLB :Gateway Load balancer

- 네트워크 트래픽을 분석하기 위해 3rd party 제품을 사용하는 경우 해당 제품으로 routing 가능
- **flow**
  - User → GWLB → 3rd Party Security Virtual Appliances → GWLB → Application
- Deploy, scale, and manage a fleet of 3rd party network virtual appliances in AWS
- e.g. Firewalls, Intrusion Detection and Prevention Systems, Deep Packet Inspection System, payload manipulation, …

- Operateds at Layer 3 (Network Layer) - IP Packets
- Combines the following functions:
  - Transparent Network Gateway - single entry/exit for all traffic
  - Load Balancer - distributes traffic to your virtual appliances
- Uses the **GENEVE** protocol on port **6081**

### GWLB Target Groups

- EC2 instances
- IP Address: must be private IPs

### Sticky Sessions (Session Affinity)

- 같은 client 요청이라면 동일한 인스턴스에 라우팅
- 쿠키를 사용해 만료 시간을 둠(쿠키 이름은 커스텀 가능)
- 세션 정보를 유지해야만 하는 경우 사용
- **Cookie Names** (이런 쿠키를 예약어로 사용한다 정도로 인지하자)
  - **Application-based Cookies**
    - Custom cookie
      - generated by the target
      - **AWSALB, AWSALBAPP or AWSALBTG 는 예약어로 사용 금지(reserved for use by the ELB)**
    - Application cookie
      - generated by load balancer
      - **AWSALBAPP**
  - **Duration-based Cookies**
    - generated by load balancer
    - **AWSALB**(for ALB),  **AWSELB**(for CLB)

### Cross-Zone Load Balancing

- 등록된 모든 AZ에 트래픽을 동등하게 분배 가능
- 총 인스턴스 수에 비례하여 트래픽을 분배함(AZ에 상관 X) 같은 AZ에 있는 것처럼 분배됨
- Application Load Balancer
  - Always on (can’t be disabled)
  - No charges for inter AZ data
- Network Load Balancer
  - DIsabled by default
  - you pay charges for inter AZ data if enabled
- Classic Load Balancer
  - Disabled by default
  - No charges for inter AZ data if enabled

### SSL/TLS

- An SSL Certificate allows traffic between your clients and your load balancer to be encrypted in trasit (in-flight encryption)
- SSL refers to Secure Sockets Layer, used to encrypt connections
- TLS refers to Transport Layer Security, which is a newer version
- Nowadays, TLS certificateds are mainly used, but people still refer as SSL
- Public SSL 인증서는 Certificate Authorities(CA = 인증기관)에서 발행함
  - Comodo, Symantec, GoDaddy, GlobalSign, Digicert 등등이 있음
- SSL certificates have a expiration date (you set) and must be renewed

Load balancing …

- load balancer uses X.509 certificate
- you can manage certificates using ACM (AWS Certificate Manager)
- HTTPS listener:
  - You must specify a default certificate
  - You can add an optional list of certs to support multiple domains
  - Clients can use SNI (Server Name Indication) to specify the hostname they reach
  - Abillity to specify a security policy to support older versions of SSL / TLS (legacy client)
- `SNI`: Server Name Indication
  - SNI를 이용하면 단일 웹 서버가 다중 SSL 인증서를 이용해 다중 website를 제공할 수 있게 됨
  - It’s a newer protocol, and requires the client to indicate the hostname, of the target server in the initial SSL handshake
  - The server will then find the correct certificate, or return the default one
  - 최신 프로토콜이라 모든 클라이언트가 이를 지원하지는 않음
  - Only works for ALB & NLB (newer generation), CloudFront
  - Does not work for CLB (older gen)

### Connection draining

- Deregistration Delay라고도 부름
- Time to complete `in-flight requests` while the instance is de-registering or unhealthy
- Stops sending new requests to the EC2 instance which is de-registering
- 1~3600 seconds (Default: 300 seconds)
- Can be disabled (set value to 0)
- Set to a low value if your requests are short

### Auto Scaling Group

Dynamic Scaliing Policies

- Target Tracking Scaling
  - Most simple and easy to set-up
  - e.g. 모든 인스턴스의 평균 CPU 사용률이 40%가 되길 원할 때
- Simple / Step Scaling
  - custom policies using CloudWatch alarm triggering
  - e.g.  평균 CPU 사용량이 70% 이상이 되면 2개의 instance를 추가
- Scheduled Actions
  - Anticipate a scaling based on known usage partterns
  - e.g. 예정된 이벤트 대비

Predictive Scaling

- Predictive scaling: continuously forecast load and schedule scaling ahead

Good metrics to scale on

- CPUUtilization: 총 평균 CPU 사용률
- RequestCountPerTarget: EC2는 대상당 1000개의 요청까지만 최적 작동하므로 스케일링을 위한 지표가 될 수 있음
- Average Network In / Out
- Any custom metric (push using CloudWatch)

Scaling Cooldowns

- 스케일링 이후에 cooldown period를 가짐 (defualt 300 seconds)
- 지표 안정화를 위해서 이 기간동안 ASG가 추가 인스턴스를 실행 또는 종료할 수 없음

## S3

### Bucket

- **globally unique name**
- **defined at the region level**
- Naming convention
  - no uppercase
  - no underscore
  - 3-63 characters long
  - Not an IP
  - Must start with lowercase letter or number

### Objects

- **Object=file**
- 다음 정보들로 이루어짐
  - Objects (files) have a key e.g. s3://some-bucket/some_file.txt
  - **Object values are the content of the body**
    - Max Object Size is 5TB
    - if uploading more than 5GB, must use `multi-part upload`
  - **Metadata** (list of text key/value pairs - system or user metadata)
  - **Tags** (Unicode key / value pair - up to 10) - useful for security/lifecycle
  - **Version ID** (if versioning is enabled)

### Versioning

- you can bucket level versioning
- suspending versioning does not delete the previous versions
- Any file that is not versioned prior to enabling versioning will have version `null`

### S3 Encryption

- method
  - `SSE-S3`: encrypt S3 obejcts using keys handled & managed by AWS
    - SSE = Server Side Encryption
    - AES-256 encryption
    - object upload 시에 header에 `“x-amz-server-side-encryption": “AES256”` 를 추가하면 S3에서 관리하는 키로 암호화
  - `SSE-KMS`: leverage AWS Key Management Service to manage encryption keys
    - KMS 이용한 암호화
    - set header  `“x-amz-server-side-encryption": “aws:kms”`
  - `SSE-C`: when you want to manage your own encyption keys
    - must HTTPS
  - `Client Side Encryption`

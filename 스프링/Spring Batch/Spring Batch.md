- [배치란?](#배치란)
- [스프링 배치란?](#스프링-배치란)
- [스프링 배치 아키텍쳐](#스프링-배치-아키텍쳐)

# 배치란?

- 큰 단위의 작업을 일괄 처리
- 대부분 처리량이 많고 비 실시간성처리에 사용
  - 대용량 데이터 계산, 정산, 통계, 데이터베이스, 변환 등
- 컴퓨터 자원을 최대로 활용
  - 컴퓨터 자원 사용이 낮은 시간대에 배치를 처리
  - 배치만 처리하기 위해서 사용자가 사용하지 않는 또 다른 컴퓨터 자원 사용 
- 사용자 상호작용으로 실행되기보단, 스케쥴러와 같은 시스템에 의해 실행되는 대상
  - crontab, jenkins... 등을 이용

# 스프링 배치란?

- 배치 처리를 하기 위한 Spring Framework 기반 기술
  - Spring에서 지원하는 기술 적용 가능
  - DI, AOP, 서비스 추상화 가능
- 스프링 배치의 실행 단위인 Job과 Step이 있음
- 비교적 간단한 작업(Tasklet) 단위 처리와, 대량 묶음(Chunk) 단위 처리

# 스프링 배치 아키텍쳐

![./Spring Batch/1.png](./Spring%20batch/1.png)

- `Job`
  - Job은 **배치의 실행 단위**를 의미하고, Job은 N개의 Step을 실행할 수 있으며, 흐름(FLOW)를 관리할 수 있음.
  - Job은 `JobLauncher`에 의해 실행됨
- `Step`
  - Step은 Job의 세부 실행 단위이고, N개 등록 가능
  - Step의 실행 단위는 크게 다음과 같음
    - Chunk 기반 : 하나의 큰 덩어리를 n개씩 나눠서 실행
    - Task 기반 : 하나의 작업 기반으로 실행
- `Chunk 기반 Step`
  - `ItemReader`, `ItemProcessor`, `ItemWriter`가 있음
    - Item은 **배치 처리 대상 객체**를 의미
  - `ItemReader`는 배치 처리 대상 객체를 읽어 **ItemProcessor 또는 ItemWriter에게 전달** ex) DB 데이터 조회, 파일 조회
  - `ItemProcessor`는 **input 객체를 output 객체로 filtering 또는 processing 해서 ItemWriter에게 전달** 
    - ItemProcessor는 optional. 즉 없어도 됨
    - ItemProcessor가 하는 작업은 ItemReader 혹은 ItemWriter가 대신할 수 있음
  - `ItemWriter`는 배치 처리 대상 객체를 처리 
    - ex) DB 업데이트 혹은 푸시 알림


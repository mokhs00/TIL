# kubernetes

- k8s(kubernetes) : k와 s 사이 문자수=8 -> k8s

- [kubernetes](#kubernetes)
  - [Architecture](#architecture)
    - [Desired State](#desired-state)
    - [Scheduler (Master)](#scheduler-master)
    - [Controller (Master)](#controller-master)
    - [etcd (Master)](#etcd-master)
    - [API server (Master)](#api-server-master)
    - [kubelet (Node)](#kubelet-node)
    - [proxy (Node)](#proxy-node)
  - [Objects](#objects)
    - [Pod](#pod)
    - [Pod 종류](#pod-종류)
    - [ReplicaSet](#replicaset)
    - [Deployment](#deployment)
  - [Service](#service)
    - [ClusterIP](#clusterip)
    - [NodePort](#nodeport)
    - [LoadBalancer](#loadbalancer)
    - [ETC](#etc)
    - [Ingress](#ingress)
  - [API](#api)
    - [Object Spec - YAML](#object-spec---yaml)
    - [API 호출하기란?](#api-호출하기란)
  - [ref](#ref)

## Architecture

- k8s의 Architecture에 대해서 간단하게 알아봅니다

### Desired State

- 직역하면, 원하는 상태를 의미
- 미리 원하는 상태를 기술하고, 해당 스펙을 기준으로 `Observe(관찰)`-> `Diff(차이점 발견)`->`Act(조치)`->`Loop(반복)` 과정을 거쳐 서버를 관리함
- kubernetes도 내부적으로 해당 방식을 사용

### Scheduler (Master)

- 새로 생성된 Pod를 감지하고 실행할 노드를 선택
- 노드의 현재 상태와 Pod의 요구사항 체크
  - 노드에 라벨(label) 부여

### Controller (Master)

- 각각의 Desired State를 가지며 Desired State를 기반으로 컨테이너의 상태를 체크하는 역할
- 논리적으로 다양한 컨트롤러가 존재
- ex) CI/CD Controller, Custom Controller, any...
- 끊임없이 상태를 체크하고 원하는 상태를 유지
- 복잡성을 낮추기 위해 하나의 단일 프로세스로 실행됨

### etcd (Master)

- 모든 상태와 데이터를 저장
- 분산 시스템으로 구성하여 안정성을 높임 (고가용성) -> 백업을 위해 여러 인스턴스에서 운영
- 가볍고 빠르면서 정확하게 설계 (일관성)
- key(directory)-value 형태로 데이터 저장
- TTL(tiem to live), watch같은 부가 기능 제공
- 매우 중요한 데이터이기에 백업 필수

### API server (Master)

- 모든 제어는 API server를 통함(Master)
- 상태를 바꾸거나 조회
- etcd와 유일하게 통신하는 모듈
- REST API 형태로 제공
- 권한을 체크하여 적절한 권한이 없을 경우 요청을 차단
- 관리자 요청 뿐만 아니라 다양한 내부 모듈과 통신
- 수평으로 확장되도록 디자인

### kubelet (Node)

- 각 노드에서 실행
- Pod를 실행/중지, health check
- CPI(Container Runtime Interface)
- Containter를 Pod으로 wrapping

### proxy (Node)

- 네트워크 프록시와 부하 분산 역할
- 성능상의 이유로 별도의 프록시 프로그램 대신 iptables 또는 IPVS를 사용(설정만 관리하는 서버)

## Objects

### Pod

- k8s에서 가장 작은 배포 단위
- 전체 클러스터에서 고유한 IP가 할당됨
- 여러 개의 컨테이너가 하나의 Pod에 속할 수 있음

### Pod 종류

- `DAEMON SET` : 모든 노드에 하나만 띄우고 싶은 Pod
- `REPLICASET` : 여러개의 Pod를 관리
- `DEPLOYMENT` : REPLICASET의 상위 개념
- `STATEFUL SETS` : 순서대로 Pod 생성 or 같은 볼륨 재활용
- `JOB` : 한 번 실행 후 종료

### ReplicaSet

- 여러개의 Pod를 관리
- 새로운 Pod는 template을 참고하여 생성
- 신규 Pod을 생성하거나 기존 Pod를 제거하여 원하는 수(Replicas)를 유지

### Deployment

- ReplicaSet의 배포 버전 관리
- ReplicaSet의 설정을(replicas 수 등) 이용해 버전 관리 -> k8s가 컨테이너를 auto recovery하는 특징을 이용


## Service

### ClusterIP

- 클러스터 내부에서 이용하는 프록시
- 로드밸런싱
- Pod 재생성 시 IP가 변경되기 때문에 IP를 고정하기 위해서 프록시 용도로 사용함
- 클러스터 내부에서 서비스 연결은 DNS를 이용

### NodePort

- cluster IP는 내부용으로 외부에서 접근 불가능
- NodePort는 외부에서의 접근을 위해 port를 열어주기 위한 리버스 프록시

### LoadBalancer

- NodePort가 종료될 위험이 있기 때문에 한 단계 더 로드밸런서를 이용

### ETC

- Volume : Storage(EBS...)
- Namespace : 논리적인 리소스 구분
- ConfigMap/Secret : 설정
- ServiceAccount : 권한 계정
- Role/ClusterRole : 권한 설정
- ...

### Ingress

- 도메인 또는 경로별 라우팅
- 도메인을 보고 내부 Service로의 연결 분기 가능
- Ingress는 NodePort, LoadBalancer를 포함함

## API

### Object Spec - YAML

- Spec을 작성하는 방법은 대부분 같은 형식이고, 아래 몇가지 예시를 보면 비슷한 걸 알 수 있다
- `apiVersion` : apps/v1, batch/v1, networking.k8s.io/v1....
- `kind` : Pod, Deployment, Service, Ingress...
- `spec` : 각종 설정
- `status(read-only)` : 시스템에서 관리한느 최신 상태

- one Pod

``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: example
spec:
  containers:
  - name: busybox
    image: busybox:1.25
```

- ReplicaSet

``` yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: web
        image: image:v1
```

- ArgoCD

``` yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: guestbook



```

### API 호출하기란?

- 원하는 상태(Desired State)를 다양한 오브젝트(Object)로 정의(Spec)하고 API서버에 yaml 형식을 전달
- 이후는 쿠버네티스가 해당 yaml 파일을 기반으로 동작한다

## ref

- https://subicura.com/k8s/guide/
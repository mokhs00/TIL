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
  - [kubectl](#kubectl)
  - [How to write config yaml](#how-to-write-config-yaml)
    - [Pod config : livenessProbe](#pod-config--livenessprobe)
    - [Pod config : readinessProbe](#pod-config--readinessprobe)
    - [Pod config : livenessProbe + readinessProbe](#pod-config--livenessprobe--readinessprobe)
    - [Pod config : multi containers](#pod-config--multi-containers)
    - [ReplicaSet config](#replicaset-config)
    - [Deployment config](#deployment-config)
    - [Deployment config : rolling update](#deployment-config--rolling-update)
    - [Service config](#service-config)
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
- `status(read-only)` : 시스템에서 관리하는 최신 상태

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

## kubectl

- [kubectl commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-)

| command  | description                                                                                                                                                                                   | example                                                                                            |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| apply    | 원하는 상태 적용 -f 옵션(=파일기반 적용)                                                                                                                                                      | `kubectl apply -f mokhs-k8s.yml`                                                                   |
| get      | 리소스 리스트 출력                                                                                                                                                                            | `kubectl get` `kubectl get pod -o json`(json으로 출력) `kubectl get pod --show-labels`(labels조회) |
| describe | 리소스의 상태 상세정보 출력                                                                                                                                                                   | `kubectl describe pod/wordpress-12f21331r1xsds`                                                    |
| delete   | 리소스 제거                                                                                                                                                                                   | `kubectl delete pod/wordpress-12f21331r1xsds`                                                      |
| logs     | 컨테이너 로그 보기                                                                                                                                                                            | `kubectl logs -f pod/wordpress-12f21331r1xsds` (-f 실시간 로그)                                    |
| exec     | 컨터이너에 명령어 전달 `kubectl exec [-it] [POD_NAME] -- [COMMAND]`(컨테이너 상태를 직접 쉘로 접속하고 싶은 경우 `-it` option 사용, 여러 개의 컨테이너를 지정하고 싶은 경우 `-c` option 사용) | `kubectl exec -it wordpress-32rqfweadaf23 -- bash` (bash 접속)                                     |
| config   | kubectl 설정 관리                                                                                                                                                                             | `kubectl config current-context`                                                                   |

## How to write config yaml

- k8s에서 리소스를 관리할 떄는 name과 label을 이용
- keyword
  - `version` : 오브젝트 버전 ex) v1, app/v1, networking.k8s.io/v1, ...
  - `kind` : 종류 ex) Pod, ReplicaSet, Deployment, Service, ...
  - `metadata` : 메타데이터 ex) name, label, annotation, ...
  - `spec` : 상세 명세 (리소스마다 다르므로 해당 리소스에 맞게 기술)

### Pod config : livenessProbe

- 컨테이너가 정상적을 동작하는지 체크하고 정상적으로 동작하지 않는다면 컨테이너 재시작

``` yml
apiVersion: v1
kind: Pod
metadata:
  name: echo
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      livenessProbe: 
        httpGet:
          path: /not/exist
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 2
        periodSeconds: 5
        failureThreshold: 1
```

### Pod config : readinessProbe

- 컨테이너가 준비되었는지 체크하고 정상적으로 준비되지 않았다면 Pod으로 들어오는 요청을 제외
- `livenessProbe`와 달리 Pod를 재시작하지 않고 요청만 제외함

``` yml
apiVersion: v1
kind: Pod
metadata:
  name: echo
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      # livenessProbe: 
      readinessProbe:
        httpGet:
          path: /not/exist
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 2
        periodSeconds: 5
        failureThreshold: 1
```

### Pod config : livenessProbe + readinessProbe

- 보통 `livenessProbe`와 `readinessProbe`를 같이 적용하고, 애플리케이션 환경에 따라 적절하게 조절함

``` yml
apiVersion: v1
kind: Pod
metadata:
  name: echo
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      livenessProbe:
        httpGet:
          path: /
          port: 3000
      readinessProbe:
        httpGet:
          path: /
          port: 3000
```

### Pod config : multi containers

- `1 Pod = 1 Container`가 대부분이지만, 단일 Pod에서 여러 개의 컨테이너를 가질 수도 있다
- 또한 한 Pod에 속한 컨테이너는 서로 localhost로 네트워크, 리소스를 공유할 수 있다(env 등)
- 다음 yml로 `kubectl apply` 하게 된다면 counter Pod 안에서 `app`, `redis` container가 생성되고, 서로 env를 localhost를 통해 통신이 가능하며, env를 공유한다

``` yml
apiVersion: v1
kind: Pod
metadata:
  name: counter
  labels:
    app: counter
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/counter:latest
      env:
        - name: REDIS_HOST
          value: "localhost"
    - name: db
      image: redis
```

### ReplicaSet config

- ReplicaSet = 복제셋
- Pod를 단독으로 만들면 Pod에 문제 발생 시 자동으로 복구되지 않음
- ReplicaSet은 Pod를 정해진 수만큼 복제하고 관리함
- `spec.template`은 생성할 Pod에 대한 명세이다
- ReplicaSet Controller는 `spec.selector` 옵션을 기준으로 API Server에 Pod를 수를 조회하는 요청을 주기적으로 보내 이를 검사하고, `spec.replicas` 수 만큼 Pod를 생성하거나 제거해서 유지함
- 이후 `Scheduler`는 API Server를 감시하며, 할당되지 않은 Pod가 있는지 체크하고, 이를 적절한 노드에 배치함
- 즉. ReplicaSet은 `ReplicaSet Controller`가 관리하고, Pod의 할당과 관련된 건 `Scheduler`가 관리한다
- 보통 ReplicaSet을 단독으로 사용하는 경우는 없고, 주로 Deployment를 사용한다 그리고 Deployment를 설정하여 내부적으로 ReplicaSet을 사용한다
- 다음은 config 예시이다

``` yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: echo-rs
spec:
  replicas: 1     # replicas 1개를 기준으로 Pod를 유지
  selector:       # Pod의 식별자
    matchLabels: 
      app: echo 
      tier: app
  template:       # Pod 명세
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1

```

### Deployment config

- k8s에서 가장 널리 사용되는 Object, Pod를 업데이트하고 이력을 관리하여 rollback 혹은 특정 버전(revision)으로 돌아갈 수 있음
- 기존 Pod를 하나씩 종료하고 하나씩 다시 생성한다 -> 이는 사람 눈에는 update 하는 걸로 보인다
- config는 type을 제외하고 ReplicaSet과 동일하다
- 다음과 같이 `kubectl describe deployment-NAME`을 통해 deploymenet-controller에 의해서 각 Replica가 가 scaled up, down 되는 걸 볼 수 있다

```
❯ kubectl describe deploy/echo-deploy
Name:                   echo-deploy
Namespace:              default
CreationTimestamp:      Wed, 06 Apr 2022 22:03:25 +0900
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=echo,tier=app
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=echo
           tier=app
  Containers:
   echo:
    Image:        ghcr.io/subicura/echo:v2
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   echo-deploy-77cd7699f4 (4/4 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m14s  deployment-controller  Scaled up replica set echo-deploy-76dcd9f4f9 to 4
  Normal  ScalingReplicaSet  91s    deployment-controller  Scaled up replica set echo-deploy-77cd7699f4 to 1
  Normal  ScalingReplicaSet  91s    deployment-controller  Scaled down replica set echo-deploy-76dcd9f4f9 to 3
  Normal  ScalingReplicaSet  91s    deployment-controller  Scaled up replica set echo-deploy-77cd7699f4 to 2
  Normal  ScalingReplicaSet  84s    deployment-controller  Scaled down replica set echo-deploy-76dcd9f4f9 to 2
  Normal  ScalingReplicaSet  84s    deployment-controller  Scaled up replica set echo-deploy-77cd7699f4 to 3
  Normal  ScalingReplicaSet  83s    deployment-controller  Scaled down replica set echo-deploy-76dcd9f4f9 to 1
  Normal  ScalingReplicaSet  83s    deployment-controller  Scaled up replica set echo-deploy-77cd7699f4 to 4
  Normal  ScalingReplicaSet  83s    deployment-controller  Scaled down replica set echo-deploy-76dcd9f4f9 to 0
```

- 다음은 버전관리를 위한 명령어들이다

``` bash
# history 확인
kubectl rollout history deployment-NAME
# revision 1 히스토리 상세 확인
kubectl rollout history deployment-NAME --revision=1
# 바로 이전으로 rollback
kubectl rollout undo deployment-NAME
# 특정 버전으로 rollback
kubectl rollout undo deployment-NAME --to-revision=2
```

- config는 다음과 같다

``` yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-deploy
spec:
  replicas: 4
  selector:
    matchLabels:
      app: echo
      tier: app
  template:
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v2
```

### Deployment config : rolling update

- Deployment에는 다양한 배포전략이 있는데, RollingUpdate에 대해서 알아보자
- RollingUpdate config는 다음과 같다

``` yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-deploy-st
spec:
  replicas: 4
  selector:
    matchLabels:
      app: echo
      tier: app
  minReadySeconds: 5
  strategy:
    type: RollingUpdate # RollingUpdate options
    rollingUpdate:
      maxSurge: 3
      maxUnavailable: 3
  template:
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1
          livenessProbe:
            httpGet:
              path: /
              port: 3000
```

### Service config

- Service를 이용해 Pod를 노출하고 클러스터 외부에서 접근할 수 있음
- Pod는 자체 IP를 가지고 다른 Pod와 통신할 수 있지만, 사라지고 생성되는 과정이 빈번하기 때문에 권장하지 않음
- k8s는 Pod와 직접 통신하는 방법 대신, 별도의 고정된 IP를 가진 서비스를 만들고 그 서비스를 통해 Pod에 접근하는 방식을 사용함
- 노출범위에 따라 ClusterIP, NodePort, LoadBalancer 등으로 타입이 나눠짐

## ref

- <https://subicura.com/k8s/guide/>

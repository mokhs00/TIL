# 개요

패스트캠퍼스 Spring Cloud 강의를 학습하고 내용을 정리합니다.

# Spring Cloud Gateway

- 하나의 진입점을 제공하기 위함
- zull과 spring cloud gateway가 있음


# Service Discovery pattern

- MSA와 같은 분산 환경은 서비스 간의 원격 호출로 구성되는데, 인스턴스의 수가 다양하고, IP가 동적으로 변경될 수 있는 상황에서 서비스 클라이언트가 서비스의 IP와 port를 알아낼 수 있는 방안이 필요한데, 이 방안으로 나온 방법이 바로 `Service Discovery`라고 한다.
- `Service Discovery`는 `Service registry`라는 것에 해당 서비스들의 IP와 port를 미리 등록해놓고, 클라이언트 서버에서 `사용하려는 서비스가 어디있는지 물어보는 요청`이 왔을 때 미리 등록된 주소 중에서 해당 서비스를 찾고 위치(IP, port)를 알려준다.
- 그리고 `Service Discovery pattern`은 `server side` 방식과 `client side` 방식으로 나뉜다.

## server side and client side : 



# Service Registry

- Discovery pattern에서 각 서비스의 IP와 port 정보를 등록해놓는 `service registry`가 필요하다. service registry로는 다음과 같은 방안을 선택할 수 있다.

- Eureka
- zookeeper
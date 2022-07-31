# 개요

- Docker에 대해 학습하고 정리합니다

## Containers(e.g.Docker) vs Virtual Machines

- `Virtual Machine`
  - OS 의존성이 크고, 속도가 상대적으로 느리며, 많은 디스크 공간을 사용
  (Bigger impact on OS, slower, higher disk space usage)
  - 공유, 재구성, 분배가 가능하긴하나 상대적으로 복잡함(Sharing, re-building and distribution can be challenging)
  - 캡슐화가 가능하나 환경별로 캡슐화하는 게 아닌 컴퓨터 머신 전체를 캡슐화 해야함(Encapsulate "whole machines" instead of just apps/ environments)

- `Docker Container`
  - OS 의존성이 적고, 매우 빠르고 적은 디스크 공간을 사용함
  (Low impact on OS, very fast, minimal disk space usage)
  - docker image와 config 파일이 있기 때문에 공유, 재구성, 분배가 쉬움
  (Sharing, re-building and distribution is easy)
  - 컴퓨터 머신 전체를 캡슐화 하는 것이 아닌 apps/ 환경 기반으로 캡슐화 가능(Encapsulate apps/ environments instead of "whole machines")

## images vs Containers

- image는 container의 base가 되는 템플릿
- container는 실제 인스턴스로 실행되는 실체..

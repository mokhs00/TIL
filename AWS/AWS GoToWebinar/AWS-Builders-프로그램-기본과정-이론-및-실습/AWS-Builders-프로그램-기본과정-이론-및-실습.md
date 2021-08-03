# 모니터링

## Amazon SNS

![amazon-sns-logo.png](./image/amazon-sns-logo.png)

**Amazon SNS(SimpleNotification Service)** 는 애플리케이션 간 혹은 애플리케이션과 사용자 간 통신 모두를 위한 완전관리형 메시징 서비스!

Amazon SNS 주제를 사용하면, 게시자 시스템에서 병렬 처리를 위해 Amazone SQS 대기열, AWS Lambda, HTTPS 엔드포인트를 비롯한 많은 구독자 시스템으로 메시지를 팬아웃할 수 있음.

![Amazon-SNS](./image/Amazon-SNS.png)

## Amazon CloudWatch

![Amazon-CloudWatch-logo.png](./image/Amazon-CloudWatch-logo.png)

**Amazon CloudWatch** 는 **모니터링 및 관찰 기능 서비스**임.
CloudWatch는 애플리케이션을 `모니터링`하고, 시스템 전반의 `성능 변경 사항에 대응`하며, 리소스 사용률을 최적화하고, **운영 상태에 대한 통합된 보기를 확보하는데 필요한 데이터와 실행 가능한 통찰력을 제공함**.

CloudWatch는 로그, 지표 및 이벤트 형태로 모니터링 및 운영 데이터를 수집하여 AWS와 온프레미스 서버에서 실행되는 AWS 리소스, 애플리케이션 및 서비스에 대한 통합된 보기를 제공함.

CloudWatch를 사용하여 환경에서 **이상 동작을 감지**하며, **경보를 설정**하고, **로그와 지표를 나란히 시각화**하며, **자동화된 작업을 수행**하고, 문제를 해결하며, 통찰력을 확보하여 애플리케이션을 원활하게 실행할 수 있음.

## Amazon EC2 Auto Scaling

![amazon-auto-scaling.png](./image/amazon-auto-scaling.png)

**Amazon EC2 Auto Scaling**을 통해서 애플리케이션의 로드를 처리할 수 있는 정확한 수의 EC2 인스턴스를 보유하도록 보장할 수 있음.
**Auto Scaling 그룹**이라는 EC2 인스턴스 모음을 생성해서 사용함. 각 Auto Scaling 그룹의 최소/최대 인스턴스 수를 지정할 수 있고, 그룹의 크기가 최소/최대 사이 값을 유지함. 또한 **조정 정책**을 지정했다면 Amazon EC2 Auto Scaling에서는 애플리케이션의 변동되는 수요에 따라 인스턴스를 시작하거나 종료할 수 있음!

# Reference

https://aws-builders-kr.workshop.aws/ko/

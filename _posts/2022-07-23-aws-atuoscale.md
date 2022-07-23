---
layout: article
title: AWS 서비스 - 오토 스케일링 (Auto Scaling)
mode: overlay
header: 
  theme: dark
article_header:
  type: cover
  theme: dark
  image:
    src: /assets/aws/ec2.png
show_author_profile: false
comment: true
mathjax: true
mathjax_autoNumber: true
tags: infra
---

# Amazon Web Service
웹 또는 앱 서비스를 운영하다보면 클라이언트의 요청이 급격히 많아 지는 경우가 발생합니다. 만약 우리집에 있는 서버로 서비스를 운영하는 경우라면, 요청이 많아지는 순간 서버가 다운되거나 응답까지 지연이 발생하게 됩니다. 그렇다고 당장 용산에 달려가 여분의 서버를 사오는 것이 효율적일까요? 만약 요청이 많아지는 시기가 불규칙적이라면, 즉 특정 시간이나 특정 날짜에만 요청이 많아 지는 것이라면 여분의 서버는 대부분의 시간을 아무 연산도 안하고 방치될 가능성이 많습니다. 따라서 여분의 서버를 사는 것은 비효율적입니다.     
아마존 웹 서비스 (AWS)는 컴퓨팅 자원, 데이터 베이스, 저장 공간 등 다양한 클라우드 서비스를 제공합니다. 저번 시간에 알아본 컴퓨팅 자원 서비스 [AWS Computing 링크](https://christinarok.github.io/2022/07/09/aws.html)에 이어 이번에는 클라이언트의 요청 개수 또는 서버의 CPU 사용량에 따라 서버의 개수를 자동으로 늘려주는 오토 스케일링 (Auto Scaling) 서비스를 알아보겠습니다.


# Auto Scaling
AWS의 오토 스케일링 서비스로는 EC2 Auto Scaling Group과 ELB(Elastic Load Balancing)이 있습니다. 이 외에도 AWS 서비스 자체에서 스케일링을 할 수 있는 기능을 사용해 스케일링을 할 수 있습니다. AWS 서비스 대부분이 EC2 인스턴스를 사용하기 때문에 오늘은 이 두가지 서비스를 중점적으로 알아보겠습니다. 

## EC2 Auto Scaling Group
> EC2 컴퓨팅 서비스를 처음 들어보신 분이라면 [EC2 링크](https://christinarok.github.io/2022/07/09/aws.html#ec2)의 설명을 참고해주세요.

앞선 예시에서 클라이언트 요청 개수가 증가하면 서버를 추가해야한다고 설명했습니다. "클라이언트 요청 개수"를 서버 또는 서비스의 로드(Load)라고 합니다. 즉, 서버나 서비스가 처리해야 되는 일의 양을 말합니다. EC2 Auto Scaling Group은 EC2 인스턴스의 로드를 측정하고 로드가 많은 경우 자동으로 인스턴스를 추가하는 horizontal scaling을 실행합니다.    

EC2 서비스에서 여러 인스턴스를 사용하고 있다면, 인스턴스를 논리적 그룹으로 나눠 스케일링을 관리할 수 있습니다. 그룹 단위로 관리 및 설정이 가능하기 때문에 auto scaling group이라고 불립니다.    

그룹별로 최대(max), 최소(min), 최적(desired)의 인스턴스 개수를 설정할 수 있습니다.    

<figure>
  <img src="/assets/aws/autoscale/ec2group.png" width="400" height="300">
</figure>

위의 예시 그룹은 최소 1개의 인스턴스, 최대 4개의 인스턴스, 그리고 최적으로 2개의 인스턴스를 설정했습니다. 따라서 해당 그룹의 인스턴스는 최소 1개의 인스턴스는 가지되, 보통 2개의 인스턴스를 가지며, 만약 로드가 증가하면 최대 4개의 인스턴스까지 증가할 수 있습니다. 따라서 서비스를 하는 중에 인스턴스가 오류로 중단되도(terminated) 자동으로 1개의 인스턴스가 다시 실행되기 때문에 어느 경우에도 적어도 1개의 인스턴스는 실행 중이라는 것을 장담할 수 있습니다.    

EC2 auto scaling group의 스케일링 정책(scaling policy)를 알아봅시다. 여기서 정책이란 인스턴스를 늘리고 줄일 기준, 즉 metric을 의미합니다. 스케일링 정책은 3가지로 나뉩니다.

- Manual
  - 최대, 최소, 최적의 인스턴스 개수만 설정합니다.
- Schedule 
  - 만약 서비스에 로드가 많아 지는 상황, 예를 들면 요청이 급격히 많아지는 시간대나 날짜를 알고 있는 상황일 경우 해당 시간대나 날짜에 스케일링이 되도록 설정할 수 있습니다. 
  - 예측 가능한 상황을 대비한다는 측면에서 proactive 방식이라고 부릅니다.
- Dynamic
  - Schedule과 달리 로드가 언제 증가할지 모르는 상황에서 적절한 CPU 사용률 등을 설정해놓는 방식입니다. 예를 들어 CPU 사용률을 50%대로 설정했다면 인스턴스의 CPU 사용률이 50% 이상으로 높아질 때 새로운 인스턴스가 실행됩니다. 
  - 예측 불가능 상황에 대처한다는 측면에서 reactive 방식이라고 부릅니다. 

EC2 Auto Scaling Group은 별도의 사용료가 없습니다. EC2, EBS(Storage) 그리고 모니터링 서비스인 CloudWatch 서비스의 사용료만 지불하면 별도의 추가 비용 없이 사용할 수 있습니다.     


## ELB (Elastic Load Balacer)
로드 밸런서는 트래픽을 서버에 분배해주는 역할을 합니다. 이를 traffic routing이라고 합니다. 웹 서버로 많이 사용되는 Nginx도 로드 밸러서 기능을 제공합니다. 즉, 클라이언트의 http 요청을 받아 이를 처리할 수 있는 서버 및 타켓을 골라 요청을 보냅니다.    

<figure>
  <img src="/assets/aws/autoscale/loadbalancer.png" width="400" height="300">
</figure>

로드 밸런서의 장점은 다음과 같습니다. 
- 응답 속도 증가 
  - 요청이 한 번에 많이 들어와도 로드 밸러서가 요청을 적절히 분배하기 때문에 요청-응답 속도에 지연이 줄어듦니다. 
- 시간 당 처리량 증가
  - 시간 당 처리량을 throughput이라고 합니다. 로드 밸런서는 요청이 많을 시 사용 가능한 하드웨어 자원 또는 타겟에게 요청을 고루 분배하기 때문에 시간 당 처리량이 증가합니다. 
- 하드웨어 자원 사용 최적화
- Fault tolerant 서비스 구축 가능
  - 하나의 서버에 장애가 발생해도 (fault) 서비스에 전혀 지장을 주지 않도록 설계된 시스템을 fualt tolerant라고 합니다. 로드 밸런서는 요청을 여러 서버에 분배하기 때문에 하나의 서버에 장애가 발생해도 다른 서버가 요청을 수행할 수 있습니다. 

로드 밸런서의 요청 분배 방식은 다음과 같습니다.   
- Round-robin
  - 요청이 입력되면 인스턴스에 순서대로 할당합니다. 모든 인스턴스가 같은 우선 순위를 갖기 때문에 인스턴스가 비슷한 하드웨어 스펙을 가지고 있는 경우 주로 사용되는 방식입니다. 
- Least-connected   
  - 요청이 입력되면 처리률이 가장 적은 서버에게 요청을 할당합니다.
- Ip-hash
  - 요청을 hash 함수를 이용해 hash 값으로 변환한 후 hash 값과 맵핑된 인스턴스에 할당합니다. 

EBS는 Auto Scaling Group(인스턴스읜 논리적 그룹)별로 설정이 가능합니다.      

마지막으로 EBS의 로드 밸런서 타입을 알아보겠습니다.   
- 어플리케이션 로드 밸런서 (L7)
  - 어플리케이션 레이어에서 발생하는 로드를 서비 및 타겟에 분배합니다. 
- 네트워크 로드 밸런서 (L4)
  - 네트워크 계증 (IP)나 트랜스포트 계층 (TCP, UDP)의 로드를 서버 및 타겟에 분배합니다. 



# Conclusion
AWS에서 제공하는 Auto Scaling 서비스 중 EC2 auto scaling group과 ELB를 알아봤습니다. 클라우드 서비스의 최대 장점은 유연한 스케일링을 통한 처리 속도 및 성능 향상입니다. 따라서 Auto Scaling 서비스를 자신의 서비스에 맞게 사용한다면 클라우드 서비스의 장점을 최대로 활용할 수 있습니다. 다음 시간에는 AWS의 저장 공간 서비스와 각종 로그를 트래킹할 수 있는 트래킹 서비스에 대해 알아보겠습니다. 


# Reference
- [AWS Scaling Documentation](https://docs.aws.amazon.com/autoscaling/?id=docs_gateway)
- [AWS EC2 Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- [AWS Scaling Policy](https://docs.aws.amazon.com/autoscaling/ec2/userguide/scale-your-group.html#scaling-options)
- [AWS ELB](https://docs.aws.amazon.com/autoscaling/ec2/userguide/autoscaling-load-balancer.html)
- [로드 밸런싱 설명 참고 블로그](https://www.stevenjlee.net/2020/06/30/%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EC%9D%98-%EB%B6%80%ED%95%98%EB%B6%84%EC%82%B0-%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8B%B1-load-balancing-%EA%B7%B8/)

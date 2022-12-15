---
title: "[AWS] Computing power(Server)"
author: "김소연"
date: 2022-12-11 21:00:00 -0500
categories: [Cloud, AWS]
tags: [AWS]
---



이 글은 원티드의 ''프리온보딩 백엔드 챌린지 12월 - 백엔드 인프라 설계 w AWS" 

2번째 강의를 듣고 작성한 글입니다.

정확하지 않은 정보가 있다면 댓글 부탁드립니다.




## AWS 인프라의 전체적인 모습

1. 인프라 관련 요소들
     AWS API Gateway, AWS S3, AWS ELB, AWS CloudFront, AWS Secret Manager, 스냅샷

2. **컴퓨팅 파워(서버)**
     **AWS EC2, AWS Elastic Beanstalk, AWS ECS, AWS Fargate, AWS Lambda(Serverless)**

3. Database
     AWS RDS, AWS DynamoDB, AWS ElastiCache

4. Message Queue - 대용량 데이터 처리 시 필요
     AWS SQS, AWS MSK, AWS Kinesis





# AWS 컴퓨팅 파워(서버)

## AWS EC2 (Elastic Cloud Compute)

가장 기본적인 형태의 클라우드 컴퓨팅 (= 클라우드 컴퓨터 한대)

#### 용어정리

- 온디맨드 : 선결제 금액이나 장기 약정 없이 저렴하고 유연하게 Amazon EC2를 사용하기 원하는 사용자
- 스팟 인스턴스 : 시작 및 종료 시간이 자유로운 애플리케이션
- Saving Plans : 1년 또는 3년 기간의 일정 사용량 약정을 조건으로 EC2 및 Fargate 사용량에 대해 저렴한 요금을 제공하는 유연한 요금



## AWS Elastic Beanstalk (EB)

AWS 클라우드에서 애플리케이션을 신속하게 배포하고 관리할 수 있는 서비스 
(애플리케이션 코드를 업로드하기만 하면 작동)

- ELB에 EC2를 연결하고, EC2에는 VPC와 SG 설정을 해주는 과정을 묶어서 사용할 수 있게 해준다. 
  (DB나 MessageQueue는 별개)
- Elastic Beanstalk = EC2 + 배포 버전 관리(롤백) + Elastic Load Balancer + 모니터링 + 로그 트래킹 + 오토스케일링


- 다양한 언어를 지원한다. (.Net, PHP, Java, Ruby, Node.js, Python, Docker, Go)
- 'Elastic Beanstalk -> 업데이트 및 배포'를 통해 애플리케이션 파일들을 올린다.



## AWS ECR (Elastic Container Registry)

안전하고 확장 가능하고 신뢰할 수 있는 AWS 관리형 컨테이너 이미지 레지스트리 서비스

#### 서비스 비교

- RDS(DB) : 데이터를 저장하는 장소
- S3 : 사진, 이미지 등 파일을 저장하는 장소
- ECR : **Docker 이미지를 올리고 관리할 수 있는 서비스**



## AWS ECS (Elastic Container Service)

AWS에서 제공하는 컨테이너 오케스트레이션 서비스

- 여러 애플리케이션 컨테이너를 쉽고 빠르게 실행하고, 컨테이너를 적절하게 분배 및 확장 & 축소할 수 있도록 도와주는 서비스
  - 오케스트레이션 : 서버를 늘리고 줄이는 행위
- AWS EC2와 AWS Fargate 중 원하는 환경에서 실행 가능
  - 사용량에 따라서 EC2를 더 늘리거나 줄이는 역할을 해준다.

#### 용어정리

![aws](/assets/img/aws2.JPG){: width="60%" height="60%"}

- Task Definition : Task 설정(컨테이너의 이미지, CPU/메모리 리소스 할당 설정, port 매핑, volume 설정)
- Task : Task 안에는 한 개 이상의 컨테이너들이 포함되어 있으며, ECS에서 컨테이너를 실행하는 최소 단위는 Task이다. (인스턴스화)
- Service : Task들이 여러 개 묶인 것. Task들의 Life cycle을 관리하고, 오토 스케일링과 로드밸런싱을 관리한다.
- Cluster : Service들이 여러 개 묶인 것. Task가 배포되는 Container Instance들은 논리적인 그룹이다.



## Amazon Fargate

EC2 없이도 컨테이너를 실행할 수 있게 해주는 서비스

- 컨테이너를 실행하기 위해서 컨테이너를 실행할 Instance(EC2)를 실행할 필요없이, Docker 파일을 AWS 자체적으로 실행할 수 있게 해준다.


- Fargate를 사용하면 컨테이너를 실행하기 위해 가상 머신의 클러스터를 프로비저닝, 구성 또는 조정할 필요가 없다.
  - 따라서 서버 유형을 선택하거나, 클러스터를 조정할 시점을 결정하거나, 클러스터 패킹을 최적화할 필요가 없다.

#### 아직도 기업들이 Fargate 대신 EC2를 사용하는 이유

가격 (Fargate가 약 4배 더 비싸다.)



## Amazon Lambda ✨

서버 없이도 코드를 실행시킬 수 있는 서버리스 컴퓨팅 서비스

- 코드를 돌리기 위한 리소스를 임의로 지정할 수 있으며, `사용리소스 * 사용 시간`에 따라 과금이 된다. (ex. 메모리 용량 /  코어 갯수)
- 최대 30분 / 최대 10GB / 최대 6개의 Core
  - 오래 걸리는 작업들은 Fargate를 사용(30분 뒤에 꺼지기 때문에)

#### 사용 예시

- 비동기 처리 (이미지 썸네일 생성)
  - APP에서 이미지를 S3에 저장하고, 이 때 Lambda가 실행되어 썸네일을 만들어 S3에 저장한다.
  - 이미지를 S3에 저장할 때 Presigned URL을 통해 저장
- 예측이 불가능한 리소스 사용 (대용량 처리 / 머신러닝)

→ Lambda를 사용해서 작업을 수행하여 서버와 직접 통신하지 않게하고 서버에 과부하가 걸리지 않도록 한다.

#### 장점

요금이 매우 저렴하다. 
코드가 돌아갈 때만 비용이 과금되기 때문에 효율적이다.

#### 왜 항상 켜져있어야 하는 EC2 / Elastic Beanstalk 대신 가성비있는 Lambda를 선택하지 않을까?

- Lambda는 기본적으로 EC2와 같은 인스턴스보다 Latency가 훨씬 높다.

![aws](/assets/img/aws3.png){: width="60%" height="60%"}

- Lambda는 서버리스 형태로 되어있어서 오랜만에 호출하면 죽어있던 람다로 할당된 컴퓨터가 살아나는 과정이 필요하다. 이를 콜드 스타트라고 한다.
- 콜드 스타트(Cold Start)
  - 소스코드가 다운되고, 컨테이너가 실행되는 부분
  - Lambda를 사용하지 않는 유일한 이유
- 웜 스타트(Warm Start)
  - 소스코드가 실행되는 부분
- 5분이 지나면 AWS에서 코드를 관리하는 컨테이너가 꺼진다.
  - 계속 warm하게 유지하기 위해서는 5분마다 계속 실행시켜야 하는 방법 밖에 없다.
  - 그래서 일반서버보다는 배치서버에서 많이 사용한다.



## AWS Lambda - Serverless

서버를 람다에 올릴 수 있도록 하는 프레임워크

- AWS, Azure, GCP 등의 서버리스 서비스를 쉽게 사용할 수 있도록 도와주는 오픈소스 프레임워크
- 사용 가능 서버
  - Node 계열 - ExpressJS, NestJs 등
  - Java 계열 - Spring(Spring Cloud Function)
  - Python 계열 - FastAPI, Flask 등
- 소규모 / 이용자가 많지 않은 서비스에서 가격 효율적으로 이용 가능
  - 사이드 프로젝트인 경우 가볍게 Serverless로 올려서 사용할 수 있다. (가격 무료)





## 출처📎

- 프리온보딩 백엔드 챌린지 12월 - 백엔드 인프라 설계 w AWS
- https://dalgonakit.tistory.com/190



---
layout: post
title: 2018 데이터야놀자 세션 기록
category: Article
tags: ['Article', '데이터야놀자', 'Data', 'Analysis', 'Engineering']
---

## 세션

*한 시에 도착해서 하이퍼커넥트에서 제공한 샌드위치를 먹고 세션에 참여했습니다.*
*오후 세션부터 제가 들었던 세션들을 요약했습니다.*

#### 팀 단위의 마이크로 서비스 구축하기 - 엔씨소프트

* 데이터의 수: 게임의 수 * 라이브 횟수 * 지표 수
* 엔씨소프트의 ETL Stack
  *  ~2012: Bash, Pig, Hive (리니지/리니지2 운영)
  * Web 스택: Sqoop, Flume, Cascading  (아이온, 블레이드&소울)
  * Open API: Embulk, Spark, Spark Streaming(리니지M)
* Product가 개발될 때마다 맞는 스택을 쌓고 ETL 플랫폼 구현하는 데에 대해 회의감...!
  * 나쁜 시스템과 나쁜 개발환경, 손댈 때마다 에러 나는 코드가 나쁜 습관과 개발문화를 만든다
  * Micro Service에 대한 관심: 방대한 ETL 서비스를 작은 서비스들로 나누고, 서비스들 간의 Http 통신을 통해 ETL 서비스 운영
  * BigOwl: 8개의 마이크로 서비스를 Http 통신을 통해 운영, 데이터 수집, 상태관리, 모니터링 및 운영
    * 서비스 환경 및 개발 문화 변화
    * API를 통한 사내 팀과의 협업 및 서비스 연동
    * 게이트웨이 서비스
      * Discovery
      * Route
      * Event Based Arch
    * 서비스 상태 관리
      * 상태관리
      * 모니터링
      * 장애 복구
      * 엔씨소프트: 서비스 별로 상태관리/모니터링 방법이 달라 개발 비효율성
        * 모든 마이크로 서비스가 구현해야 할 공통 라이브러리 구현: Metadata, Status 컨트롤러
    * 운영 및 모니터링
      * Partitioned Parquet Read(Spark)
    * 수집 에이전트 
      * 수집 에이전트가 확장성을 가지지 못함
      * Node Cluster 활용

#### 카카오뱅크의 CRM 인프라 기획, 데이터 분석

* CRM: 기업과 소비자 간의 활동(접점)에 대한 관리 및 분석 과정
  * 데이터 기획/수집부터 분석, 리포트, 분석 파트의 설계, 고객행동모형 개발, 개인화 서비스(마케팅) 기획 및 구현까지 총괄
  * 카카오뱅크: 금융거래 시스템은 Oracle, 모바일 앱 데이터는 Mysql을 활용
    * Impala(Hadoop 내 실시간 SQL 질의) 통해 앱 로그와 거래 로그 등을 동시에 적재, Data Lake에 저장하여 확보
    * Oracle 금융거래 데이터, Mysql 회원 정보를 Impala에 적재
    * 캠페인(마케팅, 사용자에게 push) 시스템 자동화
    * CRM Mart 집계
    * 카카오뱅크사의 이벤트 운용 - 이벤트 웹 로그 ElasticSearch 적재
    * AppsFlyer를 통한 SNS, 포털에서의 카카오뱅크 관련 정보 확보
      * 이벤트 정보를 유저ID, 이벤트 외부 링크와 함께 Data Lake에 저장하기
      * AppsFlyer Link를 클릭할 경우 랜딩과 함께 사용자의 디바이스 정보/사용자 정보가 AppsFlyer 서버에 저장
      * AppsFlyer는 앱의 설치에 대한 성과만을 측정 - 이벤트 페이지에 대한 성과 측정은?
      * 이벤트 페이지에서의 source, medium, campaign값을 링크에서 파싱하여 앱 설치 페이지 링크에 추가(생성), 해당 이벤트 페이지의 정보가 앱 설치에까지 전달 가능
      * 앱 외부 SNS활동을 마케팅 또는 분석에 활용 가능
    * 코리안클릭 리서치사를 통한 경쟁사 정보 확보
  * 가장 끝 단인 마케팅에서 데이터를 어떻게 활용할 지 서비스 기획에서부터 결정해야 올바른 데이터 적재 및 활용이 가능!
    * CRM 팀과 개발조직, 데이터 엔지니어링 조직 간의 협업, 지원
    * 온라인 웹상에 게시하는 매체로부터의 유입 및 거래패턴 파악
    * 카카오뱅크의 앱 내 마케팅 채널을 통합하여 고객의 최종 선택에 영향을 준 요소를 파악

#### 하이퍼커넥트 데이터 팀이 데이터 증가에 대처해 온 방식

* 데이터 기반의 의사결정 프로세스와 문화
* Azar에서 매일 생산되는 데이터 크기: 50GB >> 3TB
* Azar 애플리케이션의 증가, 데이터 필요의 증가
* 데이터 증가에 대처하기 위한 파이프라인의 복잡화/효율화
  * Conversion과 Long Table의 구현에 대한 인식, 초기부터 데이터 파이프라인을 구상
  * 데이터 100GB로 증가, 쿼리의 복잡화/종류 다양화, 속도와 서비스에 영향
    *  Batch, CRON 도입
  *  데이터 500GB로 증가, 증가한 사용자가 다양한 이슈 발생
     * 서비스 오퍼레이션에 문제가 발생하기 시작
     * 오퍼레이션 전문가 채용, 운용 프로세스 구성 시작
     * 서버 로그의 중요성과 가치를 인식 >> 로그 데이터 별도 저장 DB 구성
  * 데이터 1TB로 증가
    * Mysql만으로 서버 운영이 불가능한 수준, Redshift 도입/ Dump/ 이벤트 로그 시스템
    * 데이터에 대한 다양한 시점(dimension, segment) 도입, Fact Table 도입
  * 데이터 2TB로 증가
    * Redshift의 성능, 유지 문제
    * 데이터 분석팀의 쿼리 등에 대한 대비 필요성
    * 서비스 지표의 다양화
    * EMR 기반의 Hive, Presto, Kafka, Spark, ElasticSearch 등 도입
    * 단순한 데이터 지표 파악을 넘어 예측을 도입하기 시작
  * 데이터 3TB로 증가
    * 데이터의 활용 아이디어 증가, 구현
    * Task 증가, Pipeline 복잡화에 따른 Pipeline의 유지보수 방법 개선에 대한 필요성
    * DB 최적화에 대한 필요성
    * 5년 이전의 데이터를 어떻게 잘 지울지?
    * 계속해서 Segment 증가, 데이터를 제공해야 하는 상황의 폭발적 증가
    * Firebase, Airflow, Redis 등 도입

#### 문서 군집화를 위한 K-means 활용 및 시각화

* 토픽 모델링: 문서 집합을 몇 개의 토픽들로 요약
* LDA(Latent Dirichlet Allocation): 한 문서가 여러 개의 토픽 가질 수 있음/ k개 토픽 가정
* K-means: 문서 집합에 한 개의 토픽이 있다고 가정, 각 문서는 하나의 토픽으로 구성
  * 일반적으로 scikit-learn 활용하지만, Initialize의 연산 부담과 Euclidian Matrix의 문제
    * Centroid vectors 활용한 레이블링과 pyLDAvis 활용한 시각화
  * K-means 학습은 어렵지 않음, 각 군집에 레이블을 부여하고 시각화/해석하는 과정의 어려움
  * (Lioyd K-means) Initialize >> Assign >> Revise Centroid
    * Initialize: k개의 점을 랜덤하게 설정
    * Assign: 모든 점들을 k개의 centroid 중 가장 가까운 점의 군집으로 할당
    * Revise Centroid: 각 군집의 평균으로 centroid를 재설정
  * 계산 복잡도 낮음, 
  * Euclidean Distance 이용할수 없음>> 새로운 Norm이 필요
    * 두 문서의 공통된 단어 유무가 중요, Spherical K-means는 Cosine Distance 활용(두 문서에 공통으로 등장한 단어의 비율을 Norm으로 설정) 
  * 적절한 Initialize의 중요성
    * 그러나 최악의 initialization만 아니면 수렴은 문제 없음
    * K-means++: Initial point에서 먼 점일 수록 큰 확률을 부여하고, 해당 확률분포에서 다른 initial point를 추출
    * 고차원 데이터에서는 조금만 멀어져도 대부분의 점들 사이의 거리가 비슷함
      * 30000개 데이터 거리 계산 시 96% 이상이 cosine distance > 0.8
      * 모든 데이터를 계산하는 것이 아닌, initial point에서 가까운 데이터를 제외하고 initial point를 추출
      * 몇 천배 빠른 initial point 결정 가능!
  * 어떤 k를 설정할 것인가?
    * Siluet Measure, 그러나 다차원 데이터에서는 위와 같이 대부분의 점들 사이의 거리가 비슷함
    * Uniform Effect: 군집 간의 차이가 있을 때 K-means의 경우 모든 군집의 크기가 비슷해지는 현상
    * 작은 군집이 나눠져 있는 경우 독립된 군집으로 인식되지 않는 문제
      * K를 크게 설정하여 중복된 여려 개의 군집이 나오게, 이후 후처리로 비슷한 군집을 병합
      * (LDA 역시 동일한 방식의 후처리 필요성)
      * Pairwise Distance matrix: Centroid 간의 거리를 계산하고 시각화하여 서로 가까운 군집을 확인
      * Cosine Similarity > w 후처리를 통해 유사한 군집을 병합
  * Max Iteration 설정
    * 대부분의 경우 10번 이내의 iteration에서 수렴
    * 10번 이외의 경우에는 모호한 경우거나 Uniform Effect 나타나는 Overfitting인 경우
  * 해석의 방식
    * Centroid Vector와 labels
      * Centroid Vector에서, 다른 군집보다 상대적으로 자주 등장한 단어를 키워드로 정의하고 키워드 점수를 통해 군집을 판별
        * Coverage + Discriminative power를 모두 보장하는 단어를 선택해야 함
    * pyLDAvis를 활용한 시각화
      * LDA: 문서에서 정리한 단어 벡터로부터 토픽 - 단어 확률, 문서 - 토픽 확률을 학습하여 k개의 군집 학습
        * Gensim: LDA 라이브러리
      * pyLDAvis: PCA를 활용해 2차원 상에서 시각화 라이브러리
        * 시각화 및 토픽 별 키워드 단어 비중 정리 가능
        * topic을 centroid vector로, document를 label로 변환하여 pyLDAvis를 K-means와 함께 활용할 수 있음

#### 쏘카에서 빅데이터 엔지니어를 채용하는 이유

* 모빌리티 혁신으로 사회적 가치를 실현하는 비전
  * 카셰어링 비즈니스 - Redefining Car-sharing Business
  * 목표: 한정된 교통 자원을 공유하여 사회적 가치/효용을 확보
  * 쏘카 + VCNC: 사회적 가치 확대를 위한 병합
* 카셰어링 회사에서 머신러닝 하는 사람을 뽑는 이유?(팀빌딩 등)
* 10,000대 이상의 차량, 관리의 중요성
  * 사람이 많이 필요할 수록 Scale-up의 어려움, 사람을 적게 뽑으면 운영 차질
  * 데이터를 통해 관리하는 방식의 필요성
* 한정된 자원을 공유하는 업무를 머신러닝의 Prediction (>> 자원 배치) >> Evaluation 과정으로 운용
  * 쏘카 존 케이스
    * 50명이 3500개 쏘카 존 관리 업무 - 인력 부족!
    * 차량 당 매출, 존 별로 수요 데이터를 기반으로 차량을 재분배
      * 성수동 존에 차량을 추가로 넣어야 할까? >> 결정에 따라 2가지 미래, 2가지 미래를 보다 정확히 Prediction함으로써 더 좋은 선택이 가능!
      * 매번 수천 건 이상의 결정에 대한 실험 데이터와 그 결과가 기록, 이를 운영에 활용하는 것으로 목표
    * 이후 존의 완전 자동 관리 목표
  * 가격 케이스
    * Dynamic pricing 운용 목표 - 상황에 따라 가격 변동하여 매출 극대화/체감 가격 감소
    * 할인 제공 실험/수요 대응 가격제 목표
  * 타다 케이스
    * 고객이 기다리지 않게 하기 위한 배차 - 차량 수요 예측 고도화를 통해 차량 운행량 조정
    * 최적의 ETA: 예상되는 배차 시간을 가장 정확하게 전달할 수 있는 방식은?
    * 최적의 과금: GPS 포인트 데이터와 지도 간의 매칭을 고도화

## 소감

*이후 적도록 하겠습니다... 새벽 두시 반...*
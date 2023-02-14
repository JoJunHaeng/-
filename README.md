## 항공 영상의 옥외 문화재 객체 탐지 모델
![image](https://user-images.githubusercontent.com/93234544/218728928-af9fa9cd-d5f5-4767-912d-3f8d48ec99c3.png)
- 목조 문화재 모니터링 중 가장 큰 비중을 차지하는 항목은 기와 손상 및 크랙(매년 3,297건 발생, 2016~2017 문화재 돌봄 사업 경미수리 실적표)																	 
- 기와 훼손 판별을 신속하게 진행해야 하는 이유로는 기와에 손상, 크랙(Crack) 발생 시 흡수율 증가 
- 누수가 발생되어 목조 부분이 썩어 손상 정도가 심해지거나 기와 전체가 내려앉을 수 있는 심각한 문제 발생
- 기와를 점검하기 위해서는 고공크레인을 활용하거나 높은 사다리를 통해 점검에 의존하고 있어 안전사고와 전문 인력 부족 문제

* #### 드론으로 촬영한 항공 영상에서 옥외 문화재 객체 탐지 및 훼손 점검 프로세스를 자동화하기 위한 프로젝트. 데이터 분석을 통하여 모델의 hyper-parameter를 tuinng하여 모델의 성능을 개선함

## 프로젝트 담당 업무
* 관련 연구 조사
* K-means clustering algorithm을 이용한 데이터 셋 분석 및 object detection 모델 개발
* 데이터 셋 라벨링 기준 확립

## 업무 성과
* 전담 기관의 프로젝트 평가 등급 상향(미비->우수)
* 딥러닝 관련 논문 1건 작성

## 개발기간
* 2021년 01월~2021년 12월

## Version
* python3.7, Tensorflow 1.15


## 1. Make dataset

![image](https://user-images.githubusercontent.com/93234544/206904180-13cc8699-6b82-4d75-82f6-362d770ee15d.png)

* 드론 파일럿이 모델 학습에 필요한 데이터 셋을 자체 구축하여 프로젝트에 수행
* 학습 가능한 옥외 문화재 데이터 셋 구축
---|ground truth|
train set|8055개|
val set|871개|
test set|864개|


# 2. Model 분석
![image](https://user-images.githubusercontent.com/93234544/206904369-8c10381b-10f1-4c2a-b3e4-16ba30e86807.png)
* 객체 탐지 모델로 Faster R-CNN(backbone network : resnet50) 선정하여 객체 탐지 기능 개발
***
![image](https://user-images.githubusercontent.com/93234544/218729700-3680cd8e-cc16-458c-aec9-e95feea6ca76.png)
- 𝑃_𝑖 : Predicted probability of anchor 
- 𝑃_𝑖^∗ : Ground-truth label (1: anchor is positive, 0: anchor is negative)
- 𝑡_𝑖^∗  : Predicted Bounding box
- 𝑡_𝑖 : Ground-truth box
- Lambda : Balancing parameter
- 𝑁_𝑐𝑙𝑠 : mini-batch size
- 𝑁_𝑟𝑒𝑔 : 이미지 내부에서 사용된 모든 anchor의 location

* 원본 논문(Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks)의 분석 결과 모델의 성능에 가장 큰 영향을 미치는 hyperparameter는 anchor box이므로 anchor box를 중점적으로 tuninng


# 3. Data 분석

![image](https://user-images.githubusercontent.com/93234544/206905350-511b6386-c968-4ad0-8a2a-4473e89b3ee3.png)
* Data set의 ground-truth bounding boxes의 width, height를 그래프로 표현
* Model의 성능에 가장 큰 영향을 미치는 hyper-parameter인 anchor box를 설정하기 위해 Data set 분석
***
![image](https://user-images.githubusercontent.com/93234544/206904951-84da7aa6-b0ba-4845-8436-863341527996.png)
* Model의 hyper-parameter 중 하나인 anchor box를 튜닝하기 위해서 K-means clustering을 활용
* K-means clustering의 cluster의 x 좌표가 점진적으로 증가하는 형태가 나올 때 까지 K를 반복 탐색하여 anchor box 크기를 설정
* K가 결정된 시점에서의 cluster의 x 값을 anchor box의 크기로 설정함
* Data set의 전체 ground-truth bounding boxes의 aspect ratio를 감안하여 anchor box의 aspect ratio를 0.83으로 설정
***
![image](https://user-images.githubusercontent.com/93234544/218731285-e2701cec-fa1f-4cde-bd99-90a52f5d39c4.png)
Scale imbalance problem : Scale 불균형은 객체의 사이즈 또는 Input에 사용하는 Ground truth에 의해 발생한다. 객체 탐지를 위해 설계된 Backbone network일지라도, Ground truth의 다양한 Scale을 처리하기에 충분하지 않다.
(a) : 정규화된 이미지의 너비의 빈도
(b) : 정규화된 이미지의 높이의 빈도
(c) : 정규화된 이미지의 넓이의 빈도
구축한 Data set의 Ground truth 영역의 크기 분포는 scale imbalance problem의 위험이 존재
***
![image](https://user-images.githubusercontent.com/93234544/206904579-2a33070d-1f9d-4d6d-8eea-50d5b26ae426.png)
* Scale imbalance proble을 야기할 수 있는 452개의 case를 추려 Mosaic 기법으로 Data agumentation
* 모델의 성능을 저하시킬 수 있는 Sacle imbalence problem을 극복하기 위한 data


# 4. 조건별 Model hyperparamter 설정
실험 조건|Box size|Aspect ratio|
---|---|---|
실험 모델(anchor box 종횡비 미적용)|[161 * 161, 329 * 329, 716 * 716]|(1 : 1), (1 : 1.5),(1.5 : 1), (1.5 : 1.5)|
실험 모델(anchor box 종횡비 적용)|[161 * 161, 329 * 329, 716 * 716]|[(1 : 0.83), (1 : 1.245),(1.5 : 1), (1.5 : 1.245)]|
기존모델(original Faster RCNN)|[128 * 128, 256 * 256, 512 * 512]|[(1 : 1), (1 : 2),(2 : 1)]|
  
* 실험 모델들은 K-means clustering으로 결정한 cluster의 x 값과 aspect ratio로 anchor box의 크기 및 종횡비를 설정
* 기존 모델은 Faster RCNN의 원저자가 논문에 기재한 값을 사용



# 5. result 
* #### 학습 조건별 Model  mAP
제안 모델|AP<sub>50|AP<sub>75|AP<sub>90|mAP
---|---|---|---|---|
실험 모델(anchor box 종횡비 미적용 & Mosaic img 학습)|0.902|0.814|0.424|0.689|
실험 모델(anchor box 종횡비 적용 & Mosaic img 학습)|0.882|0.807|0.349|0.654|
실험 모델(anchor box 종횡비 미적용 & Mosaic img 미학습)|0.876|0.775|0.289|0.620|
실험 모델(anchor box 종횡비 적용 & Mosaic img 미학습)|0.870|0.769|0.223|0.598|
기존모델(original Faster RCNN)|0.869|0.781|0.079|0.556|

 *** 
* #### Model의 옥외 문화재 객체 탐지 결과 영상
  ![image](https://user-images.githubusercontent.com/93234544/206906346-f8361e4b-cb10-4be0-b18d-ce5484a68cae.png)

  
  

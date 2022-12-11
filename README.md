## 항공 영상의 옥외 문화재 객체 탐지 모델

* #### 드론으로 촬영한 항공 영상에서 옥외 문화재 객체 탐지 및 훼손 점검 프로세스를 자동화하기 위한 프로젝트. 데이터 분석을 통하여 모델의 hyper-parameter를 tuinng하여 모델의 성능을 개선함

## Version
Tensorflow 1.15

## 1. Make dataset

![image](https://user-images.githubusercontent.com/93234544/206904180-13cc8699-6b82-4d75-82f6-362d770ee15d.png)

* 드론 파일럿이 모델 학습에 필요한 데이터 셋을 자체 구축하여 프로젝트에 수행


# 2. Model
![image](https://user-images.githubusercontent.com/93234544/206904369-8c10381b-10f1-4c2a-b3e4-16ba30e86807.png)

* 객체 탐지 모델로 Faster R-CNN(backbone network : resnet50) 선정하여 객체 탐지 기능 개발


# 3. Data 분석

![image](https://user-images.githubusercontent.com/93234544/206905350-511b6386-c968-4ad0-8a2a-4473e89b3ee3.png)
* Data set의 ground-truth bounding boxes의 width, height를 그래프로 표현
* Model의 성능에 가장 큰 영향을 미치는 hyper-parameter인 anchor box를 설정하기 위해 Data set 분석
***
![image](https://user-images.githubusercontent.com/93234544/206904951-84da7aa6-b0ba-4845-8436-863341527996.png)
* Model의 hyper-parameter 중 하나인 anchor box를 튜닝하기 위해서 K-means clustering을 활용
* K-means clustering의 K를 반복 탐색하여 anchor box 크기가 점진적으로 증가하도록 설정
* Data set의 전체 ground-truth bounding boxes의 aspect ratio를 감안하여 anchor box의 aspect ratio를 0.83으로 설정
***
![image](https://user-images.githubusercontent.com/93234544/206904579-2a33070d-1f9d-4d6d-8eea-50d5b26ae426.png)
* 극단적인 종횡비를 가진 옥외 문화재가 있는 영상을 추려 Mosaic 기법으로 Data agumentation
* 모델의 성능을 저하시킬 수 있는 Sacle imbalence problem을 극복하기 위한 data

# 4. result 
* #### Model 학습 조건별 mAP
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

  
  

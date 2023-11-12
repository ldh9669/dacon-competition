dacon-competition
===========
데이콘에서 진행하는 ai 경진대회에 참여한 프로젝트입니다.

### 설명

사용언어 : Python

작업툴 : Jupyter Notebook

사용 라이브러리 : pandas, numpy, matplotlib, seaborn, nltk, imblearn, sklearn, bs4, requests, 기타등등

인원 : 1명

기간 : 2023.06.05 ~ 2023.07.03

I. 프로젝트 개요
--------
1. 프로젝트 배경 및 목적
##### 법원 승패에 관한 판결문들을 학습해 앞으로 비슷한 사건이 있을 때 승소할지 패소할지의 여부를 예측하는 프로젝트입니다.

2. 데이터셋 소개
* 데이콘에서 제공하는 법원 사건의 사례들이며 사건 당사자의 승소 여부가 있는 훈련 데이터셋과 테스트 데이터셋이 존재한다.
* ID : 사건 샘플 ID
* first_party : 사건의 첫 번째 당사자
* second_party : 사건의 두 번째 당사자
* facts : 사건 내용
* first_party_winner : 첫 번째 당사자의 승소 여부 (0 : 패배, 1 : 승리)

```
Train data
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 2478 entries, 0 to 2477
Data columns (total 5 columns):
 #   Column        Non-Null Count  Dtype 
---  ------        --------------  ----- 
 0   ID            2478 non-null   object
 1   first_party   2478 non-null   object
 2   second_party  2478 non-null   object
 3   facts         2478 non-null   object
 4   winner        2478 non-null   int64 
dtypes: int64(1), object(4)
memory usage: 96.9+ KB
```

```
train data winner
1    1649
0     829
total: 2478
Name: winner, dtype: int64
```

```
Test data
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1240 entries, 0 to 1239
Data columns (total 4 columns):
 #   Column        Non-Null Count  Dtype 
---  ------        --------------  ----- 
 0   ID            1240 non-null   object
 1   first_party   1240 non-null   object
 2   second_party  1240 non-null   object
 3   facts         1240 non-null   object
dtypes: object(4)
memory usage: 38.9+ KB
```
II. 전처리
--------
* 텍스트 정제 
  1) 전체 문자열 소문자 변환
  2) 특수 문자 유지
  3) 문장 단위 토큰화
  4) 형태소 분석
  5) 토큰화

* 불용어 제거
  1) NLTK 불용어모음
  2) 국가명, 이름을 불용어로 취급해 외부 웹사이트에서 각각 이름들을 크롤링

위 과정들을 거쳐 정제시킨 텍스트로 훈련 데이터, 테스트 데이터 재구성

* 훈련 데이터와 테스트 데이터에서 겹치는 단어들을 선정
* 정제된 텍스트를 벡터화
  1) TfidfVectorizer를 사용하여 벡터화 진행
  2) 여러가지 파라미터를 사용하여 겹치는 단어들에 더 큰 가중치 부여

* 훈련 데이터 세트의 결과값인 winner의 비율이 1649:829 인 한쪽으로 편향된 데이터이기 때문에 학습 중 과적합이 되는 결과를 초래해 비율을 맞추는 언더샘플링과 SMOTE 오버샘플링 적용
* Standard Scaler 사용

III. 모델 학습
--------
* 사용모델: LogisticRegression
* 성능지표: accuracy_score, 코사인 유사도

```
코사인 유사도: 0.034181728440276754
Accuracy: 0.7221418234442837
```
IV. 대회 및 채점 점수
--------
private 35등 / 506명, 점수: 0.51774
![image](https://github.com/ldh9669/dacon-competition/assets/98334298/ec719c9e-0c60-43f3-be0a-febbf662ff96)

V. 진행하며 겪었던 문제점과 해결 방법
--------
모델이 학습한 후 성능 검증 과정에서, 훈련 데이터는 높은 성능을 보이는 반면, 테스트 데이터는 낮은 점수를 보이는 문제가 발생했습니다. 

이에 데이터를 분석한 결과, 훈련 데이터에서 승소와 패소의 비율이 한 쪽으로 쏠려 오버피팅이 발생했다고 판단하였습니다. 

문제점을 발견한 후 다운 샘플링과 훈련 데이터와 테스트 데이터 간의 격차를 줄이는 전처리 과정을 통해 모델의 성능을 향상시켰습니다.

# 2022 우주 전파 재난 예측 대회

### 1. 데이터 셋 구성
-학습 모델의 입력자료로 일부를 편집함.
### 1-1. 사용 데이터
- ACE·DSCOVR 위성의 태양풍 데이터의 &quot;bx_gsm&quot;,&quot;by_gsm&quot;,&quot;bz_gsm&quot;,&quot;bt&quot;
- 국내 지자기 관측소 3곳의 &quot;bx_gsm&quot;,&quot;by_gsm&quot;,&quot;bz_gsm&quot;
### 1-2. 사용하지 않은 데이터
- ACE·DSCOVR 위성의 태양풍 데이터의 &quot;proton_density&quot;, &quot;proton_temperature&quot;, &quot;proton_speed&quot;
- 사유 : nan의 값이 많아 모두 제거 처리.
### 1-3. 추가한 데이터
-국내 지자기 관측소 3곳의 (총 자기장) = np.sqrt((bx_gsm)**2 +(by_gsm)**2+(bz_gsm)**2)을 추가
- 사유 : 총 자기장 값은 주어지지 않았기 때문에 추가함.
### 1-4. IQR을 통한 cliping 사용
- 하한선 : Q1 - IQR * 2.5
- 상한선 : Q3 + IQR * 2.5
의 cliping 사용.
### 1-5. minmaxscaler를 이용하여 정규화 진행
### 1-6. 180개의 분 데이터를 하나의 array로 변경
### 1-7. train data와 validation data를 6:4로 분리
사유 : 3시간(180분)의 데이터가 하나의 국내 지자기 교란 지수를 가르키기 때문.
####* 대회 답안 도출용 데이터 처리
- interploate을 이용하여 결측값을 처리함.
- 대회 답안 도출용 데이터가 모델을 통과한 후, 반올림을 사용하여 데이터 보정.
사유 : 국내 지자기 교란 지수가 자연수이기 때문.
### 2. 적용 알고리즘
Dense와 LSTM을 적절히 혼합하여 사용.

### 3. 모델 구성
-LSTM 1층, Dense 12층으로 이루어짐.
- 각 층은 모두 kernel_initializer를 he_normal로 설정함.
- LSTM은 tanh으로 activation 하였고, Dense층은 leaky relu로 activation 함.
- Dense 층은 l2 regularize(0.01)을 적용함.
- 각 Dense의 처음과 끝에 BatchNormalization과 dropout(0.2)를 적용.

### 4. 모델 정확도(accuracy)
- loss function = mse, optimizer = adam, metrics = mae 사용
- batch size = 128 사용
- train data과 validation data에 대한 결과와 현재 제출된 대회용 답안에 대한 결과가
다른 이유는 대회용 답안에서 Weighted RMSE를 사용했기 때문에 특정 자료에서
정확도가 낮았기 때문이라고 생각함.

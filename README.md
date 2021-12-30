# Kaggle_Ventilator_Pressure_Prediction
---
작성중
##서론
'Ventilator Pressure Prediction'은 2021년 11월 3일에 끝난 대회다. 새삼스레 왜 12월 하순이 되어서 이 글을 쓰게 되었냐면 12월 17일까지 빡빡하게 들어차 있던 K-Digital Train 교육 과정이 나를 괴롭히고 있었기 때문이다. 매일매일 하루 12시간 가까운 시간을 빼앗고 내 기력까지 앗아가는 바람에 지금 쓸 수 있게 되었다. 한 번은 당시 정신없이 해치웠던 작업들을 정리하는 시간이 필요했다.

 

이 대회는 제목에서 볼 수 있듯 Google Brain에서 주최했고, 산소호흡기의 압력을 예측하는 대회다. Google Brain은 우리가 알고 있는 '구글'의 딥 러닝 인공지능 연구팀이다. 산소호흡기까지 연구하는 것을 보면 상상 이상으로 다방면의 연구를 진행하는 연구팀인 것 같다.

 

##취지
이번 대회의 취지는 노동 집약적으로 사용할 수 밖에 없는 기계식 산소호흡기를 대체할 수 있는 방법을 찾는 것이다. 팬데믹 사태와 맞물려 폭발적으로 증가하는 호흡기 환자들을 모두 수동으로 대처하기란 불가능에 가깝기 때문에 비용이 많이 들더라도 높은 수준으로 산소호흡기를 조절할 수 있는 인공지능을 개발하자는 것이다. 

 

이번 대회에서 우승한 사람의 제출물은 이런 기술 개발에 사용되어 새로운 기계식 산소호흡기를 만드는 데에 일조하게 된다는데 나랑은 크게 상관없는 이야기이다. 물론 이 기술을 사용해서 전 세계 호흡기 환자들을 간편하게 돌볼 수 있기 때문에 인류 전체적으로는 의미 있는 일이다.

 

##평가 방법
이번 대회는 산소호흡기(Ventilator)의 압력을 예측하는 회귀 예측 문제다. 그리고 점수는 MAE(Mean Absolute Error)로 측정된다. MAE는 |X - Y| 로 표현되는 평가 방법인데, X는 우리가 예측할 값이고, Y는 실제 값이다. 정리하면 우리가 예측한 값과 실제 값의 차이(error)의 평균값이다. MAE는 MSE, RMSE 등의 방법과 달리 에러의 크기 그대로 반영되는 게 특징이고, 에러에 따른 손실이 선형적으로 올라갈 때 적합한 방법이다. 아마 이번 대회 데이터에 아웃라이어가 존재하거나 에러에 따른 손실이 기하급수적으로 증가하는 문제가 아닌 것 같다.

 

##데이터
이번 대회에서 제공되는 데이터는 이번 대회의 공동 주최자인 Princeton Univ에서 오픈 소스로 제공하는 PVP를 인공 폐에 연결시켜서 만든 데이터다. 아래 사진들이 이에 해당하는 기계들이다. 

![image](https://user-images.githubusercontent.com/69458840/147729141-3ea0bee8-3ed5-4dc5-ba1c-d2747e81a35b.png)
![image](https://user-images.githubusercontent.com/69458840/147729148-db8ac98b-6679-4b8f-9f95-65d240f628e3.png)

아래의 다이어그램이 이번 대회의 데이터를 수집하는 셋업을 명료하게 설명해준다. 결론적으로는 파란색 부분인 Pressure Sensor에서 감지되는 압력을 정확하게 예측하는 게 대회의 취지다. 그런데 취지 설명에서 조절에 대한 이야기를 했었는데 조절하는 부분이 바로 두 개의 초록색 영역(Inspiratory Valve, Expiratory Valve)다. 이 부분을 조절하는 것이 노동 집약적인 부분이라서 머신러닝을 이용해야 된다는데 일단 이번 대회에서는 이 부분의 데이터가 친절하게 제공된다.

 

Inspiratory Valve는 쉽게 말하면 흡기 밸브다. 이 부분의 입력은 0부터 100까지의 연속적인 변수로 받는다. 0은 완전히 밸브가 닫혀서 숨을 들이쉬지 않는다는 뜻이고 100이면 완전 열려서 힘껏 들이쉰다는 뜻이다.

 

Expiratory Valve는 반대로 배기 밸브다. 이 부분의 입력은 흡기 밸브와 다르게 이진 변수를 입력으로 받는다. 흡기 밸브와 비슷하게 0이면 완전히 닫혀서 공기가 안 빠져나가고 1이면 완전히 열려서 공기가 폐에서 빠져나간다는 뜻이다.

 

그리고 이번 대회의 중요한 포인트 중 하나는 참가자들에게 시계열 데이터가 제공된다는 것이다. 다시 말하면 주어진 시계열 입력 데이터를 바탕으로 기도의 압력을 측정해야 된다. 각각의 시계열들은 3초 동안의 호흡을 나타내고 주어지는 데이터의 각 행은 각 타입 스텝에서의 조절 신호 및 압력을 나타낸다. 
![image](https://user-images.githubusercontent.com/69458840/147729176-513892f0-4824-4266-a7ed-ff308c2ebea6.png)


- id - globally-unique time step identifier across an entire file
breath_id - globally-unique time step for breaths
- R - lung attribute indicating how restricted the airway is (in cmH2O/L/S). Physically, this is the change in pressure per change in flow (air volume per time). Intuitively, one can imagine blowing up a balloon through a straw. We can change R by changing the diameter of the straw, with higher R being harder to blow.
- C - lung attribute indicating how compliant the lung is (in mL/cmH2O). Physically, this is the change in volume per change in pressure. Intuitively, one can imagine the same balloon example. We can change C by changing the thickness of the balloon’s latex, with higher C having thinner latex and easier to blow.
- time_step - the actual time stamp.
- u_in - the control input for the inspiratory solenoid valve. Ranges from 0 to 100.
- u_out - the control input for the exploratory solenoid valve. Either 0 or 1.
- pressure - the airway pressure measured in the respiratory circuit, measured in cmH2O.

---

layout: single
title: "[자율주행] Python atan vs atan2"
category: "tech"
tags: python

---

  자율주행 보행자 위치 예측이 방향에 따라 정확도가 큰 차이를 보인다는 걸 발견했다. 예를 들면 보행자가 서/북쪽으로 이동할 경우 높은 예측 정확도를 보이고 동/남쪽으로 이동할 경우 낮은 예측 정확도를 보였다. 이처럼 특정 방향에 따라 예측 정확도가 다르다는 건 알고리즘, 코드 작성 등에 문제가 있다는 걸 나타낸다.

### 문제 확인

  보행자 이동 방향에 따라 예측 정확도가 차이가 났기 때문에 방향각 구하는 공식 또는 소스 코드에 문제가 있을 거라고 가정했다. 방향각 공식 ArcTan((Y2 - Y1) / (X2 - X1))을 구글 검색을 통해 다시 확인을 했고 문제가 없다는 걸 확인했다. 파이썬 코드 math.atan((Y2 - Y1) / (X2 - X1))에 다양한 값을 넣어보며 테스트했다. 코드 테스트에서 음수 값이 나와야 하는 상황에서 양수 값을 출력해준다는 걸 확인할 수 있었다.

### 문제 사항

  python의 Math 모듈에는 arc tan을 구하는 메서드 2 가지가 있다.

~~~
math.atan(x)
 - x의 arc tangent를 radian으로 반환
 - 반환값의 범위는 -pi/2 ~ pi/2
 
math.atan2(y, x)
 - atan(y/x)를 라디안으로 반환
 - 반환값의 범위는 -pi ~ pi
 - x와 y의 부호를 모두 알고 있기에 각도에 대한 정확한 사분면 계산 가능
~~~

 방향각을 구할 때 사용한 atan의 경우 (Y2 - Y1) / (X2 - X1) 계산된 값이 입력값으로 들어가기 때문에 (Y2 - Y1)과 (X2 - X1)의 각 부호를 알 수 없어 사분면 상의 정확한 값을 리턴해줄 수 없는 문제가 있다. 예를 들면 "(Y2 - Y1) = -1"과 "(X2 - X1) = -1"인 경우 atan 메서드는 atan(1)로 "pi / 4"를 리턴해준다. 하지만 실제 사분면 상의 정확한 값은 "-3 * pi / 4"이다.

### 문제 해결

  기존에 사용한 math.atan은 사분면 상에 정확한 값을 리턴해줄 수 없기에 math.atan2를 사용하여 해결을 했다. math.atan2의 경우 (Y2 - Y1)과 (X2 - X1)의 값을 인자로 받아 사분면 상의 정확한 값을 리턴해주며, 위 문제 사항에서 나온 테스트에서도 정확한 값을 리턴해준다는 걸을 확인할 수 있었다. 

### 출처

~~~
https://docs.python.org/ko/3/library/math.html
~~~






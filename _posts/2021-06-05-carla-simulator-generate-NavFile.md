## Carla Simulator Nav( format - bin ) 파일 생성 방법

### 환경설정

* Ubuntu 18.04
* Unreal Engine 4.24
* Carla Simulator 0.9.10

### Navigation 파일 설명

* Carla Simulator 상에서 보행자 이동을 위해 Navigation 파일을 생성을 해줘야한다.
* Navigation 파일은 Unreal Engine의 Map에서 추출한 파일(obj)을 사용해 생성한다.
* Navigation 파일 포맷은 bin이다.

### 문제 사항

  Carla Simulator 공식 문서(https://carla.readthedocs.io/en/0.9.10/tuto_D_generate_pedestrian_navigation/)를 따라할 경우 Navigation 파일 생성이 되지 않았다. 해당 문제를 해결하기 위해 구글 검색을 했으며, 확인한 문제점은 아래 2가지와 같다.

1. Navigation 파일을 생성할 때 사용하는 Unreal Engine의 obj 파일이 처리할 수 있는 크기를 초과하는 문제
2. Unreal Engin에서 Navigation 파일을 추출할 때 관련 없는 Mesh 값들이 같이 추출되는 문제

### 해결 방안

1. Unreal Engine의 obj 파일을 줄이기 위해 새로 만든 Map 자체의 크기를 줄였다.
2. Navigation 파일을 추출할 때 관련없는 Mesh 값을 제외하기 위해 Navigation 관련 값들만 들어있는 xodr 파일 사용

  위 해결방안 2가지를 전부 적용해 Navigation 파일 만드는 것을 테스트 했다. 테스트 해본 결과 obj 파일은 1.3M정도 이하여야 한다는 걸 추가적으로 확인할 수 있었다.

### 해결 방안 적용 방법

1. 새로 만든 Map의 xodr 파일을 준비
2. Unreal Engine 실행 및 시뮬레이터 동작
3. Carla Simulator에서 제공하는 PythonAPI중 config.py를 통해 xodr 파일로 맵 띄우기
   1. python3 config.py -x "newMap.xodr"
4. Carla Simulator 폴더 내부 Maps/Nav 폴더에 opendriveMap.obj 및 opendriveMap.bin 파일 확인
5. opendriveMap.bin 파일을 필요한 Map에 복사에 사용
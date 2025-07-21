---
layout: post
title: An Overview of Path Planning
date: 2025-07-20 09:40:30
description: reviews the basic concepts of path planning and summarizes the existing path planning algorithms
tags: path_planning

---

Path planning(경로 계획)의 개념은 다음과 같습니다: 환경 정보를 얼마나 알고 있는가에 따라 환경 정보가 완전히 알려진 global path planning과 부분적으로 환경 정보가 알려진 local path planning으로 나눌 수 있습니다. 또한 환경 내 장애물이 움직이는지의 여부에 따라 장애물이 정적인 static planning과 장애물이 동적인 dynamic planning으로 나뉠 수 있습니다. 그리고, 모바일 로봇 시스템에서 제어 가능한 변수의 수가 로봇의 attitude 공간 차원보다 작은지의 여부에 따라, 제어 가능한 자유도가 전체 자세 공간보다 같거나 많은 (ex, 드론, x-y평면에서 움직이는 로봇 팔) holonomic system의 운동 계획, 그와 반대 되는 non-holonomic system의 운동 계획으로 나뉠 수 있습니다.

Global path planning은 시작점에서 목표지점까지의 순수한 기하학적 경로를 다룹니다. Local path planning은 obstacle avoidance planning, dynamic path planning, real-time navigation planning이라고도 불립니다. 장애물을 탐지하고 움직이는 장애물의 괘적을 추적하며 다음 위치를 예측하는데 초점을 맞춥니다. 결과, 현재 존재하는 충돌 위험과 잠재적인 충돌 위험이 포함된 지도를 생성하게 됩니다. 


Path planning을 수행하기 위해서는, 우선 모바일 로봇의 이동 환경 모델을 구축해야 합니다. 환경은 보통 정적(static)과 동적(dynamic) 환경으로 나눌 수 있습니다. 정적 환경은 상대적으로 단순하지만, 대부분의 환경은 실시간으로 변화하는 동적 환경입니다. 이러한 특성은 경로 계획 알고리즘에 높은 실시간성과 robustness를 요구합니다. 현재 사용되는 환경 모델링 방법은 주로 모바일 로봇의 주변 환경 정보를 수집하여, map subsystem을 통해 기하학적/위상학적 특성을 갖춘 지도상에 표현하는 것입니다. 이 지도에는 도로 혹은 사물간의 연결 관계가 포함됩니다. 가장 단순한 지도 생성 방법은 항공 사진에서 이를 추출하여 라벨링하는 것 입니다. 동적 환경에는 MOT(Multi-object tracking) 또는 DATMO (Detection and tracking moving object) 서브시스템이 사용되어, 로봇 주변의 장애물의 위치나 방향을 탐지 및 추적합니다.

전통적인 환경 모델링 방법은 다음과 같습니다:
- Multi-object tracking: 센서 데이터를 분할하고 분할된 데이터를 장애물에 연결하여, 대상별 위치 추정을 위해 할당된 데이터를 평균 처리하고 필터(ex, Kalman Filter or Particle Filter)로 위치를 갱신한다.
- Model based Approach: 센서의 물리 모델과 객체의 기하학 모델을 활용하여 필터(ex, Particle filter)로 직접 데이터를 해석한다.
- Stereo Vision based method: 스테레오 이미지에서 제공되는 색상 및 깊이 정보를 이용해 움직이는 장애물을 탐지 및 추적한다. ESS는 전방 카메라의 동기화된 영상 만으로 장애물을 탐지하는 기법을 제안한다.
- Raster Map based method: occupancy grid map을 먼저 구축하고, 분할, 연관, 필터링 단계를 통해 객체 수준의 장면 표현을 제공한다. 
- Sensor fusion: 다양한 센서를 융합하여 환경 인식을 향상한다.
- Deep Laerning: DNN을 통해 이동 장애물의 위치 및 기하학적  특징을 추출하고, 현재 카메라 데이터를 바탕으로 미래 상태를 추적한다.
- V2X: Vehicle to Everything은  차량 간, 차량과 인프라 간, 차량과 보행자 간, 차량과 네트워크 간 통신을 가능케 함으로서 실시간 도로 상황, 정보, 보행자 정보 등을 알 수 있으며, V2V(ehicle), V2I(nfra), V2P(erson), V2N(etwork)을 포함한다.


Path planning 알고리즘은 크게 세 가지로 나뉩니다. 1) 지도 기반의 전통적인 경로 계획 알고리즘 (Dijkstra, A*), 2) 생물 모방 기반의 지능형 경로 계획 알고리즘 (PSO, 유전자 알고리즘, 강화 학습), 3) 샘플링 기반의 경로 계획 알고리즘 (RRT).

지도 기반 전통 경로 계획 알고리즘은 대표적으로 5가지가 있습니다: Dijkstra, a*, D*, LPA*, D* Lite. 이들은 A* 계열로 알고리즘을 실행하기 전 환경 모델을 반드시 구축해야합니다. 

Dijkstra 알고리즘은 탐욕적 원칙을 기반으로 하여 노드를 하나씩 순차적으로 탐색하고, 완화(relaxation) 방법으로 경로를 최적화합니다. 최종적으로 최적 경로를 리스트에 저장하여 최적 경로 문제를 해결하는 방식으로, 지도 데이터가 작을 때는 효과적이지만, 데이터 양이 많을 경우 성능이 급격히 떨어집니다. 우선순위 큐와 역 N-트리를 결합하면 성능이 향상될 수 있으며, 제한된 범위 내에서의 탐색 방식을 도입하면 탐색 범위 및 횟수를 줄여 검색 효율을 향상할 수 있습니다.

A* 알고리즘은 Dijkstra 알고리즘을 개선한 heuristic 탐색 알고리즘입니다. 추정 함수를 추가하여, 탐색 위치와 목표 지점 간의 거리를 예측함으로서 탐색 방향을 목표 지점 쪽으로 우선 유도 합니다. 기본 함수 형태는 f(x) = g(x) + h(x)입니다. g(x)는 시작점에서 현재 노드 x까지의 실제 거리를 의미하며, h(x)는 현재 노드 x에서 목표점까지의 최소 거리 추정값입니다. Dijkstra에 비해 탐색 횟수가 줄고 속도가 빨라서 널리 사용됩니다. 또한, 목표 지점 근처로 갈수록 탐색 범위가 좁아집니다.

D* 알고리즘은 Dynamic A* 알고리즘입니다. 목표점에서 시작점 방향으로 탐색하는 Reverse Incremental Search 방식을 사용하며, 도중 장애물이 나타나면, 기존 거리 정보를 재사용하여 전체 경로를 재계산하지 않고 부분만 갱신합니다. H(x) = H(y) + C(y,x) 공식을 사용하며 H(y)는 y지점에서 목표까지의 거리이고, C(y,x)는 y지점에서 x지점까지의 거리를 의미합니다.

LPA* 알고리즘은 Life Planning A*의 약어로, Increasement heuristic serarch 알고리즘입니다. 시작점에서 탐색을 시작하며, key값을 기준으로 탐색을 진행합니다. 목표 지점이 다음 탐색 대상이 되면 계획을 완료하고, Key 값은 휴리스틱 요소를 포함하여 탐색 방향에 영향을 줍니다. 동적 환경에서도 이전 탐색에서 얻은 G 값을 활용하여 전체 환경을 재 탐색하지 않고 빠르게 경로 재계획 가능합니다. G(n)은 시작점에서 현재 노드까지의 거리이며, RHS(n)은 min(g(n)+c(n,n')) (n'은 n의 부모 노드), h(n, goal)은 목표에 대한 휴리스틱 값을 의미합니다. 먼저 K1 값을 비교하고, K1이 작으면 해당 노드를 우선 탐색, K1 = K2이면 K2가 더 작은 노드를 우선하며 탐색합니다.

D* Lite 알고리즘은 LPA*을 기반으로 제안한 경로 계획 알고리즘입니다. Key 계산 시 목표점(goal) 대신 시작점을 기준으로 한 정보를 사용하며, 알고리즘은 역방향 탐색으로 시작하여 최적 경로를 찾고, 동적 장애물이 나타나면 국지적인 범위에서 탐색을 재시작합니다. 경로 탐색 정보를 재활용할 수 있고, 장애물로 인해 기준 경로의 사용이 불가할 시, 현재 위치에서 빠르게 최적 경로 재계획이 가능합니다.


생물 기반의 경로 계획 알고리즘으로는 신경망, 개미 군집 알고리즘, 늑대 군집 알고리즘, 유전 알고리즘 등이 있습니다. 유전 알고리즘은 자연 선택과 다윈의 진화 메커니즘을 모방하여 최적 해를 탐색하는 알고리즘입니다. 주요 특징은 함수의 도함수나 연속성의 제약 없이 구조적 개체를 직접 다룰 수 있으며, 내재된 병렬성과 뛰어난 전역 최적화 능력을 갖추고 있다는 것입니다. 확률 기반의 방식으로 명확한 규칙 없이도 최적 해 탐색이 가능합니다. 신경망 알고리즘은 많은 수의 노드(또는 뉴런)로 구성된 연산 모델로, 각 노드는 활성화 함수라 불리는 특정 출력 함수를 가지며, 노드 간 연결은 가중치를 통해 신호 강도를 조절한다. 이 가중치는 인공 신경망의 기억에 해당합니다. 개미 군집 알고리즘은 현재 널리 사용되는 지능형 알고리즘 중 하나지만, 반복 횟수가 많고 수렴 속도가 느리며 국소 최적 해에 빠지기 쉽다는 단점이 있습니다. fuzzy 알고리즘을 결합하여 수렴 속도를 개선하거나, 유전 알고리즘과의 결합을 통해 최적 경로를 도출하는 방식 등이 있습니다.


샘플링 기반 경로 계획 알고리즘은 확률적 로드맵(PRM)과 빠른 탐색 랜덤 트리(RRT)가 대표적입니다. PRM 알고리즘은 경로 공간에서 무작위로 N개의 노드를 선택하고, 각 노드를 연결한 뒤 장애물과 충돌하는 연결을 제거하여 실행 가능한 경로를 생성하는 방식입니다. 샘플링 포인트 수가 적거나 배치가 비효율적이면 알고리즘의 완전성이 떨어지지만, 샘플링을 추가하면 보완할 수 있습니다. 따라서 PRM은 확률적 완전성은 있지만 최적성은 없습니다. RRT (Rapidly exploring random tree)는 경로 공간의 모델링이 필요 없고 탐색 범위가 넓으며 미지 영역을 광범위하게 탐색할 수 있는 장점이 있습니다. 하지만 계산 비용이 크며, 이를 해결하기 위해 다양한 변형 알고리즘 (Goal-Bias RRT, Bi-RRT, RRT-Connect, Extend RRT, Local-Tree-RRT, Dynamic RRT)이 존재합니다.


References: 
@article{Tang_2021,
doi = {10.1088/1755-1315/804/2/022024},
url = {https://dx.doi.org/10.1088/1755-1315/804/2/022024},
year = {2021},
month = {jul},
publisher = {IOP Publishing},
volume = {804},
number = {2},
pages = {022024},
author = {Tang, Zhuozhen and Ma, Hongzhong},
title = {An overview of path planning algorithms},
journal = {IOP Conference Series: Earth and Environmental Science},
abstract = {This paper reviews the basic concepts of path planning, classifies environmental modeling methods, analyzes the significance of V2X environment modeling, and summarizes the existing path planning algorithms. Different algorithm can be adjusted in time according to different environments to improve the efficiency of path planning. In addition, according to the advantages and disadvantages of different algorithms, each algorithm is fused, which can effectively avoid the shortcomings of each algorithm and improve the efficiency of the planning algorithm.}
}
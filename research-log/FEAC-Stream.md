- [Overview](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/FEAC-Stream.md#overview)  
- [3가지 컴포넌트로 구성](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/FEAC-Stream.md#3%EA%B0%80%EC%A7%80-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EB%A1%9C-%EA%B5%AC%EC%84%B1)  
- [동작과정](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/FEAC-Stream.md#%EB%8F%99%EC%9E%91%EA%B3%BC%EC%A0%95)  
- [FEAC](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/FEAC-Stream.md#feac)  


---

# Overview
- Fast Evolutionary Algorithms for Relational Clustering(FEAC)의 개선 버전(가변k)
- 특징
  - Auto cluster count
  - Damped window
  - Hypersherical
  - Online
  - High dimensional data suitable
  - No expert knowledge
  - Drift adaptation
- 단점
  - 초기화에 사용되는 데이터 시퀀스의 길이 l, 감쇠 윈도우 모델을 위한 감쇠율 λ, 최소 가중치 임계값 α 하이퍼 파라미터 설정에 민감

# Process
- 초기 L개 데이터 인스턴스 수집, 최초 클러스터링 수행
  - 새로운 데이터 x도착시 가장 가까운 클러스터에 할당 시도
  - damped window를 cluster feature vector에 적용해서 오래된, 가중치 낮은 클러스터는 탈락.
- Page–Hinkley Test를 수행해서
    - 경고 (임계값1) 진입 시 버퍼에 인스턴스 저장
    - 알람(임계값2) 넘으면 개념변화 발생 의미
- 최대 iter회 또는 종료조건이 만족될 때까지 랜덤 k에 대한 k-means 수행
  - 진화 알고리즘이라고 불리는 클러스터 중심 삭제 및 병합 과정을 수행(FEAC와 동일)
  - Simplified Silhouette로 일정 범위 k중 품질 가장 좋은 값 선택

# Encoding Scheme
- 클러스터 피쳐 벡터는 4개의 정보(n, S1, S2, t)를 포함합니다.
- 경고-알림 구간에서는 데이터 인스턴스들도 저장되지만 해당 인스턴스가 어느 클러스터에 매핑되어있는지 나타내는 문자열인 Genotype도 저장. 

# Simplified Silhouette
- 인스턴스 대 clsuter 비교를 클러스터 중심만으로 계산하도록 간소화한 실루엣 계수로, 높을 수록 잘 할당된 값.


# Page–Hinkley Test
- Distribution drift를 감지하기 위한 통계적 기법으로, 
- 현재 관측값과 현재 시점까지의 평균의 차이 합mt와 현재까지의 최소 mt 값의 차이가 임계값이상 발생하면 데이터 분포가 변했다고 판단

# Evolving Cluster
- 알람 임계를 넘으면 버퍼의 데이터들에 대해 새로운 클러스터링을 수행
- 종료조건 Sc 만족할 때 까지, 매 회 k =[0, root(N)]에서 랜덤선택된 k에 대해 k-means 최대 iter번 반복
- SS값이 가장 좋은 클러스터는 유지하고, SS 나쁜 만큼 큰 확률로 진화 알고리즘 대상 클러스터 샘플링하여 제거 연산 MO1 또는 분할 연산 MO2를 적용
- Mutation Operator
  - MO1 랜덤 h회 반복(1<=h<=k-2)
    - 클러스터 별 실루엣 계수 계산 이 실루엣계수에 반비례해서 샘플링 확률 설정, 선택된 클러스터 삭제
  - MO2 랜덤 h회 반복(1<=h<=k-2)
    - 클러스터 별 실루엣 계수 계산 이 실루엣계수에 반비례해서 샘플링 확률 설정
    - C에서 랜덤 샘플 x1, x1에서  가장 먼 샘플 x2선정
    -   두 샘플이 새로운 클러스터 중심이 되어 클러스터 재할당  
  - MO1, MO2 적용할 때 적합도가 증가할때까지는 유지하고 감소하면 연산자 종류 바꿈.
    - 이전 세대에서 돌연변이 겪지 않은 경우 두 연산자 중 하나 랜덤 적용
---

# FEAC
- 클러스터 k개를 유지하면서 클러스터가 업데이트 됨
  - 3가지 컴포넌트로 구성
    - Streaming Clustering
      - CluStream, StreamKM++
  - K-Estimation
      - OMRk , BkM    
- Genotype encoding
  - cluster prototype(medoids, relational centroids 등)을 인코딩한 것
  - 아무튼 원형이 유전자형에 잘 저장되서, 나중에 원형 복원해서 해당 파티션을 복원하는 게 중요
- Local Search
  - EAC에서 보통 5번 반복하면 종료해도 되더라..
- Mutation Operator
  - MO1(제거할 클러스터 선택) 
  - MO2(클러스터 분할)

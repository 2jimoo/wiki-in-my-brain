- [Overview](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/FEAC-Stream.md#overview)  
- [3가지 컴포넌트로 구성](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/FEAC-Stream.md#3%EA%B0%80%EC%A7%80-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EB%A1%9C-%EA%B5%AC%EC%84%B1)  
- [동작과정](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/FEAC-Stream.md#%EB%8F%99%EC%9E%91%EA%B3%BC%EC%A0%95)  
- [FEAC]()


---

# Overview
- Fast Evolutionary Algorithms for Relational Clustering(FEAC)의 개선 버전
- 특징(Data Stream Clustering A Review참고)
  - Auto cluster count
  - Damped window
  - Hypersherical
  - Online
  - High dimensional data suitable
  - No expert knowledge
  - Drift adaptation
  - Outlier

# 3가지 컴포넌트로 구성
- Streaming Clustering
  - CluStream, StreamKM++
- Drift Detection
  - Page–Hinkley Test
- K-Estimation
  - OMRk , BkM 

# 동작과정
- 초기 L개 데이터 인스턴스 수집, 최초 클러스터링 수행
- 새로운 데이터 x도착시 가장 가까운 클러스터에 할당 시도, 오래된(가중치 낮은) 클러스터 삭제됨
- PH 테스트 수행  
  - 경고 (임계값1) 진입 시 버퍼에 인스턴스 저장
  - 임계값2 넘으면 개념변화 발생 의미
- 진화 알고리즘 수행
  - 클러스터 중심 삭제 및 병합 과정(FEAC 참고)
  - Simplified Silhouette로 일정 범위 k중 품질 가장 좋은 값 선택
 

# FEAC
- Genotype encoding
  - .
- Local Search
  - .
- Mutation Operator
  - .

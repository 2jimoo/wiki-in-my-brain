# Materials
https://drive.google.com/file/d/186PxfqUDVDFZZWWfg_PvaDyDo4HCWcLd/view?usp=sharing

# Content
### 1p-2p
DR과 GR의 Out-of-Distribution를 다룬 두 튜토리얼을 대상으로 정리했습니다.
IR의 전체적인 패러다임과 히스토리를 다룬 후에, IR OOD에 대한 이슈 정의, 접근법들과 대표 모델들, 평가지표, 데이터셋, 끝으로 궁금한 점, 더 공부해볼 것들 공유하고 마무리하겠습니다.
### 3p
선정한 튜토리얼은 IR의 견고성 관점에서 지속적학습을 다루는 Robustness Information Retrieval와 Generative Retrieval 관점에서 지속적 학습을 다룬 Recent Advances in GIR입니다.
### 4p
IR이란, 쿼리 q에 대해서 연관도 기준 Top-K 정보 자원을 반환하는 태스크입니다.
현재 가장 대중적인 방식은, 미리 문서들을 임베딩 해놓고 쿼리가 들어오면 쿼리를 임베딩해서 가장 유사한 거 몇 개 를 랭킹하는 파이프라인  기반 Dense Retrieval 입니다.

### 5p
그런데 이제 현실시스템에서는 IR 성능에 문제되는 상황이 발생합니다. 
학습 데이터랑 동일한 분포인데도 쿼리마다 성능 편차가 넘 큰 경우도 있고, 
학습 데이터랑 다른 분포가 되면서 성능이 저하되는 경우도 있고요,
악의적인 데이터나 조작이 개입되어서 결과가 달라지는 경우도 있습니다.
저희 같은 경우는, 데이터가 계속 유입이 되면서 성능이 저하되는 OOD문제가 가장 가능성이 높기 때문에  OOD문제를 중심으로 어떤 종류의 Problem setting이 있고 각각에 대해 어떤 접근법이 있는지 보려고 합니다.

### 6p
OOD문제 분류
- 새로운 데이터에 적응
- 기존 데이터셋이 변하는 경우
IR 패러다임
- DR: 앞서 쿼리와 문서간 임베딩 유사도 비교하고, 순위 매기는 multi-stage
- GR: 위 파이프라인을 1개 모델로 통합해서, 쿼리 들어오면 바로 관련문서 id들을 출력하는 방식
이 두가지 분류의 조합으로 접근법이 달라지는데, GR에 대해서 먼저 살펴볼 필요가 있겠습니다


### 7p
DB로 치면 DR은 query value를 full scan해서 제일 비슷한 doc value을 찾는 방식인데, GR은 역색인 처럼 query로부터 index를 생성해서 바로 연관된 문서를 찾는 겁니다
그래서 학습은 느린데 추론 당시에는 매우 빠르고, 저 multi-stage 방식에서는 indexing과 retrieving을 각각 최적화하는데 이 각 성능으 최적화가 최종 성능의 최적이라고 보장할 수 없으니 아예 두 단계를 통합해서 학습하면 전체 과정을 최적화할 수 있다는 겁니다. 그리고 DR보다 model capacity에 의한 성능 향상을 크게 영향받습니다.
이제 GR에서는 그럼 Doc ID에 Doc의 전체적인 의미를 어떻게 담을지가 중요해집니다.
### 8p
그냥 임의 숫자를 붙여서 쿼리-숫자 예측 문제로 접근하기도 하고요
임의 숫자 문자열을 LM으로 생성하게 하기도 합니다. 기존 LM의 임베딩을  활용하기 때문에 넓고 희소한 정수 space가 아니라 dense embedding space로 매핑해서 코퍼스의 크기에 영향을 덜 받습니다.
한 단계 더 나아가서 이 id sequence를 생성하는 LM decoding step마다 의미적인 계층을 부여하기도 합니다. 어떤 의미냐면
110라는 아이디에서 첫 자리 보니까 소설이네, 두번째 자리보니까 한국소설이네, 마지막 자리보니까 그중 무슨 제목이네.. 이런식으로 LM의 디코딩 스텝을 search space의 subspace로 좁혀서효율적인 탐색을 할 수 있다는 아이디어입니다.
그럼 그 의미를 무슨 정보 기반으로 만들건데에 따라서 종류가 엄청 많아집니다..

### 9p-10p
이와 같이 도큐먼트 아이디가 도큐먼트에 의해 미리 결정되는 경우에는, 문서간의 관계를 표현하지 않기 때문에 GenRet, NOVO와 같이 ID를 학습하는 모델도 있습니다.
코드북은 이산 잠재 공간의 중심(centroid) 역할을 하는 임베딩 벡터들의 집합으로 구성됩니다.
각 시간 단계 ( t )에서, 코드북의 임베딩 벡터들과 문서의 잠재 표현 ( d_t ) 간의 내적을 계산하여, 가장 높은 내적 값을 가지는 임베딩 벡터를 선택합니다.

### 11p
single ID inference
한 문서당 아이디가 하나인 경우는 id를 구성하는 토큰 순서가 유의미한가, 무의미한가에 따라 다른자료 구조와 서치 전략을 쓰긴하지만 대체적으로 LM 디코딩방식과 동일하게 진행하고요
Beam Search 가능한 모든 다음 예측 단어들 중에서 가장 높은 확률을 가진 상위 k개 선택, 제약 조건을 설정하여 생성되는 토큰이 특정 규칙이나 조건을 만족
Greedy Search는 시퀀스 생성 문제에서 매 단계마다 가장 높은 확률을 가진 단어(또는 토큰)를 선택하는 간단한 알고리즘, 제약 조건을 설정하여 생성되는 토큰이 특정 규칙이나 조건을 만족

### 12p
multiple IDs inference
문서당 부분 부분 쿼리랑 매칭시켜서 여러 아이디를 가지게하고 싶은 경우에는 이 N-gram의 내용을 집계해서 문서 수준 id를 만듭니다. 

### 13p
지금까지 DR과 GR의 컨셉이 어떻게 다른지 알아보았습니다. 이 후 OOD 접근법은 지속적 학습에 대한 내용들이 대부분이기 때문에 정적 데이터셋 학습 방법론부터 어떻게 다른지 recap 해드리려고 합니다.
DR은 문서 컨텐츠와 쿼리를 각각 임베딩하는데 의미적으로 유사해지도록 positive-pair과 negative-pair에 대해 대조학습을 수행합니다. 
GR은 Loss가 인덱싱과 리트리빙에 대해 2개 항으로 구성되어 동시에 학습합니다. 3가지 개선된 손실함수 정의가 있습니다.

### 14-16p
그래서 다시 OOD 문제로 돌아오자면… OOD 중에서도 새 문서에 대한 적응 접근법은 4가지 방식으로 구분됩니다.
새로운 데이터랑 비슷한 걸 모델이 직접 만들어서 학습하는 Data Augementation방식이 있습니다. 대신 그 데이터가 저품질이라는 단점이 있죠
Domain modeling 방식은 소스 도메인에서의 일반화 능력을 최대화해서 타겟 도메인에서의 성능 향상을 도모하자는 전략입니다. 대신 이 방법은 추가 프로세스가 들어가면서 복잡해집니다
GPT-4o랑 Mixtral에서도 사용한  MoE를 적용하는 Architecture 접근법이 있습니다. 어떤 도메인이 있을지 미리 파악해야한다는 제한이 있습니다.
모델을 키워 일반화능력을 도모하려는 Capacity적 접근법도 있습니다.
Analysis
BEIR는 다음과 같은 다양한 데이터셋으로 구성되어 있습니다:
MS MARCO: 마이크로소프트에서 제공하는 대규모 웹 문서 검색 데이터셋.
TREC-COVID: COVID-19 관련 문서 검색 데이터셋.
NQ (Natural Questions): 구글의 질문 응답 데이터셋.
BioASQ: 생의학 문서 검색 및 질문 응답 데이터셋.
FiQA: 금융 및 경제 질문 응답 데이터셋.
Climate-FEVER: 기후 변화 관련 주장 검증 데이터셋.
새로운 데이터에 적응할 때는 아키텍쳐와 캐패시티로는 성능 향상이 제한적이므로, 데이터랑 도메인 모델링하는 쪽으로 접근해야한다는 게 결론입니다.

### 17p
그래서 Data Augmentation과 Domain Modeling의 대표적 아이디어를 정리해보았습니다.
GPL은 어떤 소스 passage로 의사쿼리 생성 후, 임베딩 유사도 기반 negative pair을 찾아 데이터를 증강합니다. InPars는 LLM을 사용해서 positive-pair 생성하는 방식입니다.
Contriever는 데이터의 품질 및 다양성의 부족을 해소하기 위해 positive-pair, negative-pair 각각의 생성작업을 모두 제시합니다. 텍스트에서 추출한 span이 어느 부분에서 나왔는지 추론해서 그 span의 앞뒤 부분을 positive로 삼거나(ICT), 동일 텍스트 내 다른 부분을 무작위로 positive로 삼거나 일반적인 텍스트 변형(랜덤 삭제/마스킹/대체)를 적용해서 쓰기도 합니다. 
동일 배치 내 다른 샘플들이나 일반화 성능을 위해 다른 배치의 샘플들을 negative로 사용했습니다. 

### 18p
COCO-DR은 두 가지 주요 스테이지가 있는데요, 먼저 소스 도메인 코퍼스에서 타겟 도메인 코퍼스로의 전체적인  데이터 분포 변화를 학습하기 위해 타겟 코퍼스에 대해 대조학습을 사전훈련합니다.
활용할 수 있는 타겟 도메인 쿼리는 소수이기 때문에 소스 도메인에서 일반화 성능을 최대화 하는 것으로 타겟 도메인에서의 성능 향상을 도모합니다. 일단 passage 임베딩 들을 클러스터링하고, 소스 쿼리에 대해 Dense Retireval 성능이 낮은 클러스터에 더 패널티를 부여하는 방식으로 전체 클러스터의 Dense Retireval 성능을 최적화하는 파인튜닝을 수행합니다. 

### 19p
기존 문서가 점진적으로 변할 때는 지속적 학습이 핵심입니다. 
dataset
MS MARCO 에서, Natural Questions(NQ)에서 60% 샘플링해서 기본 셋 만든 후 10%씩 새로운 샘플 데이터 추가하는 방식으로 새로운 데이터 도착하는 것 모사
LL-LoTTE, LL-MultiCPR은 시간에 따른 도메인 변화 모사
DR에서는 기존 데이터를 별도 메모리에 저장해놨다가 새로운 데이터가 들어오면 몇 개 샘플링해서 같이 학습을 하는 방식이 있습니다. 그리고 다시 새로운 데이터로 메모리를 업데이트 합니다.
GR에서는 id 클러스터를 어떤 임계값에 따라서 새로운 클러스터를 추가하거나 기존 클러스터를 업데이트하거나 유지하거나 이렇게 3가지 시나리오로 점진적인 인덱싱을 수행합니다.
두 접근법 모두 기존 지식을 유지하는데다가 새로운 지식이 기존 지식과 어떤 관련이 있는지 파악할 수 있습니다.
Analysis
기존 데이터의 변화에 적응할 때는 지속적 학습이 중요하고, 기존 데이터를 잘 활용하면 새로운 지식을 획득하는데 도움이 됩니다.  이를 위해서 가장 필요한 과거 경험이 뭔지 정확하고 빠르게 recall하기 위한 memory design이 중요하더라는게 결론입니다.

### 20p
각각의 매커니즘도 정리해보겠습니다.
L^2R은 DR에서 이하 4개 step으로 점진적으로 학습합니다.
우선 새로운 문서 셋에서, 관련 문서임에도 레이블이 없다는 이유로  negative sample로 뽑히지 않도록 PSS지표를 정의 합니다. negative sample 다양성을 향상시키기 위해 ISD를 정의합니다. 두 지표의 가중점수를 최대화하는 새로운 문서를 최종 negative sample로 고릅니다. 
어떤 문서 d 대비 d+(실제 q에 관련된 문서)가 얼마나 q와 관련성이 높은가
어떤 문서 d가 문서 집합 D내 다른 문서들보다 얼마나 쿼리 q에 대해 다른 정보를 포함하고 있는가
메모리의 기존 문서셋에 대해서도 학습하는데 이 때는 ISD기준으로 가장 정보이득이 큰 negative sample을 선택합니다. 
이렇게 쿼리에 대한 주어진 실제 positive sample과, 위 과정 샘플링 과정을 통해 결정된 negative sample들로 doc, query encoder를 대조학습합니다. 
메모리에 ISD 기준으로 샘플을 업데이트합니다. 

### 21p
CLEVER는 GR에서 기존의 인덱스를 기억하면서도 새로운 문서 인덱스를 추가하기 위한 매커니즘입니다.
D차원 도큐먼트 임베딩을 M개의 subspace으로 나누고, 각 subspace를 K개의 클러스터로 표현하여 초기 Product Quantization codebook을 구축합니다.
벡터 분할 (Subspace Partitioning)
서브벡터의 코드북 생성 (Codebook Generation)
벡터 양자화 (Quantization)
압축-재구성 손실이 큰 샘플들을 자주 부트스트래핑해서 집중적으로 BERT를 훈련합니다. 또한, 두 가지 동적 임계값을 기준으로 센트로이드를 추가, 업데이트, 유지할지 결정합니다. 이 기준에는 **representation-centroid average distance**와 **representation-centroid maximum distance**가 포함됩니다. 이 방법으로, 기존 문서와의 유의미한 연결을 만들 수 있습니다.
클러스터링 손실 (Clustering Loss): 문서 표현을 양자화 전후로 평균 제곱 오차(MSE)
대조 손실 (Contrastive Loss): 같은 문서 내 무작위 span(positive), 다른 문서 negative
https://staff.fnwi.uva.nl/m.derijke/wp-content/papercite-data/pdf/chen-2023-continual.pdf 

### 22p
L^2R과 비슷하게 메모리를 별도로 둬서 새로운 세션마다,  유사한 이전 세션들의 문서를 샘플링하여, 새로운 데이터와 함께 인덱싱을 학습합니다. 
GR은 리트리빙 능력도 새로 인덱싱하는 동안 보존해야하기 때문에  Pseudo query-docid pairs를 활용합니다. 쿼리 생성 모델로 문서에 대한 의사 쿼리를 생성한 후, 쿼리-문서 쌍을 샘플링하여 검색 모델을 훈련시킵니다.
이러한 요소들을 종합적으로 고려하여 CLEVER의 전체 훈련 목표는 다음 네 가지로 구성됩니다:
1. 새로운 문서 인덱싱을 위한 MLE 손실
2. 기존 문서 인덱싱을 위한 MLE 손실
3. 검색 손실
4. 정규화항
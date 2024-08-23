# Meterials
https://drive.google.com/file/d/1SJmgETXqg9SIXU-HsoRoUhSmDswNT8kc/view?usp=sharing

# Content
## 1-2P
논문 전개 순서대로 
연구 목적
관련 연구, 태스크 정의
방법론
실험 셋팅과 결과분석
요약
으로 구성했습니다. 추가적으로 시나리오 구상에 관련된 내용으로 마무리 하겠습니다.


##  3P
논문에서 해결하고자하는 문제 상황은 label이 없는 새로운 주제의 문서가 유입될 때 쿼리 성능과 학습 효율성을 개선하는 것 입니다. 
이를  위해
데이터 분포 변화에 적응하면서
모델이 업데이트될 때 마다 기존 문서를 다시 인덱싱하지 않고 새로운 문서에 대한 인덱스만 업데이트
하고자 합니다.

데이터 분포변화 적응을 위해 negative sample 선정 전략을 제시하고,성능 저하나 인덱스 재구축 비용을 최소화하면서 backward-compatibility를 보장하는 objective를 정의합니다. 
도메인이 변화하는 상황을 재현하는 벤치마크 구성방법도 제안하고 있습니다. 


##  4P
노테이션을 이해하기 위해 논문에서 사용하는 용어들과 태스크 정의를 먼저 정리해보았습니다.
###  Backward-Compatibility
논문에서 말하는 Backward-Compatibility는 이전 세션 데이터를 다시 임베딩할 필요없이, 이전 세션 때 생성한 임베딩을 재사용하면서 현재 세션의 데이터만 임베딩하면 되는 것을 가리킵니다.
주로 CV에서 서로 다른 모델간 임베딩이 호환되도록 학습하는 Compatible representation learning에서 출현한 개념입니다.
### Lifelong Learning
Continual learning, incremental learning이라고도 하는 Catastrophic Forgetting을 해소하기 위한 학습 방법론
- Regularization-method
  - Elastic Weight Consolidation(EWC): CLEVER의 Loss에서도 등장했는데요, 이전 태스크과 현재 태스크가 공유하는 파라미터 공간 내에서 조금씩 업데이트하는 방식이고
  - Architecture-based: DEN(Lifelong Learning with Dynamically Expandable Networks), 이전 가중치값을 임계치 이상 잊어버릴것 같으면 새로 노드를 복사해서 동적 네트워크 구성하는 방식도 있습니다.
  - Memory-based: L2R에서는 별도 메모리로 이전 샘플들을 replay하는 방식인 Memory-based방식을 취하고 있습니다.

### 5-6P
First-stage Retrieval
쿼리에 관련있는 문서셋을 만드는 작업을 First-stage Retrieval라고 합니다.
쿼리와 labeled 관련 문서셋이 train set으로 주어지고, 이에 대해 대조학습을 수행하여 초기 모델을 구축합니다.
이 작업에 서로 다른 분포의 문서셋이 t 세션에 걸쳐 도착하는 시나리오로 Lifelong learning이 적용됩니다.
이 때는 unlabeled 문서셋으로 모델이 업데이트 되고, 임베딩의 backward-Compatibility가 보장된다면 t 세션에 도착한 새로운 문서에 대해서만 인덱싱을 수행하여 모델을 업데이트 할 수 있습니다.
전체적으로는 4개 단계로 모델 업데이트가 수행됩니다. 각 단계에 대해 자세히 알아보겠습니다.


### 7P
우선, 새로운 문서셋과 메모리의 기존 문서셋에서 negative sample을 선택할 때 두 가지 지표를 사용합니다.
Positive Sample Superity
문서가 부정적인 문서일 가능성을 측정합니다.
쿼리 q에 대해 d+가 d보다 관련성이 얼마나 큰지, 주어진 positive sample 대비 sampling 대상 문서 임베딩의 쿼리 임베딩의 성분 크기와 방향을 판단합니다.
PSS가 클수록 d가 q와 무관함을 의미합니다.
Inter Sample Diversity
문서의 다양성 측정을 측정합니다.
문서 d의 임베딩에서 쿼리 q의 임베딩 관련없는 성분을 활용합니다.
ISD가 어떤 문서셋 D의 d가 다른 문서 d'대비 더 q와 무관함을 의미합니다.


### 8P
새로운 데이터 분포를 학습하기 위해, t 세션에 도착한 문서셋에서 negative sample을 선택합니다. positive sample은 labeled train set에서 주어집니다.
label이 없지만 positive가 아니면서
모델이 학습하는데 기여하는 구분하기 어려워야하고
샘플링된 샘플간의 다양성을 최대화 하기위해
아래 과정으로 선정됩니다.
BM25로 support sample이 아닌 데이터들을 제외하여 잠재 후보셋 Ds를 구성합니다.
여기서 PSS와 ISD 가중합이 최대가 되는 top-n1 샘플을 선택합니다.


### 9P
기존 데이터 분포 기억을 위해 이전 세션 t-1의 메모리에서 replay할 샘플을 선택합니다. 
이미 다양성이 최대화된 support sample이 저장되어있으므로 
새로운 샘플셋 대비 다양성만 고려하여, ISD가 최대가 되는 top-n2 샘플을 선택합니다.


### 10P
- Model update
이렇게 선정된 q, positive, negative sample들로 대조학습을 수행하여 모델을 업데이트합니다. 
backward-compatibility가 고려되지 않은 Loss입니다.
- Memory update
메모리 제한적이므로 support 최대화, 중복 최소화하는 샘플 저장해야합니다.
Mc에서 Mt-1의 무작위 k개 대비 ISD최대화하는 n3개 샘플 선정합니다.


### 11-12P
표준 대조학습 손실 대신 compatible learning이 적용된 Loss를 정의하여 사용합니다.
여기서 굵게 표시된 게 이전에 계산된 기존 임베딩, E/f로 표시된 항이 이번에 새로 계산한 임베딩입니다.
Vanilla compatible loss 
이전 문서(d+, Dmem) 임베딩 재사용하고, Dnew에 대한 임베딩만 계산하여 contrastive loss 계산합니다.
Dnew가 모두 negative sample이어서 새로운 데이터는 모두 negative라고 잘못된 상관관계를 학습할 수 있습니다.
Embedding-aligned learning
이전 문서(d+, Dmem)의 기존 임베딩과 재계산한 임베딩의 차이 최소화하여
이전 문서를 ft-1와 ft가 유사하게 임베딩하도록 유도합니다.
point-wise로 기존 임베딩에 가까워지도록 강제하기 때문에 새로운 데이터 표현 학습력이 떨어집니다.
Ranking-aligned learning
기존 임베딩으로 후보문서 예측한 분포, 새로 계산한 임베딩으로 후보문서 예측한 분포간의 차이 최소화하도록합니다.
기존 예측 분포라 함은, 새로운 샘플만 새로 계산하고 기존 샘플 임베딩은 재사용했을 때 softmax 출력값이고
현재 예측 분포라 함은, 모든 학습 샘플 임베딩을 새로 계산했을 때 softmax 출력값입니다.
Embedding의 point-wise 엄격성을 list-wise loss로 유연하게 대응하여, 새로운 데이터에 대한 학습능력 손상 방지할 수 있습니다.
최종 Loss
Vanilla compatible loss 에 aligned항을 적용합니다


### 13-14P
실험에 쓰인 벤치마크와 평가 메트릭을 알아보겠습니다.
우선 LOTTE와 Multi-CPR로부터 실험셋을 구성하는데 구성된 셋은 공개를 안 하고 있어서  LOTTE와 Multi-CPR의 구성을 먼저 보았습니다.

- LOTTE
총 5개 도메인으로 구성되어있고
qid, query, answer_pids로 구성된 쿼리셋과
pid, passage, 기타 메타데이터로 구성된 passage셋으로 되어있습니다.
Multi-CPR은 3개 도메인에 대한 중국어 데이터셋이고, LOTTE와 유사하게 구성되어 있습니다.

### 15P
LL-LOTTE는 LOTTE로부터 Session 별 데이터 변화 구성을 모사합니다.
초기 컬렉션 D0
공통 도메인(기술, 글쓰기)에서 70% 샘플링
급증 도메인(라이프스타일, 레크리에이션, 과학)에서 40%샘플링
세션 별 컬렉션 D1, D2, D3
각 세션에서 하나의 급성장 도메인을 지정하고, 해당 도메인 50%샘플링
공통 도메인에서 10%, 급성장 도메인을 제외한 나머지 도메인에서는 5%의 문서 추가하는 방식으로 구성됩니다.
쿼리에 관련 문서가 거의 positive labeling 되어 있어 unlabeled positive data부족한 문제를 해소하기 위해 OpenAI로 unlabeled positive data 생성하여 세션 모두 균일 분배했습니다.

###  16P
LL-MultiCPR도 이와 유사하게 Session 별 데이터 변화 구성을 모사합니다.
초기 컬렉션 D0
공통 도메인(전자상거래) 70%, 급성장 도메인(의학, 엔터테인먼트) 40% 샘플링
D1, D2
급성장 도메인에서 50%의 문서 샘플링
공통 도메인과 나머지 도메인에서 각각 10%, 5%의 문서를 샘플링
D3
급성장 도메인이 없고, 공통 도메인과 나머지 도메인에서만 샘플링
MultiCPR은 unlabeled positive data 꽤 있어서 증강

###  17P
평가 지표로 랭킹 퀄리티를 측정하기 위한 Recall, MRR, Success등이 사용되고 
보조 지표로 기존 정보의 재활용성을 측정하기 위한 Forget과 FWT가 사용됩니다.

###  18P
비교할 모델들은 Memory-based lifelong learning model들 4종류와,
초기 모델 구축 이 후 세션에서 아무런 업데이트가 진행되지 않는 경우,
 증분량에 대한 학습만 적용되는 경우, 
매 세션까지의 전체 데이터로 처음부터 학습하는 경우 3가지 경우로 나누어 진행됩니다.
Objectives 조합에 따라 4가지 경우에 대해서도 수행됩니다.

### 19P
초기 모델은 bert-base-uncased 기반 ColBert입니다.
Evaluation metric도 ColBert에서 사용된 k 값들을 사용합니다.
실험은 총 5개 케이스에 대해 진행됩니다.
Representation Compatibility에 의한 성능 차이
Memory Buffer Size에 따른 성능 차이
Data Selection Strategy별 성능 차이
Alignment Objectives별 Seen and Unseen Queries에 대한 성능 차이
전체 baseline대비 Auxiliary Metrics 비교


### 20P
지속적 학습을 위한 특별한 조치가 없는 경우
Initial
BM25보다 일반화 능력이 낮고 특히 분포가 급격히 변할수록 크게 성능 저하가 나타납니다.
신경망 기반 검색기가 BM25보다 내구성이 떨어진다

### 21P
Incre-train
메모리 기반 방법들보다 성능이 떨어짐(망각발생)
LL-MultiCPR에서 Initial보다도 성능이 떨어짐
레이블이 없는 관련 문서가 모델 업데이트에 악영향 줌 확인


### 22P
Retrain
CV와 같은 다른 지속적학습 작업과 달리,성능이 제일 낮고 특히 Recall에서 심함 

### 23P
메모리 기반 방법들 
기존 메모리 기반 방법들 중에서는 MIR이 두 데이터셋 모두에서 가장 좋은 성능
OCS에서 노이즈 데이터를 필터링하기 위한 그래디언트 기반 방법이 레이블이 없는 관련 문서를 필터링 하는데 영향이 없는 것 같다
L2R가 모든 데이터셋과 세션에서 가장 우수한데 제안된 데이터 선택 전략 덕분으로 추정합니다.

### 24P
강제된 임베딩 정렬이 새로운 데이터에 대한 모델 학습에 악영향 줌 확인
랭킹 정렬은 기존 기억과 새로운 지식 습득에 도움이 됨 확인
정렬항 추가 후 표현 계산 비용 절감도 확인됨

### 25P
L2Rrank의 성능 향상
메모리 버퍼 크기가 커짐에 따라 L2Rrank의 성능이 특히 정밀도에서  더욱 개선
기본 방법의 한계
기본 방법들은 메모리 크기가 커져도 큰 이득을 보지 못함
L2R은 다양한 지원 음성 샘플을 저장하여 메모리 버퍼 슬롯을 보다 효율적으로 활용하는데 무작위로 샘플링하는 경우 라벨이 없는 긍정 문서를 필터링할 수 없기 때문으로 추정됨

### 26P
- New Data Selection에 미치는 영향
  - PSS, ISD 제거시 검색 성능 크게 저하
  - 특히 Recall에서 성능 저하가 두드러짐
- Memory Data Selection에 미치는 영향
  - ISD 제거시 검색 성능 저하
  - 서로 다른/상충되는 데이터로 이루어졌을 때 새로운 지식 학습과 기존 지식을 유지 학습의 균형에 도움이 됨
- Memory Update에 미치는 영향
  - 메모리에서 샘플을 무작위로 교체할 때 LL-LoTTE의 성능 저하가 LL-MultiCPR보다 적음 
  - LL-MultiCPR이 더 작은 버퍼에 비중복 샘플을 저장하는 것을 목표로 하기 때문으로 추정


### 27P
전체적 경향
보았던 쿼리에서 S@5에서 더 높은 성능, R@100에서는 낮은 성능
보았던 쿼리가 보지 않았던 쿼리보다 더 많은 관련 문서를 가지고 있기 때문

동일한 이유로,
L2R Embedding-aligned에서는 보았던 쿼리와 보지 않았던 쿼리 모두 성능이 크게 떨어짐
L2R Ranking-aligned에서는 보지 않았던 쿼리에 대해 성능이 향상

### 28P
L2Rrank가 Forget, FWT 모두 가장 뛰어났고 L2R 전체적으로 GSS, OCS, MIR보다 훈련 비용이 낮음

### 29P
논문에서 추가연구로 제시한 바로는
도메인 적응에 제안된 방법들이 평생 학습에서도 잘 작동하는지 조사 필요
현재 방법은 급증하는 주제와 관련된 쿼리를 처리하기 위한 전문 기술이 부족(?)


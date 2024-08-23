# Repository
- https://github.com/2jimoo/selfcheckgpt
# Materials
- https://drive.google.com/file/d/10MnN6i49k-wkT9cIiU5bjWkDQNLFzOTO/view?usp=drive_link
--

## Index
해당 논문을 선정한 목적을 말씀드리고, 논문에서 제안한 방법론을 간략히 소개하겠습니다. 기존 방식을 베이스라인으로 저희 개선안을 비교한 실험 데이터를 보여드리고 결과 분석 및 추가 연구 방향을 제시하는 것으로 마무리하겠습니다.

## Purpose
SelfCheckGPT는 할루시네이션 감지 알고리즘으로, LLM의 최종 텍스트만을 얻을 수 있고 학습이나 RAG에 사용된 데이터를 모르는 경우 응답 텍스트들의 일관성만을 측정하여 할루시네이션 여부를 결정합니다. 논문에서는 GPT-3와 Llama를 활용한 Proxy-tuning과 비교해본 결과, 최종 토큰의 확률분포를 활용하는 것보다 응답 텍스트 간 일관성 비교가 할루시네이션 감지 정확도가 높았다고 보고합니다. 또한, 상용 서비스들이 모두 직접 LLM을 운영하는 것은 아니기 때문에 항상 토큰 확률 분포를 얻을 수 없습니다. 출력 텍스트만을 활용하면서도 성능이 좋은 블랙박스 할루시네이션 탐지 기법이라는 이유에서 실용성이 높은 아이디어라고 생각하여 해당 논문을 선택하게 되었습니다.

## Literature Review
실험 설계를 먼저 설명드리는 것이 논문의 방법론을 이해하는 데 도움이 될 것 같아 실험 데이터와 지표를 우선 말씀드리겠습니다. 검증하고자 하는 응답 R과 동일 쿼리에 대한 응답 샘플들 S의 문장 유사도를 일관성 점수로 정의합니다. 문장 유사도의 평균으로 문단의 일관성 점수를 정의합니다. WikiBio longtail 데이터로 인한 할루시네이션 현상을 배제하기 위해 빈도 상위 20% 중 랜덤 238개에 대해 GPT에게 기사를 작성시켰습니다. 기사의 문장마다 수동 레이블(Major Inaccurate/Minor Inaccurate/Accurate)을 매깁니다. 문장 단위 평가(PR-AUC), 문단 단위 평가(PCC)를 수행합니다. 논문에서는 PCC 인간 레이블과 평균 문장 점수 간 피어슨 상관 계수와 스피어먼 순위 상관 계수 값을 확인합니다.

## Proposed Method
총 5개의 일관성 정의 방법이 제안되어 있습니다. 

1. 1에 가까울수록 할루시네이션 가능성이 높은 방법:
   - 문장 Ri와 최대로 일치하는 문장 Si의 임베딩 유사도 평균 (RoBERTa-Large 사용)
2. MQAG 프레임워크로 쿼리에 대한 선택지 생성 후, 다른 선택지가 선택되는 비율
3. R과 S들로 N-gram 모델을 훈련하여 토큰 확률 모방, 가장 낮은 또는 평균 토큰 확률값
4. 전제 S들이 주어졌을 때, 문장 Ri가 모순일 확률 (DeBERTa-v3-large 사용)
5. S들이 프롬프트에 컨텍스트로 주어졌을 때, LLM이 Ri가 거짓이라고 응답하는 비율

## Experiments
GreyBox 접근법보다 평균적으로 성능이 좋습니다.

### GreyBox 
최종 layer의 token probability(또는 logits)를 알고 있을 때의 방식입니다.

### Proxy-tuning:
LLM의 logits에 완전 접근 및 학습 가능한 LM의 logits를 더해서 디코딩 되는 확률 분포를 조작하는 방식입니다. 프록시 LLM은 엔트로피 H 측정치가 확률 측정치보다 더 나은 성능을 보이고 있습니다. 성능이 좋지 않은 이유는 LLM들이 서로 다른 생성 패턴을 가지기 때문이라고 추측됩니다.

### Unigram이 BERTScore보다 성능이 좋다
흥미롭게도, 가장 계산 비용이 적게 드는 방법임에도 불구하고, unigram(max)을 사용하는 방식이 성능이 좋았는데, 이는 샘플 전체에서 가장 낮은 발생 빈도를 가진 토큰을 선택하기 때문으로 추측됩니다.

### 성능과 비용을 절충했을 때 NLI가 좋다
LLM Prompt가 제일 성능이 좋기는 하지만 Passage level에서도 성능이 좋은 NLI를 추천하고 있습니다.

### 한 응답 검사 시 샘플은 4개 이상부터 성능이 수렴한다
저희 실험은 이 결과를 바탕으로 응답 당 샘플 5개로 설계했습니다.

## Our Proposal
이 실험 결과를 바탕으로 저희는 기존 방식 2개를 강화하고, 새로운 아이디어 하나를 추가하여 총 3가지 방법론을 제안합니다.

1. 추가 데이터셋으로 fine-tuning된 NLI 모델인 HHEM을 사용하여 NLI Scoring을 개선한 HHEM Scoring을 제안합니다.
2. BERT보다 문장 임베딩 성능이 뛰어난 SBERT를 사용하여 BERT Scoring을 개선한 SBERT Scoring을 제안합니다.
3. BERT보다 Unigram 모델이 성능이 좋았던 점을 착안하여 도메인 지식 모델링하여 비문맥적 정보를 활용하도록 OpenIE를 적용한 OpenIE Scoring을 제안합니다.

## HHEM Scoring
HHEM Scoring은 HHEM(Hughes Hallucination Evaluation Model)을 사용합니다. HHEM의 베이스 모델은 논문에서 사용한 Microsoft DeBERTa-v3와 동일하나, FEVER, Vitamin C, PAWS와 같은 Factuality 데이터셋에 대해 fine-tuning하여 NLI 성능을 향상시켰습니다. [0, 1] 사이 값을 출력하는데, 0에 가까울수록 환각이고 1에 가까울수록 사실과 일치합니다. 문장 Ri에 대해 Samples가 전제로 주어졌을 때 HHEM의 평균 출력값을 O라고 했을 때, 1-O을 일관성 점수로 사용했습니다.

## SBERT Scoring
SBERT(Sentence BERT)는 BERT Siamese(또는 Triplet) 네트워크를 활용하여 BERT pooling보다 의미론적 문장 임베딩을 생성하는 모델입니다. 해당 모델은 SNLI 데이터셋과 Multi-Genre NLI 데이터셋으로 파인튜닝됩니다. 우선 문장 Ri의 SBERT 임베딩과 Sample의 문장 Si의 SBERT 임베딩의 코사인 유사도를 구하고, Sample별 최대 유사도 값들의 평균 M을 구했습니다. 1-M을 일관성 점수로 사용합니다.

## OpenIE Scoring
OpenIE(Open Information Extraction)는 자연어 텍스트로부터 triples(주체, 관계, 객체) 형태로 구조화된 정보를 추출하는 기술로, 통계적 방식(ex. Stanford OpenIE) 또는 신경망적 방식(ex. IMoJIE)이 있습니다. 이번 실험에서는 Stanford OpenIE 내부에서 N-gram이 사용되고 있기 때문에 Stanford OpenIE를 사용했습니다.

### OpenIE Scoring
문장 Ri와 Sample에서 triples를 추출하고 triple의 각 단어를 임베딩했습니다. triple 간 최대 유사도 T를 구하고 1-T를 일관성 점수로 사용했습니다.

## Experiments - Detecting False
동일한 데이터셋과 레이블에 대해 PR Curve AUC 값을 비교한 결과, 문장 전체가 잘못된 데이터에 대해 NLI Scoring이 가장 높은 성능을 보여주었습니다.

## Experiments - Detecting False*
문장 일부가 잘못된 데이터에 대해 BERT Scoring이 가장 높은 성능을 보여주었습니다.

## Experiments - Detecting True
문장 전체가 사실인 데이터에 대해 N-gram Scoring이 가장 높은 성능을 보여주었습니다.

## Experiments - PCC
문단 단위로 예측 레이블과 인간 레이블과의 선형관계를 비교한 결과, NLI가 가장 인간과 유사했습니다.
```python
slope, intercept, r_value, p_value, std_err = scipy.stats.linregress(human_passage_scores, selfcheck_passage_scores)
```
회귀 직선의 기울기와 절편, 상관 계수(피어슨), p-값, 표준 오차 등을 확인합니다.

## Conclusion
### Further Research
1. Fine-tuned NLI 성능이 왜 더 떨어지는가?
   - Threshold가 부적절
   - 빠른 학습/추론 속도를 위해 성능을 희생
2. 문장 임베딩 능력이 더 좋은 SBERT 성능이 왜 더 떨어지는가?
   - Bi-Encoder 특성상 텍스트 정보 손실로 인해 비교가 어려움
3. 도메인 지식 비교는 왜 성능이 더 떨어지는가?
   - 가설 1: 도메인 지식 추출 능력이 부족
   - 가설 2: N-gram이 성능이 좋았던 이유는 토큰 분포를 모사했기 때문

### Contribution
- 상용 모델 검증, 상용 적용 시 NLI 사용 권장
- 토큰 분포 불확실성과

 텍스트 일관성 비교가 모두 필요함
- Active learning과 결합하여 자동 요청에 사용할 수 있으며, hard negative mining으로 재학습에 활용 가능

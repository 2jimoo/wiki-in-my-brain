- [Contrastive learning with hard negative samples](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/Negative_Sampling.md#contrastive-learning-with-hard-negative-samples)
- [Conditional Negative Sampling for Contrastive Learning of Visual Representations](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/Negative_Sampling.md#conditional-negative-sampling-for-contrastive-learning-of-visual-representations)
- [Etc.](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/Negative_Sampling.md#etc.)
  - Diversity
  - Uncertainity
   
---
# Contrastive learning with hard negative samples
### Self-supervised learning ⊂ Representation learning for downstream task
- Generative model
- Self-prediction model
- Innate Relationship
- Contrastive learning

### Hard Negative Sample
- 분류가 어렵고 label이 없는 데이터가 아주 많다면?
- 유사하지만 엄연히 다른 클래스(Weak Negative) <-> True Negative(Strong Negative)
- Challenge
  - True Similarity/Dissimilarity 정보 없음
  - True distribution에 대한 효율적 샘플링 전략 필요

# 좋은 Negative Sample이란?
- ***Principal 1. True Negative 중 + Principal 2. Anchor과 유사한 것***
- False Negative trade off 존재
 
# Conditional Negative Sampling for Contrastive Learning of Visual Representations
- NCE를 CNCE로.
  - ***Positive로 분류되지 않으면서도 적당히 유사한 것***
- Ring Discrimicatoin Algorithm


# Etc.
- Diversity
- Uncertainity

- Loss
  - Classification
  - Regression
  - Contrastive learning
- Evaluation
  - Success@k
  - Recall@k
  - MRR@k
  - FWT vs Forget
- Measurements
---

# Loss
## MAE & RMSE
- 
## KL divergence
- 실제 분포 P에서 분포 Q를 근사할 때 얼마나 정보가 손실되는지
- 그래서 KL 다이버전스는 비대칭적

# Evaluation Metric
https://white-joy.tistory.com/11   
https://sungkee-book.tistory.com/11  
https://avalanche.continualai.org/from-zero-to-hero-tutorial/05_evaluation
## Success@k
- 추천 리스트에서 상위 k개의 항목 중 하나라도 올바른 결과가 있다면 1 그렇지 않으면 0
- k개 쿼리에 대해 평균을 구함
## MRR(Mean Reciprocal Rank)
- 처음으로 등장하는 선호 아이템이 몇 번째에 위치하는가의 역수를 취해서 높은 곳에 처음으로 등장할 수록 큰 점수
## Recall@k
- 전체 선호 아이템 중 추천 목록 top-K에 선호 아이템이 얼마나 존재하는지
## FWT vs Forget
- 특정 태스크에서의 정확도 변화
- FWT
  - Task A를 학습한 후 Task B를 학습했을 때 Task A의 성능이 얼마나 향상되었는지
    - acc_A_after_B - acc_A_after_A
  - 이번 학습에서 성공한 정답이 이전 학습에서 실패한 경우, 이전과 이번의 차이를 비율로 계산해 누적한 뒤 평균
    - (len(hit) - len(previous_hit)) / len(answer_pids)
- Forget
  - Task B를 학습한 후 Task A의 성능이 얼마나 감소했는지
    - acc_A_after_A - acc_A_after_B
  - 이전에 성공했던 정답이 이번에는 성공하지 못한 경우, 이전과 이번의 차이를 비율로 계산해 누적한 뒤 평균
      - (len(previous_hit) - len(hit)) / len(answer_pids)

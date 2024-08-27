[Tevatron, Trainer](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/library_code_review.md#tevatron-trainer)  
[PreTrainedModel, AutoModel](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/library_code_review.md#pretrainedmodel-automodel)  
[pickle](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/library_code_review.md#pickle)  

---

# Tevatron, Trainer
- Tevatron은 딥러닝 모델의 학습과 훈련을 위해 설계된 자동화 도구
- Hugging Face transformers의 Trainer와 비슷한데 언어모델 한정X


# PreTrainedModel, AutoModel
- 사전 학습된 모델의 구조를 정의, 사용자 정의 모델을 만들 때 상속하여 사용.
- 모델의 이름에 따라 적절한 모델 클래스를 자동으로 선택하여 로드

# pickle
- 파이썬 ***표준*** 라이브러리
- 파이썬 객체를 직렬화(파일에 저장)하고 역직렬화(파일에서 읽어오기)하는 기능을 제공

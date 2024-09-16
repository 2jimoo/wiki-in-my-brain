- [Tevatron, Trainer](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/library_code_review.md#tevatron-trainer)  
- [PreTrainedModel, AutoModel](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/library_code_review.md#pretrainedmodel-automodel)  
- [pickle](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/library_code_review.md#pickle)  
- [River](https://github.com/2jimoo/wiki-in-my-brain/blob/main/research-log/library_code_review.md#river)

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


# River
- Online처리 ML라이브러리
- https://riverml.xyz/0.21.1/introduction/installation/


# Pytorch and Tensor
- norm
  - 벡터의 크기
- torch.topk 
  - largest= False
  - values, indice(batch, k) : 텐서, 행(배치)별 최대 k 인덱스들
- dict에서 직접 키 순회하며 삭제하면 에러남, 키 복사 후 순회하면서 del
- 단일값 텐서는 .item()으로 스칼라값 얻을 수 있음
- 텐서(1, 768) N개 쌓기
  - cat (N, 768)
  - stack(N,1,768) 새로운 차원으로 쌓음
- squeeze 차원 없애기 <-> unsqueese

# RuntimeError: one of the variables needed for gradient computation has been modified by an inplace operation
- 왜 발생하는가
  - backward propagation이 되면 안 되거나 필요 없는 텐서에까지 grad = True거나, 변경되면 안 되는 텐서가 변경됨.
- 어떤 텐서만이 연산에 필요한가
  - 모델 파라미터, 모델 아웃풋, 레이블 그 외는 연산그래프에서 제외해야함
  - 그런데 requires_grad=True이면 다 포함되버림
- 어떤 조치를 취할 수 있는가
  - inplace 연산 제거
  - detach vs clone
    - detach는 연산그래프에서 제외되긴 하는데 원본 참조해서 원본 변경됨
    - clone은 별도 메모리로 복사되긴 하는데 requires_grad까지 복사해가버림
    - 값만 복사해서 별도 조작을 하고 싶다면 clone().detach()해서 전달해야함~
- 연산 과정 어떻게 확인할 수 있는가
  - 텐서의 grad_fn 속성 확인하기
  - torchviz로 연산그래프 시각화하기
  - backward 호출 수 loss의 grad 속성 확인하기

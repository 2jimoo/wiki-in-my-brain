# Presentation material
https://drive.google.com/file/d/14KRVnOge9p1Z_1D77Ru7-_Ov9jIVs4x6/view?usp=sharing

# Content

### LM History
- Seq2Seq란? 
순차적인 입력이 들어가는 Encoder에서는 입력들에 대한 정보를 담은 벡터인 Context vector를 출력합니다
이 벡터로부터 Decoder에서 순차적으로 토큰을 출력합니다. 
- Word Embedding이란? 
토큰을 나타낼 때 토큰별 클래스를 부여할 수 있습니다. 그러나 그러면 토큰 집합 크기만큼의 클래스가 필요하고 이는 토큰 표현의 비효율성과 차원의 저주를 발생시킵니다.
그래서 고정적 길이의 벡터로 모든 토큰을 표현합니다.
이 토큰을 표현한 벡터를 워드임베딩이라고 합니다.
- Attention이란? 
컨텍스트 벡터에는 인코더의 초기 입력일 수록 정보가 적게 담겨있습니다
이를 보완하기 위해 디코더에서 각 임베딩 당 인코더의 임베딩들에 대해 가중치를 매겨서 초기정보라도 현재 디코더 임베딩과 연관도가 크면 많이 반영하도록 하는 매커니즘입니다.
- Self-Attention이란?
이 어텐션 매커니즘이 인코더나 디코더 자기 자긴의 임베딩에 대해 수행되는 것을 셀프 어텐션이라고 합니다
어떤 토큰이 해당 시퀀스의 토큰들에 대해 가중치를 매겨서 연관도가 크면 많이 반영하도록합니다
어떤 토큰이 포함된 문자열에서의 역할을 학습하기 위한 매커니즘입니다.
- Transformer란?
어텐션과 셀프어텐션을 도입해서 RNN을 아예 언어모델에서 제거한 구조가 트랜스포머입니다.
순차적으로 들어가는 게 아니라 시퀀스가 한 번에 들어갑니다.
- BERT란?
BERT는 표현 학습에 초점을 맞춰 인코더에서 발전하게 된 모델입니다.
구멍을 뚫어놓고 주변으로부터 마스킹된 부분을 추측하는 태스크와, 두 문장이 주어졌을 때 실제도 연속해서 등장한 문장들인지 판단하는 태스크를 통해 임베딩을 학습합니다
이러한 pre-train task를 수행했더니 언어 생성 성능이 높아졌다는 Self-Supervised learning 언어모델의 전신입니다.
- GPT란?
GPT는 생성에 초점을 맞춰, 예측만 하는 부분인 디코더에서 발전하게 된 모델입니다.
제로샷 러닝, 프롬프트 러닝, 퓨샷 러닝 등의 기법들이 적용  되면서 GPT가 등장하게 됩니다. 
GPT에 적용된 기법들에 대해 알아보겠습니다.
- Prompts란?
모델에 들어가는 입력입니다
사용자 입력이 직접될 수도 있고 가공되어 들어갈 수도 있습니다.
- Completions란?
모델의 출력입니다.
몇가지 프롬프트 엔지니어링 기법을 살펴보겠습니다.
Role/Persona를 부여하는 방법입니다.
모델에게 콘텍스트, 모더레이션, 응답형태 등에 대한 정보를 추가로 제공하는 메시지입니다.
CoT는 단계적으로 태스크를 나눠 접근하도록 문장을 추가해주거나 지정해주는 방법입니다.
나눠서 접근하게 시켰더니 수학연산에 성공한 예제입니다.
MultiPersona는 여러개의 Role이 한 문제에 대해 논의하도록 설정하는 방법입니다.
예를 들어 인간들이 일자리를 모두 잃어서 충분한 소득이 없어지면 자본주의가 어떻게 되느냐에 대해 정치인 2명이 논의하도록 시킨 예제이고요, 아래는 인간에 적대적인 AI와 우호적인 AI에게 서로를 설득하라고 시킨 예제입니다.
어떤 주제에 대한 논쟁점 정의, 다양한 시각과 방안 등을 도출할 수 있습니다.

### Fewshot Learning
GPT에서 사용되어 BERT와 달리 학습없이 성능을 향상시킨 프롬프트 엔지니어링 기법인 퓨샷 러닝입니다.
퓨샷러닝과 제로샷러닝의 의미
일반적으로 학습방법으로서의 퓨샷러닝과 제로샷러닝의 의미를 알아보겠습니다.
퓨샷러닝은 레이블된 데이터가 부족한 경우, 적은 데이터로 학습또는 대응하기 위해 고안한 방법론들을 통칭합니다. 
대표적으로 대용량 레이블 데이터에서 학습된 모델을 타겟 도메인의 작은 레이블 데이터로 재학습하는 transfer learning이 있습니다.
제로샷러닝은 처음 등장한 클래스의 인스턴스에서 학습한 적 없는 클래스임을 인식하거나 대응하기위한 방법론들을 통칭합니다.
대표적으로 대용량 언레이블 데이터에서 임베딩이나 샘플의 분포등 표현방법을 학습하고 타겟 태스크를 수행하는 Self-supervised learning이 있습니다.
GPT에서의 퓨샷러닝과 제로샷러닝의 의미
GPT에서 퓨샷러닝이 적용된 부분은 프롬프트 러닝입니다. 몇 개의 입출력 예시를 모델에게 제공하여 성능을 향상시켰습니다.
아래 예제와 같이 제가 임의로 정의한 단어 게임 방식에 대해 예시를 주기 전에는 규칙을 파악 못 하다가 몇 개 예제를 준 이후에는 규칙을 찾아 단어 게임을 수행하는 것을 볼 수 있습니다. 
GPT에서 제로샷러닝이 적용된 부분은 GPT-2인데요, 번역같은 특정 태스크를 각각 집중적으로 재학습 시키는 대신, 학습셋과 모델크기를 매우 키움으로써 요약, 번역, 질의 응답 등 다양한 태스크를 모두 대응할 수 있게 되었습니다.
그리고 뒤에서 랭체인의 ReAct라는 동작방식에서도 ZeroShot이라는 말이 나옵니다. 언어모델이 어떤 태스크를 수행할 때 어떤 기능을 사용할지, 어떤 지침없이 기능별 기술된 디스크립션만을 보고 알아서 결정하도록 하는 방식인데요, 후에 다시 언급해드리겠습니다.


### GenAI Project lifecycle
- GenAI Project lifecycle Adapt란?
모델을 어플리케이션 목적에 맞게 어떻게 재학습 또는 타겟 도메인에 맞춘 응답을 하게 할지 결정하는 단계입니다.
사용할 수 있는 기법들은 크게 모델을 직접 업데이트하는 방법과 모델을 직접 업데이트하지 않고 학습 가능한 레이어를 추가하거나 외부 정보등을 활용하여 답을 구성하게 하는 방법이 있습니다.

- GenAI Project lifecycle Align이란?
윤리적, 법적 기준 준수 등을 포함하여 사용자의 기호에 맞게 응답하는 것입니다. 
인간의 피드백을 활용한 RLHF를 통해 사용자 기호에 맞게 학습시킬 수 있습니다.


### FineTuning이란?
학습 도메인으로 훈련된 모델을 현재 도메인에 맞춘 동작을 할 수 있게 현재 도메인 데이터로 학습시키는 방법입니다. 
전체를 학습시킬 수도 있고 일부는 파라미터를 고정하고 일부만 업데이트 시킬 수 있습니다.
대용량 데이터를 직접 활용할 수 있지만 요즘은 모델이 크고 연산만도 많은 리소스가 필요합니다. 그래서 효츌적인 파라미터 업데이트 방법론들이 등장하게 되었는데요,
그중 하나가 LORA입니다.
가중치 차분 업데이트 시 그 자체를 사용하는게 아니라 작은 차원으로 projection 후 다시 크기를 키우는 projection으로 대체
실질적으로 전체 파라미터가 성능에 미치는 것은 아니므로.
LORA이전에는 모델의 중간중간 학습가능한 레이어를 삽입하는 adapter방식이 있었는데, 추가 레이어까지 거쳐야 출력이 생성되기 때문에 latency issue가 있었습니다.
그런데 로라는 모델과 같은 레벨에서 병렬적으로 수행되므로 그러한 문제점을 해소했습니다.
컨텍스트 정보를 담은 prefix 벡터를 임베딩 벡터 앞에 삽입하는 방법도 있었는데, 넣을 수 있는 데이터양에 한계가 있고 성능이 불안정했다고 합니다.

### Catastrophic Forgetting
FineTuning 중 갑자기 이전 태스크를 급속하게 잊어버리는 현상 발생
Continual learning으로 이전 태스크 기억하면서 조금씩 바꾸는 방법들 제시
이전 태스크과 현재 태스크가 공유하는 파라미터 공간 내에서 조금씩 업데이트하기
이전 가중치값을 임계치 이상 잊어버릴것 같으면 새로 노드를 복사해서 동적 네트워크 구성하기
이전 태스크 데이터 다시 경험하기


### 정성적 평가
RLHF InstructGPT논문에서 소개된 기법
인간 labeler에 의해 선별된 고급 데이터로 LLM을 파인튜닝합니다.
LLM의 대답을 인간 labeler들이 랭킹을 매깁니다. 
이 선호도 데이터를 Reward model이 학습합니다.
텍스트에 대해 인간의 선호도와 예측한 선호도가 오차가 최소화되도록 업데이트 
prompt와 LLM이 생성한 reponse에 학습된 선호도에 따라 Reward를 반환합니다.

이 Reward 모델의 보상이 최대화되도록 LLM의 가중치가 업데이트 됩니다.
강화학습을 간단히 정리하면 
Action이란 에이전트가 취할 수 있는 행동으로, 여기서는 다음 토큰을 결정하는 행위입니다.
Policy란 에이전트가 취할 수 있는 모든 행동에 대한 확률분포로, 여기서는 토큰 시퀀스를 생성하는데 사용되는 단어사전의 확률 분포입니다.
상태별 최적의 Action을 취하도록 Policy를 업데이트하는 것을 Policy-based 방법이라고 합니다.
LLM업데이트 과정을 보면 업데이트 되는 LLM과 가중치가 고정된 LLM이 있는데
이는 과거 Policy와 현재 Policy를 비교하기 위해 모델 2개가 필요하기 때문입니다.
PPO loss는 강화학습에서 많이 사용되는 방식인데 구 정책과 현 정책이 너무 크게 차이나면 그걸 다 반영하지 않고 클리핑해서 조금씩 최적화하는 기법


### 정량적 평가
그런데 언어모델의 성능지표는 기존 머신러닝과 조금 다릅니다.
PPL
일반적으로 많이 쓰이는 지표인데,
토큰 시퀀스 어느 지점에서 다음 토큰을 결정할 때 몇개의 선택지를 고려하는 지 
많은 선택지를 고려할 수록 언어모델이 불확실해하는 것
즉 작은 값일 수록 성능이 좋음
BLEU
기계번역에서 특히 많이 쓰이는 지표
n개 토큰을 1개 단위로 하는 Precision, precision 값들은 기계입장에서 성능을 측정합니다
기계가 대답한 것중 실제 정답에 얼마나 포함되는지
ROUGE
요약에서 특히 많이 쓰이는 지표
N개 토큰을 1개 단위로 하는 Recall, recall은 실제 정답 입장에서 성능을 측정합니다
실제 정답중 기계가 얼마나 맞췄는지
두 지표의 차이는 분모인데요, 같은 의미같아 보이지만 구분해서 사용해야하는 경우는 오른쪽 그림을 보시면 
나는 무조건 짧게 요약하겠다며 a, the만 외치면 BLEU지표로는, 기계가 대답한 것이 실제 정답에 포함될 확률이 매우 높지만 품질은 형편없습니다.
그래서 그런 걸 포착하기 위해 ROUGE가 사용됩니다.


### 파인튜닝을 하면 문제점
최신 데이터 유지 어려움, 학습비용, 내부 정보에만 의존하여 할루시네이션
모델을 업데이트 하는 대신 외부 데이터 소스를 사용하자
그러한 인 컨텍스트 기법들
프롬프트 엔지니어 
많은 데이터 활용 힘들고 오래 기억하기 어려움


### 그래서 RAG 등장 
벡터DB에 외부 자료들을 저장해놓고
모델의 쿼리와 유사한 도큐먼트를 반환해서 답안 구성
벡터 데이터베이스는 문서등을 벡터로 매핑한 것을 저장해두는 DB입니다.
벡터 DB에 data가 저장과정은 크게 두 단계로 나눌 수 있습니다.
임베딩 모델을 거쳐 벡터로 매핑되는 과정이 수행됩니다.
의미적으로 비슷한 것끼리는 비슷한 값으로 임베딩됩니다.
벡터를 바로 저장하지 않고 빠르게 찾을 수 있도록 별도 자료구조로 인덱싱합니다.
큰 벡터를 작은 청크로 나눠, 각 청크 번호 아이디를 이어붙여 실제 벡터보다 훨씬 작은 ID벡터로 변환하는 PQ방식
해시 버킷 LSH방식
벡터를 노드, 유사도를 엣지로 표현해서 그리디하게 탐색해나가는 HNSW방식이 대표적인 인덱싱 방식입니다.
데이터를 조회하는 방식도 크게 두 단계로 나눌 수 있습니다.
쿼리를 저장할 때와 동일하게 인덱싱하여 인덱싱된 데이터들로부터 유사한 데이터를 찾아냅니다.
벡터간의 유사도는 cosine, 유클리디안, 내적 등으로 정의할 수 있습니다.
선택적으로 메타 데이터를 활용해서 탐색 범위를 줄이거나 결과를 필터링할 수 있습니다.


### LLMOps를 포함한 Foundation model를 사용한 어플리케이션 개발할 때 통용되는 워크 플로우
Foundation model Engineering
Pre-trained LLM
Data Embedding
Fine Tuning
Prompt Iteration
Prompt market
Prompt Tracking: 협업, 정리
Prompt Chaining: 외부정보 저장, 인덱싱, 컨텍스트 주입 등
Deploy and Monitoring
Test, Responsinle AI
User feedback 수집 및 앱 개선
차이점 
MLOps는 AI 모델의 개발, 배포, 최적화를 위한 플랫폼
FMOps는 이미 학습을 마치고 배포된 모델과 자사 또는 자신의 데이터를 결합하여 커스텀한 LLM 기반 AI 앱을 위한 플랫폼
엔드투엔드 보다는 매우 많은 모듈형 툴들이 있습니다.	
https://github.com/tensorchord/Awesome-LLMOps


### Langchain과 같은 LLM Application framework는 왜 필요할까요
이미지를 전달하면 어떤 인물인지 인식해서 관련 문서를 요약해서 인물에 대한 정보를 알려주는 이미지 인물 검색기를 구현하려고 합니다. 
언어모델 혼자만은 환경과 상호작용할 수 없습니다.
데모의 아키텍쳐를  보면서 각 Langchain component를 소개드리고 어떻게 다른 component들과 동작하는지 살펴보겠습니다.


- ChatOpenAI
대화형 언어모델입니다. 
메세지 투 메세지

- OpenAI
일반적 언어모델입니다.
텍스트 투 텍스트

- Agent 
환경에 액션을 취하고 상호 작용할 수 있는 LLM입니다. 
도구와 체인을 가지고 어떤 것을 사용할지 동적으로 결정하는 리즈닝 목적으로 LLM이 사용됩니다.
Tool은 API, 또 다른 모델 등의 특정 기능, Toolkit은 툴 콜렉션
Chain은 툴들과 에이전트의 실행순서가 미리 정의되어있는 번들입니다.

- AgentExecutor
에이전트와 실제 세상 사이의 중개자, 런타임 환경으로 볼 수 있다.  
에이전트로부터 요청이나 명령을 받아 기본 시스템이나 소프트웨어에서 수행할 수 있는 작업으로 변환합니다. 
행동의 실행을 관리하고 에이전트에게 피드백이나 결과를 제공한다.

- AgentTypes
ZeroShot
그 결정은 도구의 설명만을 기반으로 한다. 
ReAct를 사용하면 생각/행동/관찰 루프로 응답하도록 요청할 수 있습니다.
Conversational
컨버세이셔널 prefix가 붙은 모드는 대화 히스토리를 기억하고 기능은 동일합니다.


- PromptTemplate, MessagePromptTemplate
저희가 앞서 본 유저 입력을 가공하기 위한 component입니다.
System, User, Assistant role별 메세지 형태로도 제공합니다.
FewShot prefix가 붙으면 예시를 제공할 수 있습니다.
VectorStore
데이터를 벡터형태로 변환하여 벡터 스토어에 저장합니다.
query와 data의 similarity를 기반으로 검색할 수 있습니다.
도큐먼트를 조회해 올 때도 필요한 부분만 추출 및 압축해오도록 하는 식으로 효율적으로 리트리빙 할 수 있습니다.  


### LLM이 Agent의 추론엔진으로 사용된다는 의미
태스크를 수행하기 위해 LLM이 추론-액션-관찰을 반복하는 것을 볼 수 있습니다.
이러한 사이클을 반복하며 목적을 달성하는 방법론을 ReAct라고 합니다.


- Reasoning이란
생각과 그 답에 대해 평가하는 것을 프롬프팅을 통해 반복합니다.
내부정보로만 답을 평가하기 때문에 한계가 있습니다.
ActOnly란
실행 계획을 수립하고 실제로 수행합니다
그런데 그 결과들을 종합해서 최종 답안을 생성하는 능력이 떨어집니다.
둘을 섞어서 ReAct!
추론을 하고, 실행 계획을 수행하고, 그결과를 관찰해서 다시 추론을 하는 것을 반복합니다.
논문에서는 실패사례도 보여주는데 모델의 사고과정을 볼 수 있기 때문에 인간이 어떻게 교정해줘야할지 알기 편하다고 합니다.


- 랭체인에서 제공하는 PlanAndExecute Agent는 ReAct와 비슷하지만 조금 다르게 동작합니다.
Plan-and-Solve Prompting은 계획 단계에서만 step을 나누고 최종 계획에 대해 액션을 수행하는 Zeroshot CoT와 달리 태스크를 세부 계획과 실행으로 나눕니다.  
“Lets think step by step” 대신 “devise a plan”과  “carry out the plan”을 포함한 프롬프트 문구 삽입(https://verymuchmorethanastronomically.tistory.com/m/33)
이런 식으로 다단계 추론 방법이 발전하였고, 강화학습에서 LLM이 goal-driven agent로 사용되게 되었습니다. weight 업데이트하는 대신 언어적 피드백으로 language agent 를 강화하는 Reflexion방식 인데요,
PlanAndExecute Agent 공식문서에서 함께 소개된 Reflexion 오픈소스로 BabyAGI가 있습니다.
이로서 요즘 화두인 AGI까지 LLM이 어떻게 연결될 수 있었는지 알아보았습니다.

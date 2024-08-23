# Meterials
- https://docs.google.com/document/d/1zOlrYjEr4xB5BcGCuK_nOtr8nfW5V8C4Q5niLBWGk3o/edit?usp=drive_link

# Content
## Rationale for Selecting the Paper
### Meta Reinforcement Learning
- 메타 러닝은 AI 모델이 새로운 태스크에 빠르게 적응할 수 있도록 합니다. 이는 다양한 태스크에 대한 일반화 능력을 향상시키며, 특히 적은 데이터셋을 가진 태스크에서 AI 모델의 성능을 극대화하는 데 중요한 역할을 합니다.
- 따라서 강화 학습 분야에서 메타 러닝의 적용은 에이전트가 새로운 환경에서 소수의 훈련 샘플로도 효과적으로 학습하도록 하는 것을 목표로 하며 크게 두 갈래가 있습니다.
- 그라디언트 기반 접근 방식을 사용하여, 그라디언트 강하가 가능한 모든 모델에 적용될 수 있는 범용적인 알고리즘인 MAML과 RNN 기반으로 과거의 학습 경험을 기반으로 새로운 상황에 대한 최적의 행동 전략을 도출하는 RL^2입니다.
- MAML(Model-Agnostic Meta-Learning)가 모든 딥러닝모델에 사용될 수 있고 논문에서 8주차 강의에서 다룬 Policy Gradient를 강화학습 적용 예시로 제시하였기 때문에 선정하게 되었습니다.

## Proposed Method
### Approach Concept
- MAML의 목표는 여러 task에 빠르게 Adaption할 수 있는 최적의 Generalized parameter θ를 찾는 것입니다.
- 나중에 특정 task에 대해 학습을 할 때 최적의 parameter *θ로 빠르게 이동할 수 있게 됩니다.
- θ를 찾는 과정은 크게 Adaption 또는 learning이라고 부르는 inner loop와 Meta-learning이라고 부르는 outer loop 총 2중 루프가 반복되며 이루어집니다.
- inner loop에서는 각 task별로 gradient descent를 수행하여 모델 파라미터가 갱신되고, outer loop gradient descent에서 task별로 사용했던 loss function들을 선형결합하여 사용하기 때문에 loss합이 최소화되는 방향으로 θ를 gradient descent를 업데이트 되게 됩니다. 
- MultiTask-Learning과 다른 점은 이렇게 학습된 파라미터가 shared layer에서 공유되는 구조가 아니라 이 후 특정 태스크 학습의 시작점으로 제공된다는 것입니다.

### Algorithm
#### 노테이션 
- P(T): Adaption할 task들을 추출할 확률분포입니다.
- Ti: 샘플링된 adapt대상 task입니다. Adaption할 태스크 Ti의 data point batch에 대해 inner loop에서 GD를 수행하게 됩니다.
- fθ: parameter θ로 구성된 내부 루프 알고리즘 즉, 딥러닝 모델을 의미합니다.
- α, β: 내부/외부 경사하강 step size 입니다.
- L_Ti: adapt대상 task의 loss function입니다.
- the meta-objective function은 

#### 수행과정
- 전역 파라미터 θ을 랜덤값으로 초기화합니다.
- Ti~P(T) adapt대상 task의 data point batch를 샘플링합니다.
- the inner-objective gradient descent 수행합니다.
- θ → θ'i: 전역 파라미터로 초기화했던 θ가 점점 현재 task에 적응된 로컬 파라미터 θ'i로 업데이트 됩니다.
- batch에 대한 GD가 종료가 되면 현재 task에 대해 학습된 model fθ → fθ'i가 얻어집니다.
- the meta-objective gradient descent 수행
- task별로 업데이트된 모델 fθ'i 들에 대하여
- meta-objective function 최소화하도록 전역 θ을 업데이트합니다.

## Applying the Algorithm to Policy Gradient
- reference
  - https://hiddenbeginner.github.io/rl/2022/09/11/policy_gradient_methods.html 
  - https://www.mindscale.kr/docs/reinforcement-learning/policy-gradient 
  - https://developer-lionhong.tistory.com/20 
  - https://paperswithcode.com/method/reinforce 
- inner loop algorithm을 REINFORCE를 대입하여, RL에 MAML을 적용한 예시와 실험결과를 알아보겠습니다.
  - 전역 파라미터 θ로 초기 policy Φ0를 설정합니다.
  - inner loop에서는 이 정책 모델 Φ0로부터 각 task별로 목적 함수 J을 최대화하는 방향으로 Gradient ascent를 수행하여 adapted policy Φ’를 얻을 것 입니다.
  - 목적함수 J는 현재 정책 Φ의 상태가치함수이고, J의 그래디언트는 policy gradient theorem에 의해서 리턴의 기대값으로 모델링할 수 있습니다.
  - 현재 정책 Φ에 따라 에피소드들 각각 상태(S), 행동(A), 그리고 보상(R)의 시퀀스를 생성합니다.
  - 에피소드 당 time step 별로
    - 상태 s, 행동a 별 리턴을 구하여, 이 리턴의 표본평균으로 실제 그레디언트를 근사합니다.
    - 이 근사된 그래디언트로 정책 매개변수 θ를 업데이트합니다
  - 전역 파라미터 θ policy Φ도 동일하게 각 task별로 업데이트된 파라미터에 대해 목적함수 J를 선형결합 한 것을, 최대화하는 방향으로 업데이트합니다.

## Experiments
#### Setting
- MAML로 학습된 정책 모델은 ReLU를 활성화함수로 사용하는 2 layer 100 node 신경망으로,
- REINFORCE 사용하여, 1 policy gradient update당 20 trajectories(에피소드 시퀀스)을 사용했습니다.
- 최대 4회의 그라디언트 업데이트로 새로운 작업에 적응결과 확인하였고, 각 업데이트에는 40개의 샘플 사용되었습니다.
#### Baseline
- 사전 학습 후 파인 튜닝된 정책
- 처음부터 특정 태스크만으로 학습한 정책
- Ideal한 Oracle policy
#### Task
-  점 에이전트가 단위 정사각형 내에서 무작위로 선택된 목표 위치로 이동하는 2D Navigation
- 치타와 개미의 보행 방식을 시뮬레이션하는 Locomotion
#### Result
- 이상적인 Oracle policy에 MAML(초록선)을 적용한 모델이 가장 빠르게 수렴함을 볼 수 있습니다.


# Summary
## Contribution 
- 이 방법론의 장점은 이전 강화학습 Meta-Learning 접근법들과 달리 RNN 등의 새로운 모델이나 함수, 규칙을 따로 학습할 필요가 없다는 점과 그래디언트 기반의 업데이트를 취하는 모든 딥러닝 모델에 범용적으로 적용가능하다는 점입니다.
## Limitations 
- 그러나 논문에서 얼마나 비슷한 task여야하는지, 새로운 task sample은 얼마나 필요한지 등은 기준을 제시하지 않고 있고,어떤 pre-adaptation behavior가 Adapted policy에 기여했는지 이해하기 어려운 Credit Assignment Problem이 있습니다.  

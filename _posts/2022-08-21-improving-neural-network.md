---
layout: article
title: DeepLearning - Robust한 모델을 만들기 위한 방법론 (Hyperparameter Tuning & Batch Normalization)
mode: overlay
header: 
  theme: dark
article_header:
  type: cover
  theme: dark
  image:
    src: /assets/aws/autoscale/aws_autoscale.jpg
show_author_profile: false
comment: true
mathjax: true
mathjax_autoNumber: true
tags: deeplearning
---

> 해당 게시물은 코세라 딥러닝 특화 과정 중 Improving Deep Neural Networks 강좌를 듣고 정리한 글임을 밝힙니다.

# Building a Rubust Model
뉴럴넷 모델 학습은 한 번으로 끝나지 않는다. 하나의 데이터 셋을 반복적으로 학습(many epochs)시켜주거나 데이터를 증강해 모델이 지금까지 보지 보지 못한 데이터 셋으로 학습시켜줘야 한다. 즉, 반복적인 (iterative) 작업이다.    
세상에 존재하는 모든 데이터 (population)로 모델을 학습하는 것은 불가능하다. 따라서 우리는 일부 데이터 (sample)를 가지고 적절한 기술로 모델이 전체 데이터 (population)을 알도록 학습시키고 있다. 이처럼 전체 데이터를 아는 모델을 robust한 모델이라고 한다. 즉, 일부 데이터만 학습한 모델일지라도 새로운 데이터가 들어왔을 때 적절한 예측을 할 수 있는 모델을 말한다. 뉴럴넷 학습의 목적은 robust한 모델을 개발하는 것이고 그 과정은 앞서 이야기 했듯이 반복적인 (iterative) 과정이다.    
Bias와 Variance의 개념을 들어본 사람이라면 robust한 모델은 variance가 낮은 모델이라고 이해하면 쉽다. 예를 들어 학습 셋에서는 93%의 정확도를 내는 모델이 검증 셋에서는 정확도가 60%로 떨어졌다면 variance가 높다고 이야기 한다. 즉, 학습 셋 분포에 과적합되 (overfitting) 학습 셋의 노이즈까지 학습한 경우 variance가 높은 모델이고 robust하지 못하다고 말 할 수 있다.    

# Hyperparameter Tuning
뉴럴넷은 2가지 종류의 변수 (variable)가 있다.    
하나는 파라미터 (parameter)로 weight나 bias 처럼 back propagation시 업데이트되는 변수다. 즉, 파라미터는 매 학습 시 업데이트 된다.    
또 다른 하나는 하이퍼 파라미터 (hyperparamter)로 학습시 고정된 변수이다. 하이퍼 파라미터로의 종류는 다음과 같다.    

- learning rate ($\alpha$)
- 배치 사이즈
- 히든 유닛의 개수
- 옵티마이저 (optimizer)의 파라미터
  - Momentum ($\beta$)
  - Adam ($\beta_1$, $\beta_2$, $\epsilon$)
- 레이어 개수
- learning rate decay

하이퍼 파라미터 튜닝이란 학습 데이터 셋을 N번 반복해 학습할 때 (N epochs) 하이퍼 파라미터 값을 변경하며 어떤 값이 최고 성능을 내는지 실험하는 것을 말한다.    

## Importance of Hyperparameters
성능에 많은 영향을 미치는 하이퍼 파라미터를 순서대로 나열해보자.

> learning rate > 배치 사이즈, 히든 유닛 수, Momentum 옵티마이저 파라미터 ($\beta$) > 레이어 개수, learning rate decay  > Adam 옵티마이저 파라미터 ($\beta_1$, $\beta_2$, $\epsilon$)

## Choosing Hyperparameters Randomly
복잡한 뉴럴넷은 튜닝할 하이퍼파라미터의 개수가 많다. 따라서 하이퍼파라미터 조합의 개수가 많아지는데 이 때, 하이퍼파라미터의 값을 **랜덤**하게 선택하는 것이 좋다.    
예시를 통해 어떻게 하이퍼파라미터 값을 랜덤하게 선택하는지 알아보자.    
먼저 이산형 데이터 (discret data)인 레이어의 개수를 튜닝하는 경우를 보자. 레이어 개수의 최솟값과 최댓값 사이의 값을 균등 분포에서 추출하면 된다. 최솟값 2, 최댓값 4라고 할 때, `np.random.randint(2,5)`이다.    
하지만 연속형 데이터 (continuous data)인 learning rate를 튜닝하는 경우는 균등 분포를 사용하면 안된다. Learning rate의 최솟값이 0.0001, 최댓값이 1이라고 할 때 균등 분포로 추출하면 (0.1,1] 사이의 값이 나올 확률이 90%가 된다. 반대로 (0,0.1] 10%밖에 되지 않는다. 이럴 경우는 **로그 스케일**을 적용해 랜덤 추출해야 한다. 

<figure>
  <img src="/assets/deeplearning/scale.png" width="500" height="200">
</figure>

위 그림처럼 learning rate ($\alpha$)의 분포에 로그 스케일을 적용한 후 랜덤 샘플을 한다. `np.random.rand`는 [0,1) 값을 균등 분포에서 추출한다. 따라서 -4를 곱해 우리가 원하는 범위를 만들고 learning rate를 복원하기 위해 10제곱을 양변에 곱하면 learning rate 값을 얻을 수 있다.    

# Batch Normalization
Batch Normalization이란 모든 레이어 (l)마다 활성화 함수를 통과한 값($a^{[l]}$)을 정규화하는 방법을 의미한다. 배치 사이즈를 64로 설정했다고 가정했을 때, 64개의 미니 배치 데이터를 매 레이어를 통과할 때마다 정규화하는 것이기 때문에 "batch" normalization이라고 부른다.   

## Benfits of Batch Normalization
Batch normalization은 2가지 장점이 있다.    

1. Covariate shift 문제를 해결해 학습을(수렴을) 빠르게 함
2. 부가적으로 regularization 효과를 볼 수 있음

Covariate shift란 모델이 학습한 데이터와 실제 데이터의 분포가 달라지는 경우를 말한다. 예를 들면, 고양이 이미지를 예측하는 분류 모델을 만들었다고 했을 때 학습 시 검은색 고양이 데이터만 학습한 모델은 노란색 고양이가 입력됐을 때 예측에 실패한다. 검은색 고양이 분포와 노란색 고양이 분포가 달라졌기 때문이다.    
뉴럴넷의 모든 레이어는 이전 레이어의 결과를 받아 순차적으로 값을 계산한다. 즉, forward시 3번째 레이어는 2번째 레이어의 값에 weight와 bias를 곱하고 더하는 계산을 한다. 이 때 3번 째 레이어는 2번째 레이어의 결과 값이 고정됐다는 가정하에 y 값 예측을 가장 올바르게 할 수 도록 weight와 bias값을 업데이트한다. 하지만 back propagation때 모든 레이어의 weight와 bias가 업데이트 되기 때문에 다음번 배치에서 3번째 레이어가 받는 2번재 레이어의 결과 값은 달라지게 된다. 즉, 이전 배치에서 학습한 분포가 아닌 새로운 분포를 보게되는 것이다 (Covariate shift). 이를 방지하기 위해서는 매 layer의 결과값 ($a^{[l]}$)을 정규화해 다음 배치에서 값이 바뀌더라도 분포의 평균과 분산은 고정되도록 만들어주면 모델이 수렴하는 속도가 빨라진다.    

Batch normalization의 부가적인 장점으로는 regularization 효과를 들 수 있다. Batch normalization은 전체 데이터의 평균과 분산을 가지고 정규화하는 것이 아니라 mini-batch 단위로 정규화를 한다. 따라서 mini-batch마다 각 레이어의 분포에 어느 정도 노이즈를 추가한다. 이 노이즈가 약간의 regularization 기능을 한다. 만약 배치 사이즈를 늘리게 되면 regularization 기능을 약화시킬 수 있다.    

## Implement Batch Normalizaiton

<figure>
  <img src="/assets/deeplearning/batchnorm.png" width="900" height="200">
</figure>

위 사진은 3개의 hidden layer를 가진 뉴럴넷이다. l번째 hidden layer의 결과값을 $z^{[l]}$이라고 하자. Batch normalization은 $z^{[l]}$ 대신 정규화된 값 $\tilde{z^{[l]}}$을 사용한다. 

<figure>
  <img src="/assets/deeplearning/calculation.png" width="500" height="300">
</figure>

위 사진은 정규화 과정을 자세히 보여준다. 배치 사이즈를 m이라고 가정했을 때 미니 배치의 $z^{[l]}$ (위 사진에서는 $x_i$ 기호로 표시했지만 그 의미는 이전 레이어의 아웃풋에 weight와 bias를 곱하고 더한 값인 $z^{[l]}$를 의미한다.)의 평균과 분산을 구한다. 구한 평균과 분산으로 정규화된 값 ($\tilde{z^{[l]}}$)을 구하는데 이 때 분모가 0이 되는 것을 방지하기 위해 (numerical stability) 분모에 $\epsilon$을 더한다.    

여기서 하나 중요한 점은 batch normalization은 2가지 학습 가능한 파라미터를 갖는 점이다. $\gamma$와 $\beta$ 파라미터는 단순한 정규 분포 (평균이 0이고 분산이 1)를 스케일링하고 이동하는 역할을 한다. 즉, 평균과 분산을 변경한다.    
이렇게 분포를 변형하는 파라미터를 학습하는 이유는 활성화 함수 (activation function)의 비선형적 특징을 살리기 위해서이다. 예를 들어 sigmoid를 활성화 함수로 사용한다고 했을 때 정규 분포는 대부분의 데이터가 0 주위에 분포하는데 이 때 sigmoid의 값도 0주위에 분포하게 된다. 이를 방지하기 위해 $\gamma$와 $\beta$ 파라미터로 평균과 분산을 학습할 수 있다.    

# Conclusion
지금까지 robust한 뉴럴넷을 구현하기 위한 두 가지 방법론인 hyperparameter tuning 그리고 batch normalization를 알아봤다. 특히 hyperparameter는 모델마다 적절한 값이 다르기 때문에 반복적인 실험을 통해 적절한 값을 찾아야 한다. 또한 batch normalization은 pytorch에 구현되어 있어 사용하기는 쉽지만 이론적으로 학습에 어떤 영향을 주는 지 이해할 필요가 있다.    


# Reference
- [Batch Normalization Paper](https://arxiv.org/pdf/1502.03167.pdf)
- [Batch Normalization Blog](https://machinelearningmastery.com/batch-normalization-for-training-of-deep-neural-networks/)
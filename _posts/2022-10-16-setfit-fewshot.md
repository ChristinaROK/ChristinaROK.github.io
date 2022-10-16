---
layout: article
title: DeepLearning - Fewshot Learning (SetFit)
mode: overlay
header: 
  theme: dark
article_header:
  type: cover
  theme: dark
  image:
    src: /assets/tips/watch.jpg
show_author_profile: false
comment: true
mathjax: true
mathjax_autoNumber: true
tags: deeplearning
---

# Task Introduction

현업에서 모델을 학습시킬 때 겪는 가장 큰 문제점은 학습 데이터가 없다는 것이다.    
데이터를 가지고 있어도 데이터에 레이블(답)을 부여하는 작업은 시간이 많이 소요된다.    
최근 Few-shot Learning, 즉 이미 언어를 충분히 이해하고 있는 모델에 소량의 데이터만 학습해 유의미한 정확도를 내는 방법론에 관한 연구가 활발하다.   
오늘 알아볼 SetFit(Sentence Transformer Finetuning)이란 모델은 2022년 9월 공개된 논문으로 아주 간편하게 그리고 적은 데이터 셋으로 분류 테스크에서 SOTA를 기록한 모델을 설명한다.    

# Problems

SetFit은 아래의 2가지 문제를 해결한다. 

1. 기존 few-shot 모델은 프롬트(prompt)를 사용하는데, 사용자가 입력한 프롬트에 따라 결과값이 달라지는 문제점이 있다. 
2. 기존 few-shot 모델은 매우 큰 모델을 사용한다. 파라미터 개수가 10억개 이상에 달하는 모델을 실제 현업에서 사용하기에는 부담이 크다. 

# Architecture

<figure>
  <img src="/assets/setfit/arch.png" width="800" height="200">
</figure> 

SetFit은 어떻게 상대적으로 작은 모델을 사용하고 프롬트도 없으면서 좋은 성능을 낼 수 있었을까?   
그 학습 방식을 알아보자.    

SetFit은 Sentence Transformer 모델을 사용한다.   

<figure>
  <img src="/assets/setfit/sb.png" width="300" height="200">
</figure> 

Sentence Transformer는 Bert 모델 2개에 2개의 문장을 인풋으로 넣고 triplet loss를 계산해 두 Bert 모델의 웨이트를 업데이트 한다. 두 모델의 웨이트가 동시에 업데이트 되기 때문에 쌍둥이 모델라는 뜻의 Siamese Network라고 불리기도 한다. 

Triplet loss를 구하기 위해서는 anchor, positive, negative 데이터가 필요하다. Triplet loss는 데이터간의 상대적인 거리를 학습하는데 anchor와 positive 데이터의 거리는 최소화되고 anchor와 negative의 거리는 최대가 되도록 학습하기 위한 loss이다. 

Sentence Bert는 문장을 입력했을 때 그 문장을 잘 표현하고 있는 고정된 벡터 값을 반환한다. 이 문장 벡터가 SetFit의 첫번 째 시작 지점이다.    

## 1. Finetuning Sentence Transformer
우선 SetFit에 데이터가 입력된다. 예를 들어 문장을 긍정, 부정, 중립으로 판별하는 문제를 풀고자할 때 긍정 문장 8개, 부정 8개, 중립 8개가 학습 데이터로 입력된다.    

그러면 이 데이터들로 contrastive learning을 진행한다.    
Contrative learning이란 데이터간 거리를 학습하는 방식으로 같은 레이블의 데이터 간 거리는 최소화되고 다른 레이블 간 데이터의 거리는 최대화되도록 학습한다.    

이 학습 방식의 장점은 데이터 뻥튀기가 가능하다는 것이다.    
예를 들면, 첫번째 데이터가 `("이 집 음식 맛있어요", 긍정)`라고 했을 때, 
`("이 집 음식 맛있어요", "이 집 분위기 좋아요", 1)` 와 같이 유사도가 1인 데이터를 $R$개 생성할 수 있다.    
반대로 `("이 집 음식 맛있어요", "생각보다 별로에요, 비추", 0)`와 같이 유사도가 0인 데이터도 $R$개 생성할 수 있다. 따라서 하나의 레이블 당 생성할 수 있는 데이터의 개수는 2 x $R$ 개 이며, 총 (2 x $R$ x 레이블 개수)의 데이터가 생성될 수 있다. 

Contrastive learning 방식으로 Sentence Transformer를 fine-tuning 시키는데, 적은 데이터가 입력되도 다양한 데이터 쌍을 생성해 거리를 학습하기 때문에 학습 데이터에 맞춤화된 문장 벡터를 학습시킬 수 있다. 

## 2. Classification head
Sentence Transformer를 fine-tuning 한 후 학습 데이터의 문장 벡터를 생성할 수 있다. 이 문장 벡터는 Fully conntected neural network 에 입력되어 레이블을 예측하도록 계산되며 cross entropy loss를 이용해 웨이트 값이 업데이트 된다. (일반적인 1 hidden layer logistic regression 테스트라고 볼 수 있다.)

# Results

SetFit은 Sentence Transformer pretrained 모델을 베이스 모델로 사용한다.    
가장 큰 모델은 RoBERTa의 파라미터 수가 3억개 가량이기 때문에 10억개가 넘어가는 GPT 계열보다 현업에서 사용하기 더 적합한 모델이라고 할 수 있다.   

<figure>
  <img src="/assets/setfit/sb-pretrained.png" width="500" height="200">
</figure> 

[Sentence Transformer pretrained 모델 링크](https://www.sbert.net/docs/pretrained_models.html)

아래 표는 기존의 fine-tuning 방법으로 학습한 모델과 few-shot 방식으로 학습한 모델을 비교한 표이다. 데이터 셋은 모두 분류 테스크 문제이며 $|N|$은 각 클래스(레이블) 당 학습 데이터의 개수를 나타낸다.    

SetFit과 T-Few(현재 few-shot SOTA 모델) 모델을 비교해보면 SetFit 모델이 30배 적은 파라미터 수를 갖지만 성능 차이가 크지 않다는 점에 주목할 만하다. 

또한 한 클래스 당 64개 정도의 데이터를 학습 데이터로 학습했을 때, 모든 데이터를 학습시킨 기존의 fintune 모델에 비해 성능이 많이 떨어지지 않는 점도 주목할 만하다. 

<figure>
  <img src="/assets/setfit/result.png" width="500" height="400">
</figure> 

실제로 SetFit 모델로 내부 데이터를 테스트해본 결과 놀랍도록 높은 예측력을 보인다.   

[SetFit 모델 사용해보기 링크](https://github.com/huggingface/setfit)    

Sentence Transformer는 문장의 상대적 거리를 학습하기 때문에 문장 분류 태스크에 강점을 보이기 때문이다. 학습 데이터 셋 구성에 좀 더 신경쓴다면 few-shot 모델이 robust하지 않다는 기존의 통념을 깰 수 있는 모델이라고 할 수 있다. 

# Reference
- [Efficient Few-Shot Learning Without Prompts](https://arxiv.org/pdf/2209.11055.pdf)
- [Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks](https://arxiv.org/pdf/1908.10084.pdf)


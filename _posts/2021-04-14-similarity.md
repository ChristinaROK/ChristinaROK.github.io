---
layout: article
title: Math-distance, similarity
aside:
 toc: true
tags: math
disqus: true
---

# Basic Statistics words
### random variable (확률 변수)
> measurable function defined on a probability space that maps from the sample space to the real numbers

코딩에 익숙한 사람이라면 variable을 듣는 순간 데이터를 담는 그릇을 떠올릴 것이다.   
하지만 통계에서 말하는 확률 변수는 **함수**다.    

반려동물을 입양하기 위해 유기동물 센터에 갔다. 그곳에 강아지가 5마리, 고양이가 3마리 살고있다고 해보자. 내가 강아지를 입양할 경우의 수는 5, 고양이를 입양할 경우의 수는 3이다. 강아지를 입양할 **확률은 해당 사건이 일어날 경우의 수를 전체 사건의 경우의 수로 나누면 된다.** 즉, 강아지를 입양할 확률은 5/(5+3)로 0.625이며, 고양이를 입양할 확률은 3/(5+3)으로 0.375이다. 

| 변수 (x) | 강아지 (x=강아지) | 고양이 (x=고양이) |
|:--- | :---: |:---: | 
| 확률 (P(x)) | 0.625 | 0.375 | 

확률 변수 P(x)는 변수인 x의 값이 강아지일때는 확률 값이 0.635가 되는 함수이다. 즉, 변수인 x에 따라서 확률 값 y가 나오는 함수이다. 흔한 예시로 나오는 주사위의 확률 변수는 P(x)=1/6 이다. 즉, 어떤 1과 6사이의 x값이 모두 같은 확률 0.166을 갖는 확률 변수 함수인 것이다. 


### mean

### variance

다른 데이터 사이의 각 데이터들이 평균에서 얼마나 떨어져있나를 비교하기 위한 지표

### std (standard deviation) 

# Estimator
### MSE (Mean Squared Error)
### RMSE (Root MSE)
### MSD (Mean Squared Deviation)

# Distance

### Norm

* L1 Norm (Manhattan Norm)

* L2 Norm (Euclidean Norm)

### Euclidean Distance
* 벡터의 크기(magnitude)가 영향을 미침

### Manhattan Distance 

### Cosine Distance

### vector normalization
벡터는 방향성(direction)과 크기(magnitude)를 갖고 있다.    
벡터의 방향성을 유지하며서 크기를 -1과 1사이로 맞추는 작업을 벡터 정규화라고 한다.   
벡터간의 유사도를 계산하고 싶은데 벡터의 크기가 너무 클 때 벡터 정규화로 크기를 -1과 1사이로 맞춘 후 코사인 유사도를 구한다. 

# Clustering

### K-means

* inertia
* centrioid

* 문제
	* word embedding이 sparse vector, high-dimension 특징을 갖기 때문에 euclidean distance가 적합하지 않음. 

k-means + cosine-distance
[참고](https://lovit.github.io/nlp/machine%20learning/2018/10/16/spherical_kmeans/)

# Reference
[Vector Norm](http://taewan.kim/post/norm/)
[Vector nomalization](https://stackoverflow.com/questions/10002918/what-is-the-need-for-normalizing-a-vector/45410687)
[Why normalize vector in cosine similarity](https://stats.stackexchange.com/questions/292596/is-feature-normalisation-needed-prior-to-computing-cosine-distance)   

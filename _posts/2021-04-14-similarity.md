---
layout: article
title: STATISTICS-distance, similarity
aside:
 toc: true
tags: statistics
disqus: true
---

# Basic Statistics words
### random variable

### mean

### variance

### std (standara deviation) 

# Estimator
### MSE (Mean Squared Error)
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

* 문제
	* word embedding이 sparse vector, high-dimension 특징을 갖기 때문에 euclidean distance가 적합하지 않음. 

k-means + cosine-distance
[참고](https://lovit.github.io/nlp/machine%20learning/2018/10/16/spherical_kmeans/)

# Reference
[Vector Norm](http://taewan.kim/post/norm/)
[Vector nomalization](https://stackoverflow.com/questions/10002918/what-is-the-need-for-normalizing-a-vector/45410687)
[Why normalize vector in cosine similarity](https://stats.stackexchange.com/questions/292596/is-feature-normalisation-needed-prior-to-computing-cosine-distance)   

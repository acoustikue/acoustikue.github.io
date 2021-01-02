---
layout: post
title:  "[Intro to AI] MLP 로 Linear Regression 구현하기"
date:   2020-11-24 13:00:00
categories: Python
permalink: /archivers/mlp-lr
nocomments: false
use_math: true 
---

# MLP 로 Linear Regression 구현하기 (작성중)

Python 3.8, Jupyter 환경 이용하였습니다.

## 데이터 생성


**1. First generate 10,000 ($ 100 \times 100 $) 2-dimension data points based on $f(x, y)$ where $-1\leq x \leq 1$ and $-1 \leq y \leq 1$.**  
**2. Add standard Gaussian noise to $f(x, y)$, ie. $\hat{f}(x, y)=f(x,y)+0.1N(0. 1)$ (1)**  
**The input instance $(x, y)$ and the target value form a dataset, namely $\left\{[x, y], \hat{f}(x, y)\right\}$**



## 필요 모듈 설치

```bash
(pytorch) path> conda install jupyter pandas matplotlib  
(pytorch) path> conda install -c conda-forge jupyter_contrib_nbextensions   
(pytorch) path> conda install scikit-learn  
(pytorch) path> jupyter notebook  
```

## 가상 환경 나가기

```bash
(pytorch) path> conda deactivate 
```



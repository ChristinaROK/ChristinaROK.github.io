---
layout: article
title: ETC - 백엔드 및 프론트엔드 프레임워크 정리 (웹 & 앱)
aside:
 toc: true
tags: etc
---

## (Web) Framework

> Application을 개발하고 배포할 때 framework를 사용한다. Framework는 개발과 배포에 필요한 프로그램, 컴퍼일러, 코드 라이브러리, 각종 유틸, 또는 API등을 제공한다. 

* (Additional) Framework의 특징 4가지 
  * inversion of control
    * 프로그램의 제어권은 프레임워크에게 있음. 따라서 사용자(caller of framework)는 프레임워크가 시키는대로 (control) 코딩해야함
  * default behavior
    * 프레임워크의 초깃값을 사용함 (OOP에서 base (abstract) class 생각하면 됨)
  * extensibility
    * 사용자가 프레임워크에 코드를 추가해 기능을 얹을 수 있음
  * non-modifiable framework code
    * 프레임워크의 기본 기능은 수정이 불가능함. 사용자는 extension을 추가해 프레임워크에 기능을 추가할 수는 있지만 프레임워크의 기본 기능을 변경할 수는 없음. 

---

### FrontEnd (FE)

* Language
  * HTML
  * CSS
  * JavaScript (JS)
* Framework
  * React
  * Angular
  * VueJS

### BackEnd (BE)

* Language
  * Python
  * Ruby
  * PHP
* Framework
  * Django
  * Spring Boot
  * NodeJS
    * Express: fast and minimalist 

### Combination

* [**JavaScript Combination**] (FE) React +  (BE) NodeJS/Express
* (FE) React + (BE) Django

---

## (App) Framework/Language

### Native

각 운영체제에 최적화된 프레임워크. 스마트폰의 카메라, 마이크 등의 입출력 디바이스에 접근할 수 있음. 하지만 운영체제별로 개발해야하기 때문에 개발 비용과 시간이 증가함.

* Android
  * Kotlin
  * Java
  * C++
* IOS
  * Swift
  * Object C

### Hybrid

주로 웹 프론트엔드 기술을 활용하며 웹과 앱을 동시에 지원하기 위해 사용됨. 

### Cross Platform

네이티브와 하이브리드의 중간 형태. 하나의 소스 코드로 여러 플랫폼을 지원하지만 아웃풋이 네이티브이기 때문에 사용성이 하이브리드 대비 뛰어남.

- Flutter
- React Native
- Ionic

## Reference

* [FE-BE-framework-pairing](https://blog.crowdbotics.com/most-compatible-frontend-backend-framework-pairings/)   
* [Mobile Stacks](https://lucidmaj7.tistory.com/256)


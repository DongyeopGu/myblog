---
title: "Django FBV vs CBV"
weight: 25
chapter: true
pre: "<b>10. </b>"
tags: ["django","FBV", "CBV"]
---

## Django FBV vs CBV

> FBV CBV 차이

- FBV(Function Based View)
  - 장점
    - 구현이 간단함
    - 읽기 편함
    - 직관적인 코드
    - 데코레이터 사용이 간단함
  - 단점
    - 확장 / 재사용이 힘듬
- CBV(Class Based View)
  - 장점
    - 확장에 용의(재사용)
    - 다중 상속 Mixin등 사용 가능
    - HTTP Method가 클래스 안에서 나누어 처리
    - 강력한 Generic Class View 가 있음
  - 단점
    - 읽기 어려움(코드 흐름이 암시적)
    - 상속되고 믹스되면서 코드 이해를 위해 찾아다녀야함
    - 데코레이터 사용시 별로도 import 하거나 Method 생성

----




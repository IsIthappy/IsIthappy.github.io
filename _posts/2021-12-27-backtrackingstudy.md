---
title: "Backtraking"
excerpt: "두리뭉실 백트래킹이란 무엇인가?"

categories:
    - Algorithm
tags:
    - [Note, Algorithm]
last_modified_at: 2021-12-27 
---


# Backtracking 알고리즘이란?

> 완전 탐색의 한 종류이다.

**가능한 모든 방법을 탐색한다.** 가 기본 아이디어임. 따라서 경우의 수가 몇가지나 있는지 우선적으로 고려를 하고 사용해야하는 알고리즘임.


> dfs 알고리즘과 매우 유사하다. 사실상 단짝친구 느낌?


1. 가지치기
2. 이전 상태로 되돌리기(함수 호출하면서 바뀐 상태를 호출이 끝난 뒤에 돌려놓는거임)


위 두개가 backtracking 알고리즘의 핵심이라고 생각한다.


1. dfs를 이용해서 backtracking의 개념을 이해해보자면 한 우물을 파기 시작하는데? 잘못된 우물이다? 그럼 종료 조건까지 내려가지 않고 중간에서 가지를 쳐내는 것이다.


2. 가지를 쳐냈으면? 다시 이전 상태로 돌아가서 다른 우물을 파기 시작하는 것이다.


> 따라서 이 가지치기를 얼마나 잘 하느냐에 따라서 백트래킹의 성능이 결정된다.


# 라이 블로그에서 추천받은 문제들

1182번: 부분집합의 합

1759번: 암호 만들기

1987번: 알파벳

9663번: N-Queen

2580번: 스도쿠

10597번: 순열장난

2661번: 좋은수열 (★)

17136번: 색종이 붙이기 (★)

1189번: 컴백홈

1405번: 미친 로봇 (★)

1339번: 단어 수학 (★)
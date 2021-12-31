---
title: "플로이드 와샬 알고리즘"
excerpt: "모든 정점 간 최단 거리 구하기"

categories:
    - Algorithm
tags:
    - [Note, Algorithm]
last_modified_at: 2021-12-31 
---

# 플로이드 와샬 알고리즘(Floyd–Warshall algorithm) 노트 목차
1. 플로이드 와샬 알고리즘이란 무엇인가?
2. 문제를 통해 플로이드 와샬 알고리즘 공부하기
3. 후기


# 1. 플로이드 와샬 알고리즘이란?

> 최단 경로를 구하는 알고리즘 중 한가지이다.

(벨만포드, 플로이드 와샬, 다익스트라)

#### 핵심적인 포인트

**1) 모든 정점 사이의 최단 거리를 구하는 알고리즘이다.**

다익스트라 알고리즘의 경우 하나의 시작 정점으로부터 모든 정점 사이의 최단 거리를 구하는 알고리즘이었지만 이 플로이드 와샬의 경우 완전 탐색을 기반으로 동작하여 모든 정점 사이의 최단 거리를 구하는 알고리즘이다.


**2) 음의 가중치가 있어도 사용할 수 있다.**

이 부분에 대해서도 다익스트라처럼 Greedy하게 동작하지 않고 완전 탐색 기반의 알고리즘이라 음의 가중치가 있어도 최단 거리를 구할 수 있다.

---

# 2. 문제를 통해 플로이드 와샬 알고리즘 공부하기

[https://www.acmicpc.net/problem/11404](https://www.acmicpc.net/problem/11404)

문제를 통하여 알아보도록 하자.

1) 중간에 거쳐갈 노드 선택 V

-> 이 중간에 거쳐가는 노드 선택으로 인하여 완전 탐색이 가능해진 것이다.

2) 선택된 거쳐가는 노드에 따라 A -> V ->  B 최단거리 갱신하기  V^2


>따라서 O(V^3)의 시간복잡도가 된다.

>모든 정점 간의 거리를 표현해야해서 인접행렬의 효율이 좋다. 따라서 시간 복잡도가 저렇게 나오는 것.


```c
#define INF 100000000
#define MIN(a, b) (((a) < (b)) ? (a) : (b)) // 삼항 연산자, true 면 왼쪽, false면 오른쪽 

#include <stdio.h>

int dist[101][101];
int n;

void init();
void floyd();

int main()
{
	init();
	floyd();
	for (int i = 1; i <= n; i++)
	{
		for (int j = 1; j <= n; j++)
		{
			if (dist[i][j] == INF)
				printf("0 ");
			else
				printf("%d ", dist[i][j]);
		}
		printf("\n");
	}
	return 0;
}

void floyd()
{
	for (int k = 1; k <= n; k++) // 중간 정점 선택
	{
		for (int i = 1; i <= n; i++) // 선택된 중간 정점을 거쳐갈 것인지, 그냥 바로 갈 것인지 경로별로 갱신
		{
			for (int j = 1; j <= n; j++)
				dist[i][j] = MIN(dist[i][j], dist[i][k] + dist[k][j]);
		}
	}
	return;
}

void init()
{
	int m;
	scanf("%d\n%d", &n, &m);
	for (int i = 1; i <= n; i++) // dist 배열 초기화 과정
	{
		for (int j = 1; j <= n; j++)
		{
			if (i != j)
				dist[i][j] = INF;
		}
	}
	int a, b, c;
	for (int i = 0; i < m; i++)
	{
		scanf("%d %d %d", &a, &b, &c);
		if (dist[a][b] == INF)
			dist[a][b] = c;
		else // 중복 노선 쳐내기
		{
			if (dist[a][b] > c)
				dist[a][b] = c;
		}
	}
	return;
}

```


# 4. 후기

생각보다 구현은 엄청 쉬운 알고리즘이다. 모든 정점간의 거리를 구하는 알고리즘이다 보니, 정점의 수가 적을 때 사용가능하다.
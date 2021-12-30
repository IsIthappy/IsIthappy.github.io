---
title: "다익스트라 알고리즘"
excerpt: "우선순위 큐를 이용한 다익스트라 알고리즘"

categories:
    - Algorithm
tags:
    - [Note, Algorithm]
last_modified_at: 2021-12-30 
---

# 다익스트라 알고리즘 노트 목차
1. 다익스트라 알고리즘이란 무엇인가?
2. 다익스트라 알고리즘 구현 두가지 방법
3. 문제를 통해 다익스트라 알고리즘 공부하기
4. 후기


# 1. 다익스트라 알고리즘이란?

> 최단 경로를 구하는 알고리즘 중 한가지이다.

(벨만포드, 플로이드 와샬, 다익스트라)

#### 핵심적인 포인트

**1) 시작점으로부터 다른 모든 정점까지의 최단 거리를 구하는 알고리즘이다.**

알고리즘이 끝나고 나면 dist 배열에는 시작노드로부터의 최소 거리가 저장되어있다.

**2) 음의 가중치가 있으면 사용할 수 없다.**

이 부분에 대해서는 greedy, DP 적인 움직임을 보이는 다익스트라 알고리즘에서 최소값을 보장할 수 없게 되기 때문임. (우선 순위 큐를 이용하여 구현할 경우엔 싸이클이 형성되어 음의 값으로 발산될 수도 있음.)

-> 물론 우선 순위큐를 계속 업데이트하여 이를 회피할 수도 있다고 하는데 이러면 지수시간이 걸린다고 함.

https://www.acmicpc.net/board/view/19865

**3) 매번 가장 비용이 작은 정점을 선택하는 그리디 기반의 알고리즘이다.**


---

# 2. 다익스트라 알고리즘을 구현하는 두가지 방법

[https://www.acmicpc.net/problem/1753](https://www.acmicpc.net/problem/1753)

문제를 통하여 알아보도록 하자.

1) 선형 탐색을 통하여 다음 노드를 취하기- O(V^2)

2) 우선 순위 큐를 활용하여 다음 노드를 취하기 - O(ElogV) OR O(ElogE)


>위 문제의 경우에는 우선 순위 큐를 활용하지 않으면 시간 초과에 걸린다.

>또한, 그래프를 인접행렬(2차원배열)로 표현하면 메모리 초과에 걸린다.


### 1) 선형 탐색을 통하여 다음 노드를 취하기

다익스트라 알고리즘의 기본형이다. 시작 노드로부터의 거리를 갱신할 dist 배열과, 방문 체크를 위한 visit 배열 두개를 준비한다.

1) 최단 거리 테이블을 INF 값으로 초기화한다.

2) 시작 노드를 설정하고 거리 테이블을 0으로 갱신한다.

3) 현재 노드로부터 갈 수 있는 노드들의 dist 값을 갱신한다.

(기존에 갱신되어 있는 값보다 작을 때, 갱신한다.)

4) 방문하지 않은 node 중에서 최소값인 node를 방문한다.(현재노드 변경)

(이때 이 최소값인 node를 찾기 위하여 dist 배열에서 선형탐색이 이루어진다.)

5) 3,4 과정을 방문가능한 node를 다 방문할 때까지 반복한다.

(visit 배열과 dist의 INF 값을 통하여 경로가 없는 경우도 걸러낼 수 있다.)


인접 행렬로 표현해서 시간 복잡도를 계산하면

>O(모든 vertex 방문 V + (dist 갱신 V + 방문하지 않은 곳에서 최소가 되는 V 선택)) = O(V^2)


(dist갱신의 경우 인접 list로 표현한다면 V가 아니라 해당 vertex의 edge수로 줄일 수 있다.)


# 2. 우선 순위 큐를 활용해 다음 노드 선택하기

> 다음 노드를 선택하는 과정에서 매번 dist 배열을 뒤지지 않고 그것을 우선순위 큐를 통하여 해결하는 방법이다.

이 경우에는 dist 배열 하나만 있으면 된다. 방문 체크를 하지 않고 진행해도 무방하다.

(우선 순위대로 나오고, dist 크기 비교를 통해 걸러내는 과정이 들어가기 때문이다 -- 중복 방문을 막을 수 있음.)

> 큐에 dist 크기 값이 같이 들어가기 때문에 가능한 것임.


**시간 복잡도**

우선순위 큐에 간선을 넣고 빼는 과정 - logE

우선순위 큐에서 꺼낸 현재 노드에 연결된 간선 모두 확인 - 간선의 개수(E) 만큼 확인

O(ElogE) 이지만 중복 간선이 없는 경우 E = V*(V-1) -- undirected graph의 경우.

따라서 O(ElogV) 로 표현될 수도 있다.


```c

#define INF 100000000

#include <stdio.h>
#include <stdlib.h>

// 그래프 구현을 위한 구조체
typedef struct node {
	int v;
	int cost; // 간선의 비용 정보
	struct node* next;
}node;

//최소힙 구현을 위한 구조체
typedef struct{
	int d;
	int vertex;
}pos;

node* list[20001]; // 정점의 최대개수는 20,000개
int mv, sv; // 최대 정점의 개수, 시작 vertex ,,,, 간선의 수 같은 경우엔 init에만 필요하기 때문에 굳이 전역이 아니어도 된다.

pos heap[300001]; // 우선 순위 큐를 위한 최소힙
int top = 0; // heap은 0번 index를 비워둔다!

int dist[20001]; // 거리 갱신을 위한 배열.


//그래프 표현 부분
void init(); // 입력 받아 그래프 표현하는 부분(인접리스트 기반)
void insert_edge(int, int, int);
void free_mem();


//heap 표현 부분(우선 순위 큐 구현을 위해 최소힙 사용.)
void insert_heap(int, int);
void pop_heap();
void swap(int, int);

void dijk();


int main()
{
	init();
	dijk();
	for (int i = 1; i <= mv; i++)
	{
		if (dist[i] == INF)
			printf("INF\n");
		else
			printf("%d\n", dist[i]);
	}
	free_mem();
	return 0;
}

void dijk()
{
	for (int i = 1; i < 20001; i++)
		dist[i] = INF;
	dist[sv] = 0;
	insert_heap(0,sv);
	while (top > 0) // heap이 비어있지 않다면
	{
		int cur_v = heap[1].vertex, cur_dist = heap[1].d;
		pop_heap();
		if (cur_dist > dist[cur_v]) // dist 크기 비교를 통해서 불필요한 방문을 쳐내는 것이다.
			continue;
		if (list[cur_v] != NULL) // 현재 노드에서 갈 수 있는 노드들의 dist 갱신
		{
			node* cur = list[cur_v]; // 현재 노드에 연결된 노드
			while (cur != NULL)
			{
				if (dist[cur->v] > dist[cur_v] + cur->cost)
				{
					dist[cur->v] = dist[cur_v] + cur->cost;
					insert_heap(dist[cur->v], cur->v);
				}
				cur = cur->next;
			}
		}
	}
	return;
}

// 여기서부턴 우선 순위 큐를 위한 최소힙 구현 부분

void pop_heap()
{
	if (top != 0) // heap 이 비어있지 않다면
	{
		heap[1] = heap[top--]; // heap의 맨 마지막 원소를 1번으로 땡겨와야지(재배치를 위해서)
		int parent = 1, child = 2;
		while (child <= top)
		{
			if (child < top && heap[child].d > heap[child + 1].d) // child < top이면 다음 child가 있다는 소리고, 따라서 두 차일드 중 더 작은애를 찾아야지
				child++; // 더 작은 child로 변경
			if (heap[child].d >= heap[parent].d) // 부모가 더 작으니까 최소힙 우선 순위 만족한 상태.
				break;
			swap(parent, child);
			parent = child;
			child *= 2;
		}
	}
	return;
}

void insert_heap(int d, int vertex)
{
	heap[++top].d = d;
	heap[top].vertex = vertex;
	if (top != 0) // 최소힙 맨 마지막에 추가하고, 재배치 과정. O(log n)
	{
		int child = top;
		int parent = child / 2;
		while (parent > 0)
		{
			if (heap[child].d < heap[parent].d)
			{
				swap(parent, child);
				child = parent;
				parent /= 2;
			}
			else
				break;
		}
	}
	return;
}

void swap(int a, int b)
{
	int temp_d = heap[a].d, temp_v = heap[a].vertex;
	heap[a].d = heap[b].d;
	heap[a].vertex = heap[b].vertex;
	heap[b].d = temp_d;
	heap[b].vertex = temp_v;
	return;
}


// 여기서부턴 그래프 표현, 메모리 해제 부분

void init()
{
	int me; // 최대 간선 수
	scanf("%d %d", &mv, &me);
	scanf("%d", &sv);
	int sv, ev, c; // 시작, 목적지 vertex, cost
	for (int i = 0; i < me; i++)
	{
		scanf("%d %d %d", &sv, &ev, &c);
		insert_edge(sv, ev, c);
	}
}

void insert_edge(int sv, int ev, int c)
{
	node* cur = (node*)malloc(sizeof(node));
	cur->v = ev;
	cur->cost = c;
	if (list[sv] == NULL)
	{
		cur->next = NULL;
		list[sv] = cur;
	}
	else
	{
		cur->next = list[sv];
		list[sv] = cur;
	}
	return;
}

void free_mem()
{
	for (int i = 1; i < 20001; i++)
	{
		if (list[i] != NULL)
		{
			node* cur = list[i];
			node* pre = NULL;
			while (cur != NULL)
			{
				pre = cur;
				cur = cur->next;
				free(pre);
			}
		}
	}
	return;
}

```


# 4. 후기

다익스트라 알고리즘의 시간복잡도가 이게 맞는지는 잘 모르겠다.

나중에 보는 나야 다시 한번 보고 틀린 부분 좀 고쳐봐.

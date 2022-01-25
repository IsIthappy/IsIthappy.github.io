---
title: "벨만포드 알고리즘"
excerpt: "음수 간선이 있을 때 다익스트라 대신 사용하는 알고리즘"

categories:
    - Algorithm
tags:
    - [Note, Algorithm]
last_modified_at: 2022-1-25 
---

# 벨만포드 알고리즘 노트 목차
1. 벨만포드 알고리즘이란 무엇인가?
2. 벨만포드 알고리즘 사용 예시 두가지 문제
3. 후기


# 1. 벨만포드 알고리즘이란?

> 최단 경로를 구하는 알고리즘 중 한가지이다.

다익스트라의 경우 우선순위 큐를 활용할 때 (VlogE) 였지만 벨만 포드의 경우 O(VE) 의 시간복잡도를 가진다.

즉, 음의 간선이 없을 때 다익스트라를 사용하는 이유는 벨만 포드의 경우 모든 간선을 다 훑어보며 진행하기 때문에 다익스트라보다 비효율적이기 때문에 그런 것이다.

(벨만포드, 플로이드 와샬, 다익스트라)

#### 핵심적인 포인트

**1) 시작점으로부터 다른 모든 정점까지의 최단 거리를 구하는 알고리즘이다.**

알고리즘이 끝나고 나면 dist 배열에는 시작노드로부터의 최소 거리가 저장되어있다.

이게 가능한 이유는 다익스트라의 경우에는 시작 정점으로부터 방문 체크를 하며 진행하면서 최소 비용인 정점을 선택하면서 진행하기 때문이었고

벨만 포드에서는 greedy하게 동작하지는 않지만 **이미 방문한 정점으로부터의 edge를 통해서만 최소 비용 갱신**이 이루어지기 때문이다.

>dist 배열 값이 INF인지 아닌지를 통해서 이를 가능하게 한다.


**2) 음의 가중치가 있을 때 다익스트라 알고리즘 대신 사용한다.**

다익스트라의 경우 greedy하게 동작하는데 cost가 음수인 간선이 있다면 매번 최소 비용의 node를 방문하며 진행하는 이 선택이 최선의 선택이 될 수 없는 경우가 생기게 된다.

>정점 세개를 그려놓고 1 -> 2, 1 -> 3 -> 2 두개의 경로가 있다고 할 때, 3 -> 2 를 음수 간선으로 두고 생각해봐라

즉, greedy하게 판단할 수 없으므로 **모든 간선을 다 탐색하는 벨만 포드 알고리즘**을 사용해야 하는 것이다.

**why?** 다익스트라의 경우 방문하지 않은 정점 중, 방문할 수 있는 최소 비용의 정점을 방문하며 그 정점에서 갈 수 있는 도로들만 갱신하는 형식이었는데 벨만 포드 알고리즘은, 모오오든 간선들을 다 훑으면서 **방문한 적이 있는 node에서 뻗어져가는 간선들만 dist 배열 갱신에 사용하기 때문이다.**

>방문한 적이 있는 node에서의 간선만 갱신에 사용하는 이유는 출발 node로 부터의 경로를 구하기 위해서임.



**3)음의 사이클이 존재하는 경우 정확한 최소 비용을 구할 수 없다.**

벨만 포드 알고리즘은 음의 사이클이 존재하는 경우, **정확한 최소 비용을 구하지 못하고 음의 사이클이 존재한다는 것**만 판단할 수 있게 해주는 알고리즘이다.

이에 대한 설명은 https://yabmoons.tistory.com/365 여기 블로그를 천천히 읽어보며 손으로 정리해보니 이해할 수 있었다.

V - 1번 반복해서 진행하는 이유는 1 -> 2 -> 3 -> 4 와 같은 worst case 때문에 이렇게 진행하는 것이며, 

음의 사이클이 없다면 **V - 1 번 반복되기 전에 더 이상 갱신이 일어나지 않을 수 있다.** (이런 예시 역시 이 블로그 아래에 있다.)

하지만 음의 사이클이 존재한다면??? 무한히 최소값으로 갱신이 될 수 있기 때문에 V번째 반복했을 때 역시 갱신이 일어난다. 때문에 V - 1 번 반복 후, 음의 사이클 존재 여부를 판단하기 위해 V번째 edge들을 훑어보며 dist 배열을 갱신하는 과정을 거치는 것이다.


---

# 2. 벨만포드 알고리즘 사용 예시 두가지 문제


### 1) 타임머신 (벨만포드 알고리즘 적용 표준 문제)

[https://www.acmicpc.net/problem/11657](https://www.acmicpc.net/problem/11657)

벨만포드 알고리즘을 적용해서 풀 수 있는 가장 기본적인 형태의 문제이다.

딱 보면 시작 정점으로부터 다른 정점들간의 최단 거리를 구해야 하는데?

>음의 간선이 존재한다.

이렇게 되면 다익스트라 알고리즘을 사용했을 때 매 선택이 최적의 해라는 것을 보장할 수 없기 때문에, 모든 간선을 탐색하는 벨만포드 알고리즘을 사용하는 것이다.

자세한 알고리즘의 이해는 빠르게 코드의 주석을 통해서 다시 복기하자.


**하지만 이 문제의 진짜 숨겨진 핵심은, INF 값은 500 * 10,000 이 되지만 음의 사이클이 존재할 경우 underflow가 발생한다는 점이다...**

정점의 개수가 500개, 간선의 개수가 6000개, 모든 간선의 가중치가 -10000 일 때,

V번 반복하고(음의 사이클 존재 여부 판단까지) **매번 모든 간선이 원형으로 이루어져 있어 갱신이 이루어진다면**,

-10000 * 500 * 6000 값 까지 나올 수 있으므로 충분히 underflow가 일어나기 때문에 dist 배열을 long long으로 잡아줘야 하는 문제였다.

>INF 값은 어떻게 해서 나오는지 바로 알았지만 벨만 포드에서 최소 값은 어디까지 내려가는지 이해하는데 조금 걸렸다.


```c
#include <stdio.h>
#include <stdlib.h>

#define INF 50000000 // 정점의 수 * 최대 비용 보다만 크면 된다.(INF 값이 어떻게 정해지는지, 원형으로 이루어진 case를 생각해보면 알 수 있다.)

typedef struct node {
	int v;
	int c;
	struct node* next;
}node;

node* list[501];
int n;

long long dist[501]; // underflow으로 인해 자료형 설정

void init();
void insert_node(int, int, int);

void bellman();
void free_mem();

int main()
{
	init();
	bellman();
	free_mem();
	return 0;
}

void bellman()
{
	dist[1] = 0;
	for (int i = 2; i <= n; i++)
		dist[i] = INF;
	for (int k = 1;k < n; k++) // n-1 번 반복을 통해 worst case까지 최단 경로 찾기
	{
		for (int i = 1; i <= n; i++) // 매 반복마다, 모든 edge들 체크하기
		{
			if (list[i] == NULL || dist[i] == INF) // dist[i] 가 INF면 갱신 아무것도 못하니까 건너 뛰어도 된다.
				continue;
			// 방문된 적이 있는 지점으로부터의 경로만 갱신해줘야 출발 지점으로부터의 경로를 뽑아낼 수 있음.
			
			node* cur = list[i];
			while (cur != NULL) // 방문된 적이 있는 node로부터의 edge을 통해서 갈 수 있는 정점들 dist 갱신
			{
				if (dist[cur->v] > dist[i] + cur->c)
					dist[cur->v] = dist[i] + cur->c;
				cur = cur->next;
			}
		}
	}
	// n번째 돌려서 음의 사이클 체크 ( V번째에서 dist 배열 갱신이 일어난다면, 음의 사이클 존재)
	for (int i = 1; i <= n; i++)
	{
		if (list[i] == NULL)
			continue;
		node* cur = list[i];
		while (cur != NULL) 
		{
			if (dist[i] != INF && dist[cur->v] > dist[i] + cur->c) // 다시 갱신이 일어나면 음의 사이클 있다는거임
			{
				printf("-1");
				return;
			}
			cur = cur->next;
		}
	}
	// 위 for문을 무사히 통과하면 음의 사이클 없다는 거임
	for (int i = 2; i <= n; i++)
	{
		if (dist[i] == INF)
			printf("-1\n");
		else
			printf("%lld\n", dist[i]);
	}
	return;
}

void free_mem()
{
	for (int i = 1; i <= n; i++)
	{
		if (list[i] == NULL)
			continue;
		node* cur = list[i];
		node* pre;
		while (cur != NULL)
		{
			pre = cur;
			cur = cur->next;
			free(pre);
		}
	}
	return;
}

void init()
{
	int m;
	scanf("%d %d", &n, &m);
	int a, b, cost;
	for (int i = 0; i < m; i++)
	{
		scanf("%d %d %d", &a, &b, &cost);
		insert_node(a, b, cost);
	}
	return;
}

void insert_node(int a, int b, int cost)
{
	node* cur = (node*)malloc(sizeof(node));
	cur->v = b;
	cur->c = cost;
	if (list[a] == NULL)
	{
		cur->next = NULL;
		list[a] = cur;
	}
	else
	{
		cur->next = list[a];
		list[a] = cur;
	}
	return;
}

```

# 2. 웜홀 (조금 생각해야 했던 문제)


[https://www.acmicpc.net/problem/1865](https://www.acmicpc.net/problem/1865)

이 문제의 경우! 출발 정점이 정해져 있지 않다...

그냥 음의 사이클의 존재 여부만 묻고 있다.(사실 이 문제를 처음 봤을 때에는 이게 음의 사이클 존재 여부를 묻는 건지도 몰랐었다.)

그래서 약간 벙 쪘다. 음의 사이클의 존재여부만 묻는데, 이러면 모든 정점을 다 한번씩 출발 정점으로 삼아서 3중 for문 형식으로 풀어야 하나 싶었는데 이러면 무조건 시간초과가 날 것 같았다.

O(V*V*E) 말이 되냐 이게...

일단 벨만포드 알고리즘을 공부한 뒤, 질문 게시판을 커닝해보니...

dist배열 갱신 부분을 조금만 손 봐주면, 출발 정점을 지정하지 않고서도 풀 수 있다는 것을 알게되었다.

https://www.acmicpc.net/board/view/72995 벨만 포드 알고리즘을 이해한 상태에서 이 글을 읽으면 정신이 번쩍 든다. 어떻게 이런 생각을 하지?! 하고

>dist 배열을 INF로 초기화하지 않고 싹 다, 0으로 잡은 뒤 **모든 정점에서 동시에 출발하는 것**으로 푸는 방법

>dist 배열의 INF 값이, 방문한 적이 없는 node인 것이 아니라, **최소값이 INF인 방문한 적이 있는 node**라고 생각하고 푸는 방법

이렇게 두가지인데, 알고리즘 흘러가는 방향을 보면 둘이 사실상 동작하는 메카니즘은 똑같다. 생각하는 방법만 다를 뿐

이해하는 방법이 두가지이다.


```c

#include <stdio.h>
#include <stdlib.h>

typedef struct node {
	int v;
	int c;
	struct node* next;
}node;

int n;
node* list[501];

int dist[501];

void init();
void insert_node(int, int, int);

void bellman();
void free_mem(); // 메모리 할당 해제 & 초기화 함수

// test case를 여러 개 받으니까 연결 리스트를 사용하는 만큼 초기화를 잘해줘야함.

int main()
{
	int t;
	scanf("%d", &t);
	for (int k = 0; k < t; k++)
	{
		init();
		bellman();
		free_mem();
	}
	return;
}

void bellman() // O(V*E) -> vertex 만큼 반복 * 모든 엣지
{
	// 일단 n-1번 수행해야함. 그리고 나서 n번째에 음의 사이클이 있는지 확인하면 된다.
	// n - 1 번 수행 전에, dist 배열 갱신이 더 이상 일어나지 않을 수도 있긴 하다.
	// 위 타임 머신 문제랑 다른 점은, 모든 정점을 방문한 적이 있다고 생각하고 푸는 것이다.(모든 노드가 출발노드)
	for (int t = 1; t < n; t++) // 일단 n-1 번 수행
	{
		for (int i = 1; i <= n; i++)//(모든 엣지 돌면서 확인 해야지)
		{
			// 요 부분이 위에 출발 노드가 정해진 타임 머신 문제랑 다르지.
			if (list[i] == NULL)
				continue;
			node* cur = list[i];
			while (cur != NULL)
			{
				if (dist[cur->v] > dist[i] + cur->c) // 최소값으로 갱신해줘야지
					dist[cur->v] = dist[i] + cur->c;
				cur = cur->next;
			}
		}
	}
	for (int i = 1; i <= n; i++) // 한번 더 엣지들 돌려서 갱신 되는게 있으면 음의 사이클 존재하는거임.
	{
		if (list[i] == NULL)
			continue;
		node* cur = list[i];
		while (cur != NULL)
		{
			if (dist[cur->v] > dist[i] + cur->c)
			{
				printf("YES\n");
				return;
			}
			cur = cur->next;
		}
	}
	printf("NO\n");
	return;
}

void free_mem()
{
	for (int i = 1; i <= n; i++)
	{
		if (list[i] == NULL)
			continue;
		node* cur = list[i];
		node* pre;
		while (cur != NULL)
		{
			pre = cur;
			cur = cur->next;
			free(pre);
		}
		list[i] = NULL;
	}
	for (int i = 1; i <= n; i++) // 다음 test case를 위해 dist 배열 '0' 으로 초기화
		dist[i] = 0;
	return;
}

void init()
{
	int m, w; // 도로의 개수, 웜홀의 개수
	scanf("%d %d %d", &n, &m, &w);
	int s, e, cost; // 시작, 끝, 도로의cost
	for (int i = 0; i < m; i++)
	{
		scanf("%d %d %d", &s, &e, &cost); // 도로는 양방향임
		insert_node(s, e, cost);
		insert_node(e, s, cost);
	}
	for (int i = 0; i < w; i++)
	{
		scanf("%d %d %d", &s, &e, &cost);
		insert_node(s, e, -1 * cost);
	}
	return;
}

void insert_node(int a, int b, int cost) // a->b 로 가는 비용이 cost인 도로
{
	node* cur = (node*)malloc(sizeof(node));
	cur->v = b;
	cur->c = cost;
	if (list[a] == NULL)
	{
		cur->next = NULL;
		list[a] = cur;
	}
	else
	{
		cur->next = list[a];
		list[a] = cur;
	}
	return;
}

```


# 4. 후기

역시... 공부는 여러번 해야하는 것 같다.

벨만 포드를 공부하면서 다익스트라를 다시 보니까 점점 이해가 간다.

그리고 뭐다?! 역시 손으로 쓰면서 공부를 할때가 제일 잘된다.

>귀찮고 어려워도 손으로 정리하면서 이해하자!

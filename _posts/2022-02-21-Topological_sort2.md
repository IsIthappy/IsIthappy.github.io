---
title: "ACM Craft"
excerpt: "bfs를 활용한 위상정렬 && bfs 도중 cycle 존재 여부 확인"

categories:
    - Topological_sort
tags:
    - [PS, Topological_sort]
last_modified_at: 2022-02-21
---
# ACM Craft 문제 풀이
1. **문제의 이해**
2. 소스코드

백준 문제 링크 [https://www.acmicpc.net/problem/1005](https://www.acmicpc.net/problem/1005)


---

# 1. ACM Craft 문제의 이해

일단 문제를 봤을 때 건물 건설의 우선 순위를 따져가며 건설이 진행되어야 하므로 위상 정렬을 써야한다는 것은 쉽게 파악할 수 있었다. 그 외에 추가적으로 파악한 부분들은

1. 건설 순서는 건설이 가능하게 주어지므로 cycle이 형성되는 입력은 안들어옴.
2. input의 크기를 보아 시간의 합을 구하는 과정에서 overflow는 날리 없음.
3. 일단 위상 정렬을 통해 우선 순위를 구해야 건설이 가능해짐
4. 우선 순위를 통해서 target 건물을 짓기 위해 현재 방문하고자 하는 건물이 반드시 필요한지 check를 해야함.
5. 건물을 동시에 지을 수는 있지만 해당 node에 들어오는 선행 in_degree node 들이 모두 건설이 완료되어야 지을 수 있음.

그래서 이 조건들을 바탕으로 구성한 알고리즘이

1. dfs를 통해 우선 순위를 먼저 구한다.
2. 우선 순위를 따라서 건물들을 짓기 시작할건데 이때 불필요한 가지를 쳐내야한다.
3. 등수와, 해당 건물을 짓기 위해 선행 건물까지의 시간을 다 합친 값들 총 두개의 배열을 통해 적절히 가지를 쳐내면서 bfs를 진행한다.

-> 등수가 target node보다 높으면(우선순위가 낮으면) 방문하지 않음

-> 방문하고자 하는 node에 저장된 value가 현재 타고온 경로의 value보다 해당 경로는 더 이상 진행하지 않음(동시에 건설이 가능했던 경로므로)

이렇게 구상했는데... 런타임에러가 난다... 어디서 문제가 생겼는지는 모르겠지만 위 방식보다 효율적으로 푸는 방법이 존재하길래 그냥 그렇게 풀어버렸다.

현재 나는 우선순위를 구하기 위해서 dfs를 통한 위상정렬을 하고 다시 bfs를 돌리며 건물을 짓는데 걸리는 시간을 구했지만 in_degree를 이용한 bfs로 위상정렬을 해버리면 정렬을 하면서 건물을 짓는데 걸리는 시간을 바로 구할 수 있게 된다.

in_degree 방법을 사용하면 dfs와 다르게 상위 등수인 애들부터 출발하기 때문이다.(dfs를 사용하면 재귀적인 구조 특성상 마지막에 최상위 node를 알 수 있었기 때문에 등수만 구하는 것이 아닌 등수대로 진행하며 어떠한 동작을 하기에는 어려움이 있었다. 그래서 in_degree를 활용한 bfs를 통해 위상정렬을 하는 것)

## 1. indegree 배열 사용하기

dfs로 위상정렬을 하기 위해서 stack을 사용했다면, bfs로 위상정렬을 하기 위해선 indegree배열과 queue가 필요하다.

-> indegree == 0 인 node들부터 queue에 넣어서 진행하는 것이다.(indegree == 0) 이라는 의미는 해당 노드에 들어오는 edge가 없다! 즉, 최상위 node라는 의미이다.

우선 위 처럼 indegree가 0인 node들을 다 큐에 넣어두고, while(!q.empty())를 돌려서 큐에서 빼면 해당 node에서 나가는 edge들을 지워준다.(outdegree를 통해, 목적지 node의 indegree의 카운트를 하나 줄여주는 것이다.)

그러다가 node의 indegree가 0이 되면 그걸 다시 큐에 넣어주며 진행하면 된다.


## 2. 우선 순위를 꼭 지켜줘야 하는 관계라면 큐에 동시에 있을 수 없다.

큐에서 꺼낸 뒤에 outdegree를 없애주기 때문에 우선 순위가 꼭 고려되어야 하는 관계로 주어졌다면 자연스럽게 뒤에서 큐에 들어가게 될 것이다.

## 3. DAG(Directed Acyclic Graph) 인지 check하며 bfs 진행하기

현재 이 문제를 풀 때에는 while(front < rear) 로 큐가 빌때까지 진행했지만 원래는 node의 개수만큼 반복문을 돌려서 반복문이 끝나기 전에 큐가 빈다면??? **cycle이 존재한다.**고 판단한다.

그 이유는 cycle이 형성되어 있으면 indegree가 절대 0이 될 수 없다.(직접 그냥 그려서 봐도 불가능함) 따라서 cycle을 형성하는 node들은 큐에 들어갈 수 없고, 정점의 수만큼 반복문을 다 돌기 전에 큐가 비어버리는 것이다.

이렇게 위상 정렬을 함과 동시에 사이클의 존재 여부도 판단할 수 있다.



---

# 2. 소스코드

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct node {
	int v;
	struct node* next;
}node;

int n, target;
node* list[1001];
int time[1001]; // 각 건물을 짓는데 걸리는 시간
void init();
void insert_node(int, int);
void free_mem();


int degree[1001]; // 해당 node에 들어오는 edge수를 저장하는 indegree 배열
int cost[1001]; //선행 건물을 다 짓고 현재 건물을 짓는데 걸리는 시간 저장배열(DP개념이 들어감)
void bfs();

int main()
{
	int t;
	scanf("%d", &t);
	for (int k = 0; k < t; k++)
	{
		init();
		bfs();
		printf("%d\n", cost[target]);
		free_mem();
	}
	return 0;
}

void bfs() // in_degree를 활용한 bfs 위상정렬
{
	int q[1010], front = 0, rear = 0;
	for (int i = 1; i <= n; i++)
	{
		if (degree[i] == 0)
		{
			q[rear++] = i;
			cost[i] = time[i];
		}
	}
	while (front < rear)
	{
		int cur = q[front++];
		if (list[cur] == NULL)
			continue;
		node* temp = list[cur];
		while (temp != NULL)
		{
			if (cost[temp->v] < cost[cur] + time[temp->v])
				cost[temp->v] = cost[cur] + time[temp->v];
			degree[temp->v]--;
			if (degree[temp->v] == 0)
				q[rear++] = temp->v;
			temp = temp->next;
		}
	}
	return;
}

void init()
{
	int a, b, k;
	scanf("%d %d", &n, &k);
	for (int i = 1; i <= n; i++)
		scanf("%d", &time[i]);
	for (int i = 0; i < k; i++)
	{
		scanf("%d %d", &a, &b);
		degree[b]++;
		insert_node(a, b);
	}
	scanf("%d", &target);
	return;
}

void insert_node(int a, int b)
{
	node* temp = (node*)malloc(sizeof(node));
	temp->v = b;
	if (list[a] == NULL)
	{
		temp->next = NULL;
		list[a] = temp;
	}
	else
	{
		temp->next = list[a];
		list[a] = temp;
	}
	return;
}

void free_mem()
{
	for (int i = 1; i <= n; i++)
	{
		cost[i] = 0; // cost배열을 다시 초기화해줘야 내가 세운 DP 점화식이 다음 tc에서 정상적으로 작동함.
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
	return;
}
```

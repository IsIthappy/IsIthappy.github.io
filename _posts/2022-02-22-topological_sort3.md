---
title: "문제집"
excerpt: "우선순위 큐를 활용한 bfs 위상정렬"

categories:
    - Topological_sort
tags:
    - [PS, Topological_sort]
last_modified_at: 2022-02-22
---
# 문제집 문제 풀이
1. **문제의 이해**
2. 소스코드

백준 문제 링크 [https://www.acmicpc.net/problem/1766](https://www.acmicpc.net/problem/1766)


---

# 1. 문제집 문제의 이해

문제를 보고 든 생각은 일단 이 문제역시 우선 순위를 따져가며 진행 해야하니 위상정렬이라는 생각은 들었고, 위상정렬을 함과 동시에 추가적으로 붙는 조건을 어떻게 처리해야 할까 고민이 생겼다.

일단 이것 역시 단순 우선순위만 출력하는 것이 아닌, 우선 순위가 높은 애들부터 진행하면서 비교를 해야하기 때문에 root 부터 직관적으로 시작하는 bfs 위상정렬을 쓰기로 했다.

우선 처음에는 

1. 먼저 푸는 것이 좋은 문제가 있는 문제는, 먼저 푸는 것이 좋은 문제를 반드시 먼저 풀어야 한다.
2. 가능하면 쉬운 문제부터 풀어야 한다.

2번 조건을 보고, 아 이건 level로 정렬하고, level이 같다면 정점의 숫자 순으로 정렬이 들어가야 한다. 근데 정렬을 하면서 진행을 할 수 있는건 우선순위큐가 있으니 heap으로 구현을 해야겠다. 였으나!!! 틀렸다.

생각을 한번 다시 해보니까 level 순으로 정렬을 할 필요가 없었다.

가능하면 쉬운 문제부터 풀어야 한다. 5번 문제를 푸는데 선행 문제가 안필요하고, 3번 문제를 풀기 위해서 1,2번이 선행되어야 한다면

내가 구상한 알고리즘에서는 1 -> 5 -> 2 -> 3 의 순서로 진행이 됐었다.

하지만? 1 -> 2 -> 3 -> 5 가 맞는 순서이기 때문에... level 순서가 아니라 그냥 vertex의 숫자대로 우선순위를 잡고 heap을 구현하면 됐었던 문제였다...

---

# 2. 소스코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

typedef struct node {
	int v;
	struct node* next;
}node;

// 선후 관계 graph 형성 부분
node* list[32001];
int n;
void init();
void insert_node(int, int);
void free_mem();

// 우선순위큐를 사용해 bfs를 통한 위상정렬하기
int degree[32001];
void bfs();

int q[32001];
int top = 0;
bool cmp(int, int);
void swap(int, int);
void q_push(int);
int q_pop();

int main()
{
	init();
	bfs();
	free_mem();
	return 0;
}

void bfs()
{
	int cur;
	for (int i = 1; i <= n; i++)
	{
		if (degree[i] == 0)
			q_push(i);
	}
	while (top > 0) // 우선순위 큐가 빌 때 까지
	{
		cur = q_pop();
		node* temp = list[cur];
		while (temp != NULL)
		{
			degree[temp->v]--;
			if (degree[temp->v] == 0)
				q_push(temp->v);
			temp = temp->next;
		}
	}
	return;
}

void q_push(int cur)
{
	q[++top] = cur;
	int child = top;
	int parent = child / 2;
	while (parent > 0)
	{
		if (cmp(parent, child))
			swap(parent, child);
		else
			break;
		child = parent;
		parent /= 2;
	}
	return;
}

int q_pop()
{
	int buf = q[1];
	printf("%d ", buf);
	q[1] = q[top--];
	int parent = 1, child = 2;
	while (child <= top)
	{
		if (child < top && q[child] > q[child+1])
			child++;
		if (cmp(parent, child))
		{
			swap(parent, child);
			parent = child;
			child *= 2;
		}
		else
			break;
	}
	return buf;
}

bool cmp(int a, int b) // 항상 a가 더 작아야함.
{
	if (q[a] > q[b])
		return true;
	return false;
}

void swap(int a, int b)
{
	int temp = q[b];
	q[b] = q[a];
	q[a] = temp;
	return;
}

void init()
{
	int k, a, b;
	scanf("%d %d", &n, &k);
	for (int i = 0; i < k; i++)
	{
		scanf("%d %d", &a, &b);
		insert_node(a, b);
		degree[b]++;
	}
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
```

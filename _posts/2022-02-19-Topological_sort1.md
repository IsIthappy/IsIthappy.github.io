---
title: "줄 세우기"
excerpt: "위상정렬... 별거 없다, dfs 도중 cycle 존재 여부 check!"

categories:
    - Topological_sort
tags:
    - [PS, Topological_sort]
last_modified_at: 2022-02-19
---
# 줄 세우기 문제 풀이
1. **문제의 이해**
2. 소스코드

백준 문제 링크 [https://www.acmicpc.net/problem/2252](https://www.acmicpc.net/problem/2252)


---


# 1. 줄 세우기 문제의 이해

문제를 처음 봤을 때에는 상당히 난해했다... 순서를 맞춰줘야 하는데

1 -> 4

1 -> 3 -> 4

이런식으로 사이 사이에 계속 끼워넣어주는게 시간을 고려하지 않으면 O(N^2)으로 어떻게 해서든 구현이 되겠지만 시간적으로도, 또 component가 두개 이상이면 그때는 list를 하나 더 추가할 것인가? 말이 안된다.

그러다 문득 graph를 표현하고 post order 순회를 해주면 되지 않을까??? 싶은 생각이 문득 들었다. 여기서 생각을 조금만 더 신중하게 했었다면 풀 수 있었을텐데... 이 문제에서는 중복 node가 주어질 수도 있고 root node가 어디인지 모르니까 이 방법으로는 풀 수 없겠다라고 생각이 들어서... 질문게시판을 커닝했다.

>post order... 이 생각의 연장선이 위상 정렬 알고리즘의 틀이었다.

우선 내가 잘못 생각했었던 부분들이

1. 선후 관계만 파악하기 위해서라면 root node를 굳이 알 필요가 없다. (tree가 아닌데 뭔 root node를 찾을 생각을 하냐, 이게 실수였다.)
2. 그럼 1 -> 2 -> 3 이런 구조일 때, 3부터 탐색하면 어떡하냐? 3보다 누가 더 큰지 모르지않냐? 라고 할 수 있지만....... 알 필요가 없었다...


>dfs를 활용한 위상정렬 알고리즘을 알아보자


## 1. directed acyclic graph 에서만 사용할 수 있다. (사이클이 존재하면 사용할 수 없음.)

이 문제같은 경우에는 답이 존재하지 않는 경우가 없으므로 사이클이 있는 경우는 주어지지 않겠구나라고 생각할 수 있지만, 답이 존재하지 않는다? -> 사이클이 존재할 수도 있다. -> 사이클의 유무 판단을 해줘야 한다.

이럴때는 dfs를 활용하여 위상 정렬을 하는 과정에서 사이클의 존재 유무를 판단할 수 있다.

```c
bool finish[];
bool visit[];
dfs(int cur)
{
    visit[cur] = true;
    while(cur에 연결된 node들 방문)
    {
        if(visit[cur->v] == false)
            DFS(cur->v);
        if(visit[cur->v] == true && finish[cur->v] == false) 
            (방문한 적은 있는데, 해당 node가 finish가 아니다? 아직 그곳으로부터 경로가 진행중이란 소리)
            즉, 사이클이 형성된다는 의미이다.
    }
    finish[here] = true;
}
```

## 2. 하나의 DAG(Directed Acyclic Graph)으로부터 여러개의 위상 순서가 나올 수 있다.

leaf node부터 stack에 넣어서 최종 node인 root 부터 최초의 leaf node 순으로 출력이 진행되므로, graph가 완벽한 선후관계로 주어지지 않는 이상, 구현에 따라 선후관계가 약간씩 다를 수도 있다.


## 3. 위상 정렬은 그냥 dfs다... leaf node라면 stack에 해당 node를 저장하여 선후 순서를 기억하는..

dfs 진행 중에 방문체크를 통해 중복 방문은 허용하지 않는다. 따라서 내가 처음 생각했던 중복 node가 입력으로 들어오면 걔내들은 어떻게... 이건 고민할 필요가 없었다.

또한 root node를 모르는데 어떻게 해?.... leaf node 순으로 stack에 넣어서 마지막 출력할 때에는 stack[top]부터 역순으로 출력되기 때문에 최상위 우선순위를 자랑하는 놈부터 출력이 될 수밖에 없다.

따라서 root node를 모르는데 어떻게 해?.... 이것도 전혀 문제가 될 수 없는 부분이었다.

---

# 2. 소스코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

typedef struct node
{
	int v;
	struct node* next;
}node;

node* list[32001]; // 그래프 표현을 위한 인접 리스트
int n; // 학생 수 (학생의 번호는 1 ~ N )

int stack[32000]; // 정렬 된 학생 정보를 담을 stack (leaf node부터 담기 시작해서 root부터 출력해야 하니까)
int top = -1;

void init();
void insert_node(int, int);
void free_mem();

void dfs(int);
bool visit[32001];

int main()
{
	init();
	for (int i = 1; i <= n; i++)
	{

		if(visit[i] == 0)
			dfs(i);
	}
	while (top > -1) // 정렬된 list 출력
		printf("%d ", stack[top--]);
	free_mem();
	return 0;
}

void dfs(int cur)
{
	visit[cur] = true;
	node* temp = list[cur];
	while (temp != NULL)
	{
		if (visit[temp->v] == 0)
			dfs(temp->v);
		temp = temp->next;
	}
	stack[++top] = cur; // 더 이상 방문할 node가 없다면 leaf node처럼 생각하고 stack에 넣어주기
	return;
}

void init()
{
	int a, b, m;
	scanf("%d %d", &n, &m);
	for (int i = 0; i < m; i++)
	{
		scanf("%d %d", &a, &b);
		insert_node(a, b);
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

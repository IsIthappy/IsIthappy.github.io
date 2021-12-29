---
title: "Tree 표현하기"
excerpt: "상황에 따라서 tree를 어떻게 구현할 것인가?"

categories:
    - Datastructure
tags:
    - [Note, Datastructure]
last_modified_at: 2021-12-29 
---

# Tree 정리 목차
1. Tree란 무엇인가?
2. 문제를 통해 Tree 직접 구현해보기


# 1. Tree란 무엇인가?

> 데이터 구조가 선형적이지 않고 계층적으로 구성되어있는 자료구조이다.


종류로는 다양하게 트리, 이진트리, 완전이진트리, 레드블랙트리 등등 엄청 많다.



---



# 2. Tree를 표현하는 방법

>문제를 통해서 상황에 따라 tree를 표현하는 방법에 대해서 알아보도록 하자.

1) Adjency List로 표현하기

2) 단순 Linked List로 표현하기

3) 이진 검색 tree 구현하기

### 1) Adjency List를 통해 표현하기

[https://www.acmicpc.net/problem/11725](https://www.acmicpc.net/problem/11725)

백준 11725번 Tree의 부모찾기 문제이다.


이 문제는 각 node들의 parent node를 확인해야 하는 상황이다. 즉, node를 모두 방문해야 하며 방문할 때 parent node 역시 참조가 가능해야 하는 상황이라고 할 수 있다.

> tree의 순회, graph 탐색(dfs, bfs) 을 최종적으로 해야하는 상황이며 그에 맞는 tree 구현이 필요한 상황이다.


tree를 어떻게 구현할 것인가? node의 갯수가 100,000개까지 주어지며, tree의 형식또한 정해지지 않았기 때문에 하나의 node가 몇개의 child를 가질지도 모르는 상황이다.

> 즉, Adjency matrix(2차원 배열)을 통해서는 메모리가 너무 비효율적으로 사용될 가능성이 있다.


또한 연결되는 node들의 순서가 랜덤하게 들어오기 때문에 Loot로부터 쭈욱 연결해가는 방식은 사실상 불가능하다.
그렇기 때문에 모든 vertex들의 List를 구성해놓고, vertex에 연결된 node들을 Link해주는 Adjency List방식으로 구현해보았다.


> 이 부분은 C++ 에서의 vector STL을 사용하면 직접 연결리스트를 구현하지 않고 문제를 풀 수 있긴 하다.


```c
#include <stdio.h>
#include <stdlib.h>

typedef struct node{
	int child;
	struct node* next;
}node;

int n;
int visit[100001]; // 마지막 tree의 각 node 방문체크, 부모확인을 위한 배열이다.
node* list[100001]; // vertex List

void init();
void insert_node(int, int);
void dfs(int); // tree 탐색으로 dfs 기법을 선택하였다.
void free_memory();

int main()
{
	init();
	visit[1] = 1;
	dfs(1);
	for (int i = 2; i <= n; i++) // 2번 node부터 자신의 parent node 번호 출력
		printf("%d\n", visit[i]);
	free_memory();
	return 0;
}

void dfs(int c)
{
	node* cur = list[c];
	while (cur != NULL)
	{
		if (visit[cur->child] == 0)
		{
			visit[cur->child] = c; // 방문체크와 동시에 자신의 부모 노드 저장
			dfs(cur->child);
		}
		cur = cur->next;
	}
	return;
}

void insert_node(int a, int b)
{
	node* cur = (node*)malloc(sizeof(node));
	cur->child = b;
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

void init() // undirected graph이기 때문에 양쪽으로 연결해줘야한다.
{
	int a,b;
	scanf("%d", &n);
	for (int i = 1; i < n; i++)
	{
		scanf("%d %d", &a, &b);
		insert_node(a, b);
		insert_node(b, a);
	}
	return;
}

void free_memory()
{
	for (int i = 1; i <= n; i++)
	{
		if (list[i] != NULL)
		{
			node* cur = list[i];
			node* pre = cur;
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


### 2) 단순 Linked List로 표현하기

[https://www.acmicpc.net/problem/1991](https://www.acmicpc.net/problem/1991)

백준 1991번 Tree를 구현해서 전위, 중위, 후위 순회를 해야 하는 문제이다.


이 문제에서는 이진 트리라고 정해졌기 때문에 배열을 통해서 tree를 표현해볼까? 라는 생각을 할 수 있다. 하지만 완전 이진 트리가 아니기 때문에 한쪽으로만 child가 달릴 경우 배열의 size가 2^26 만큼 필요할 수도 있기 때문에 이 문제에서 역시 tree는 linked list 기반으로 구현이 되어야 한다.


하지만 이 문제의 경우에 잘 읽어보면 각 node들 간의 연결 상태가 계층적으로 주어진다는 것을 알 수 있다. root 부터 차례 차례 연결이 진행되는데 때문에 그냥 root부터 내려오는 Linked list를 통해서 tree를 구현할 수 있다.


> 이런식으로 이진트리라는 정보가 주어진다면 C++ STL map을 통해서 구현이 가능하긴 하다.

[https://breakcoding.tistory.com/205](https://breakcoding.tistory.com/205)

(설명이 매우 잘 되어있음.)


```c

#include <stdio.h>
#include <stdlib.h>

typedef struct node {
	char v;
	struct node* left;
	struct node* right;
}node;

int n;
node* root;

void init();
node* search(char); // root에서부터 node 찾아가는 함수

void preorder(node*); // recursive 기반의 tree 순회 함수이다.
void inorder(node*);
void postorder(node*);
void free_mem(node*);

int main()
{
	init();
	preorder(root);
	printf("\n");
	inorder(root);
	printf("\n");
	postorder(root);
	free_mem(root);
	return 0;
}

void init()
{
	scanf("%d", &n);
	root = (node*)malloc(sizeof(node));
	root->v = 'A';
	for (int i = 0; i < n; i++)
	{
		char p, c1, c2;
		getchar(); // %c로 입력을 받아야하기 buffer에 남아있는 개행 문자를 지워줘야 한다.
		scanf("%c %c %c", &p, &c1, &c2);
		if (c1 == '.' && c2 == '.')
			continue;
		node* cur = search(p); // root로부터 연결된 node를 찾고 거기에 child를 이어붙이는 과정이다.
		if (c1 == '.')
			cur->left = NULL;
		else
		{
			node* temp = (node*)malloc(sizeof(node));
			temp->v = c1;
			temp->left = NULL;
			temp->right = NULL;
			cur->left = temp;
		}
		if (c2 == '.')
			cur->right = NULL;
		else
		{
			node* temp = (node*)malloc(sizeof(node));
			temp->v = c2;
			temp->left = NULL;
			temp->right = NULL;
			cur->right = temp;
		}
	}
}

node* search(char p)
{
	node* stack[30];
	int top = -1;
	stack[++top] = root;
	while (top > -1)
	{
		node* temp = stack[top--];
		if (temp->v == p)
			return temp;
		if (temp->left != NULL)
			stack[++top] = temp->left;
		if (temp->right != NULL)
			stack[++top] = temp->right;
	}
}

void preorder(node *cur)
{
	printf("%c", cur->v);
	if (cur->left != NULL)
		preorder(cur->left);
	if (cur->right != NULL)
		preorder(cur->right);
	return;
}

void inorder(node* cur)
{
	if (cur->left != NULL)
		inorder(cur->left);
	printf("%c", cur->v);
	if (cur->right != NULL)
		inorder(cur->right);
	return;
}

void postorder(node* cur)
{
	if (cur->left != NULL)
		postorder(cur->left);
	if (cur->right != NULL)
		postorder(cur->right);
	printf("%c", cur->v);
	return;
}

void free_mem(node* cur)
{
	if (cur->left != NULL)
		free_mem(cur->left);
	if (cur->right != NULL)
		free_mem(cur->right);
	free(cur);
	return;
}

```


### 3) 이진 검색 tree 구현하기

[https://www.acmicpc.net/problem/5639](https://www.acmicpc.net/problem/5639)

이진 검색 트리를 구현하는 문제이다.

문제에서 이진 검색 트리가 무엇인지 설명이 나와있긴 하지만 요약하자면 root 부터 이진 트리 구성대로 node들을 채워 가는데 이때 조건이 있다.

1. left child key < parent key
2. right child key > parent key

이 조건을 만족하면서 계속 이어붙여나가면 된다.

> 이 문제 역시 완전이진트리가 아니기 때문에 배열 기반으로 tree를 표현하기엔 어려움이 있다.

```c
#ifdef _MSC_VER
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <stdlib.h>

typedef struct node
{
	int v;
	struct node* left;
	struct node* right;
}node;

node* root;

void insert_node(int);
void postorder(node*);

int main()
{
	int buf;
	while (scanf("%d", &buf) != EOF)
		insert_node(buf);
	postorder(root);
	return 0;
}

void insert_node(int buf)
{
	node* cur = (node*)malloc(sizeof(node));
	cur->v = buf;
	cur->left = NULL;
	cur->right = NULL;
	if (root == NULL)
		root = cur;
	else
	{
		node* temp = root;
		while (temp != NULL) // 추가될 node가 들어갈 자리를 찾는 반복문이다.
		{
			if (temp->v < cur->v) // right child 후보
			{
				if (temp->right == NULL)
				{
					temp->right = cur;
					break;
				}
				else
					temp = temp->right;
			}
			else if (temp->v > cur->v) // left child 후보
			{
				if (temp->left == NULL)
				{
					temp->left = cur;
					break;
				}
				else
					temp = temp->left;
			}
		}
	}
	return;
}

void postorder(node* cur)
{
	if (cur->left != NULL)
		postorder(cur->left);
	if (cur->right != NULL)
		postorder(cur->right);
	printf("%d\n", cur->v);
	return;
}

```
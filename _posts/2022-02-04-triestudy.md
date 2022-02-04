---
title: "trie 자료구조"
excerpt: "문자열을 저장하고 효율적으로 탐색하기"

categories:
    - Datastructure
tags:
    - [Note, Datastructure]
last_modified_at: 2022-02-04 
---

# Trie 정리 목차
1. trie를 사용해야 하는 문제의 이해
2. Trie 자료구조란 무엇인가?
3. 소스코드


# 1. 문제의 이해

[https://www.acmicpc.net/problem/5052](https://www.acmicpc.net/problem/5052)

일단 원문으로 문제를 풀었는데 첫 줄 제일 중요한 부분에서 해석을 못해가지고 파파고의 힘을 조금 빌렸다.

>Given a list of phone numbers, determine if it is consistent in the sense that no number is the preﬁx of another.

1. consistent : 일관된, 한결같은
2. prefix : 접두사
3. in the sense that : ~라는 점에서

주어진 전화번호들의 리스트가, 일관되어있는지 결정해라, 어떠한 숫자도 다른 숫자의 접두사가 아니라는 점에서

-> 서로가 서로의 접두사면 안된다 이거임.

일단 단순하게 든 생각은 이중 for문으로 계속 비교해야 된다는 것이었다.

1. sort하고 자리수대로 뚝뚝 떼서 대가리를 비교하면 한번에 슈슉 처리가 가능할 것이라고 생각했지만, 맨 앞자리가 0인 숫자들의 경우 처리가 곤란해져서 이 방법은 패스
2. 무지성 이중 for문으로 하나하나 비교, O(N^2) 에다가 각 자리 비교해야 하므로 시간복잡도가 터무니가 없다

따라서 한번에 여러개를 처리할 수 있는 방법을 생각해야 했다.

>그래서 떠오른 방법이 hash table.

자릿수 대로 hash table을 구성하면 맨 첫번째 자릿수는 일단 같은것이니까 다시 그 해쉬를 떼서 2번째 자리대로 분산하면 된다고 생각했음.

근데 이러면 흐름의 분기가 존나 많이 일어나고 이 data들을 관리할 방법이 떠오르지 않았다.

-> 이건 도저히 내가 알고있는 수준에서는 해결할 수 없다고 판단. 질문게시판 커닝

trie라는 자료구조를 알게 되었다.



---


# 2. trie 자료구조란 무엇인가?

>문자열을 저장하고 효율적으로 탐색하기 위한 자료구조이다.

내가 위에서 생각했던 저 흐름의 분기를 그냥 tree로 구현해낸 것이다.

각 node가 child node를 해당 자리에 들어올 수 있는 문자의 수만큼 가져서 메모리를 엄청 잡아먹겠지만 


>trie가 만들어진다면, 문자열이 몇개가 있던 간에 O(N)의 시간복잡도로 문자열을 탐색할 수 있게된다.


이때, 주어진 문자열의 길이가 길다면? 메모리가 미친 듯이 많이 필요하기 때문에 이를 잘 고려해서 사용해야 한다.


---

# 3. 소스코드

구현하는데 살짝 생각이 필요했던 부분들

1. root node를 더미노드로 생각하고서는 메모리 할당을 안해준 것
2. 접두사가 겹치는지 어떻게 파악하는가
3. 중간에 접두사가 겹치는걸 확인했다면 중간에 종료시킬 수 있는가?
4. 9119 , 911 순으로 입력이 주어질 때, 즉 후에 terminal node가 갱신 될 경우 어떻게 처리할 것인가?


```c
#include <stdio.h>
#include <stdbool.h>
#include <stdlib.h>

typedef struct node // 각 자리수는 index로 구분되니까 굳이 저장안해도 되지.
{
	struct node* child[10];
	bool flag; // terminal node인지 판단
}node;

node* root; // 편의상 dummy node를 만들어두고 시작하는 것

node* make_node();
bool make_trie();
void free_mem(); // 입력이 여러번 주어지기 때문에 할당을 잘 풀어줘야함.
void recursive_del();

int main()
{
	int t,n;
	scanf("%d", &t);
	root = make_node(); // 더미노드 생성
	for (int z = 0; z < t; z++)
	{
		scanf("%d", &n);
		bool flag = true;
		for (int x = 0; x < n; x++) // 이 입력을 끝까지 다 받아야 YES임.
		{
			if (flag)
				flag = make_trie();
			else // 입력은 계속 받아줘야 하기때문에 buffer 비우는 작업이라 생각하면 된다.
			{
				char trash[11];
				scanf("%s", trash);
			}
		}
		if (flag)
			printf("YES\n");
		else
			printf("NO\n");
		free_mem();
	}
	return 0;
}

bool make_trie() // 여기서 terminal node로 접두사가 겹치는지 확인하기
{
	char buf[11];
	scanf("%s", buf);
	int index = 0;
	node* cur = root;
	while (buf[index] != '\0') // 이전 노드를 계속해서 기억하고 있어야함
	{
		int digit = buf[index] - '0';
		if (cur->child[digit] == NULL) // 현재 진행하고자 하는 자릿수가 없었음.
		{
			node* temp = make_node();
			cur->child[digit] = temp;
			cur = cur->child[digit];
		}
		else // 현재 자릿수 숫자까지 이미 가진 숫자가 있었음.
		{
			cur = cur->child[digit];
			if (cur->flag)// 이동한 노드가 terminal node라면? 해당 숫자를 prefix로 가지는 것
				return false;
		}
		index++;
	}
	// 끝까지 다 진행했다면 terminal node 처리, 하고서 데리고 있는 자식새끼 있나 한번 점검해야지
	for (int i = 0; i < 10; i++) // 이 부분을 안해줘서 틀렸었음. ex) 9119, 911 순의 입력
	{
		if (cur->child[i] != NULL)
			return false;
	}
	cur->flag = true;
	return true;
}

node* make_node()
{
	node* temp = (node*)malloc(sizeof(node));
	for (int i = 0; i < 10; i++)
		temp->child[i] = NULL;
	temp->flag = false;
	return temp;
}

void free_mem()
{
	for (int i = 0; i < 10; i++)
	{
		if (root->child[i] == NULL)
			continue;
		recursive_del(root->child[i]);
		root->child[i] = NULL; // 이 부분 root를 재사용하기 위해 굉장히 중요한 부분이다.
	}
	return;
}

void recursive_del(node* cur)
{
	for (int i = 0; i < 10; i++)
	{
		if (cur->child[i] == NULL)
			continue;
		recursive_del(cur->child[i]);
	}
	free(cur);
	return;
}

```
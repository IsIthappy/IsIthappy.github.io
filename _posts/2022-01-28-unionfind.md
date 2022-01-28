---
title: "union - find 알고리즘"
excerpt: "disjoint - set을 구하는 알고리즘"

categories:
    - Algorithm
tags:
    - [Note, Algorithm]
last_modified_at: 2022-1-28 
---

# union - find 알고리즘 노트 목차
1. union - find 알고리즘이란 무엇인가?
2. 벨만포드 알고리즘 사용 예시 두가지 문제
3. 후기


# 1. union - find 알고리즘이란?

>대표적인 그래프 알고리즘 중에 하나이다.

정점들이 같은 component에 속하는지 판별하는 알고리즘으로 집합에서의 partition 개념을 생각하면 된다.

#### 핵심적인 포인트

**1) union 연산**

연결되어 있는 정점들을 하나의 집합으로 합치는 과정이다.

연결되어 있는 정점들이 같은 component에 속해있는지 알려면 bfs, dfs 알고리즘과 같이 그래프를 탐색하는 과정이 필요한데, 단지 하나의 component에 속해있는지 빠른 시간에 판별하고 싶다면 union 연산을 통해서 parent node들을 이어주는 것이다.

```c
void Union(int a, int b) // 일반적으로 더 작은 node를 parent로 지정한다.
{
	a = find(a); // 주어진 두 node의 component에서 root node 찾기
	b = find(b);
	if (a < b) // 두 root node로 component 합쳐주기(딸려있는 자식까지 통째로 옮기는 것이다.)
		parent[b] = a;
	else
		parent[a] = b;
	
	/*find(a);
	find(b);
	if (parent[a] < parent[b])
		parent[b] = parent[a];
	else
		parent[a] = parent[b];*/
	return;
}
```
주석 처리된 부분은 내가 처음에 잘못된 생각으로 구현한 union 함수이다. find 함수를 거치고 나면, 해당 node의 parent node는 알아서 해당 component의 root node로 지정이 되기 때문에, 이를 통해서 union하면 된다고 생각했는데 이러면

4 2

4 1

이렇게 세 정점을 union 해버리면 어떻게 되느냐?

4와 2의 parent node는 2였고, 

4와 1을 union하는 과정에서 4의 parent node가 1로 바뀌면서 2의 union이 끊어져 버린다. 

따라서 4의 root node를 찾고, 그 root node인 2의 parent node를 1로 지정해줘서 연결이 끊어지지 않게 union 함수를 구성한 것이 위에 주석처리되지 않은 부분의 코드이다.

>이러면 4의 root node는 1로 바뀌지 않는데요?

이건 차후에, 두 정점이 같은 component인지 체크하는 과정에서 다시 find 함수를 호출해 최정상 root node를 찾아보면 된다.

**2) find 연산.**

해당 정점의 parent node를 찾는 연산이다. 재귀 함수를 통해 짜여져 있다.

이 find 연산이 끝나면 **해당 정점의 parent node는 root node로 지정**이 되어있다.

이 이유는 편향 트리를 고려해서 짜여진 것인데, 재귀 함수를 통해 최정상 parent node인 root node를 찾고, 

그것으로 정점들을 이어주면, 다음에 정점들끼리 같은 component에 속해있는지 판별할 때, O(1) 의 시간복잡도를 가지고 판별이 가능해지기 때문이다.

level이 2인 tree로 재구성하는 느낌이라고 생각하면 된다.

```c
int find(int cur)
{
	if (parent[cur] == cur) // parent node가 자기 자신이면 해당 component의 root node인 것이다.
		return cur;
	else
		return parent[cur] = find(parent[cur]); // 이 부분이 핵심이다. parent node를 root node로 계속 갱신하는 것
}

```
---

# 2. union - find 알고리즘 사용 예시 두가지 문제


### 1) 집합의 표현(union - find 적용 표준 문제)

[https://www.acmicpc.net/problem/1717](https://www.acmicpc.net/problem/1717)

union - find 알고리즘을 적용해서 풀 수 있는 가장 기본적인 형태의 문제이다.

문제에서 주어진 연산은 두가지이다.

1. 주어진 두 정점을 하나의 집합으로 합치기(component 합치는 연산)
2. 주어진 두 정점이 같은 component에 속하는지 체크하기

union, find 두가지 연산을 구현하라는 의미이다.

union, find 외에 check_parent 함수가 추가되었는데, 

이는 union 과정에서 정점들이 연결은 되어있지만 최종적으로 parent 배열에 root node로 갱신이 되어있는 것은 아닌 상태이다.

따라서 check_parent 함수 내부에서 find 함수 호출을 통해 root node로 갱신을 해줌과 동시에 두 정점이 같은 component에 속하는지 판별하는 함수이다.

```c
#include <stdio.h>
#include <stdbool.h>

int parent[1000001];

int find(int);
void Union(int, int);
bool check_parent(int, int);

int main()
{
	int n,m,op,a,b;
	scanf("%d %d", &n, &m);
	for (int i = 1; i <= n; i++) // 맨 처음에는 자기 자신이 root node가 되게끔 초기화를 해줘야한다.
		parent[i] = i;
	for (int t = 0; t < m; t++)
	{
		scanf("%d %d %d", &op, &a, &b);
		if (op == 0)
			Union(a, b);
		else
		{
			if (check_parent(a, b))
				printf("YES\n");
			else
				printf("NO\n");
		}
	}
	return 0;
}

bool check_parent(int a, int b)
{
	if (find(a) == find(b))
		return true;
	else
		return false;
}

void Union(int a, int b)
{
	a = find(a); // 주어진 두 node의 component에서 root node 찾기
	b = find(b);
	if (a < b) // 해당 root node들로 component 합치기
		parent[b] = a;
	else
		parent[a] = b;
	return;
}

int find(int cur)
{
	if (parent[cur] == cur)
		return cur;
	else
		return parent[cur] = find(parent[cur]);
}
```

# 2. 거짓말 (조금 생각해야 했던 문제)


[https://www.acmicpc.net/problem/1043](https://www.acmicpc.net/problem/1043)


## brutefroce 알고리즘으로 풀기

이 문제를 처음 풀 때 당시에는 유니온 파인드 알고리즘을 몰랐던 상태여서 그래프를 하나로 다 이어주는 방법을 몰랐었다.

진실을 아는 사람과 같은 파티에 참석하는 사람들을 다 이어줘야 한다는 것까지는 파악을 했지만 이렇게 무분별하게 주어지는 입력에서 그래프를 어떻게 표현할지를 몰라 test case가 작은 것을 보고 brute force로 풀어버렸다.

1. M개의 파티를 훑는다
2. 진실을 아는 놈이 있는 파티를 찾고 해당 파티에 온 애들에게 소문을 낸다.
3. 진실을 아는 놈이 있었고 해당 파티에서 거짓말을 하지 못한다고 갱신이 된다면 이 과정을 한번 더 반복한다.

(갱신이 되지 않을 때까지 반복하는 것)

즉, worst case를 생각해보면

1

1 2

1 2 3

...

1 ~ 49

1 ~ 50

50개의 파티가 주어지고 파티에 참석하는 사람이 위와 같이 주어질 때, 진실을 아는 사람이 50이라면?

M개의 파티를 갱신이 되지 않을때 까지인 M번 탐색해야 하며, 그 때마다 1~50명으로 등차수열을 이루고 있는 인원 수를 훑어야 한다.

인원수 = 항의 개수 * (초항 + 끝항) / 2

즉, O(M * M((N*N))) = O(M^2 * N^2) 의 시간복잡도를 가지겠지만 test case가 매우 작기 때문에 이와 같은 알고리즘으로 풀 수 있다.

```c

#include <stdio.h>
#include <stdbool.h>

bool person[51]; // true면 진실을 아는 애
bool flag_p[50]; // true면 진실을 말해야 하는 파티
int party[50][51]; // 첫번째 칸에는 해당 파티의 인원 수가 저장되어 있음.
int n, m;

void init();
void bruteforce();

int main()
{
	init();
	bruteforce();
	int count = 0;
	for (int i = 0; i < m; i++)
	{
		if (flag_p[i] == false)
			count++;
	}
	printf("%d", count);
	return 0;
}

void bruteforce()
{
	bool flag = true;
	while (flag) // 진실을 아는 놈이 있어 파티를 갱신해줬다면 while문 탐색
	{
		flag = false;
		for (int k = 0; k < m; k++) // 파티 돌면서 진실을 아는 놈 전염 되는지 체크 해줘야지
		{
			if (flag_p[k] == true)
				continue;
			for (int i = 1; i <= party[k][0]; i++)
			{
				if (person[party[k][i]] == true) // flag_p 는 false인데 진실을 아는 놈이 들어있다?
				{
					flag_p[k] = true; // 해당 파티에서 과장 못하는 걸로 바꿔주고
					for (int j = 1; j <= party[k][0]; j++) // 진실 소문내야지
					{
						person[party[k][j]] = true;
						flag = true;
					}
					break;
				}
			}
		}
	}
	return;
}

void init()
{
	scanf("%d %d", &n, &m);
	int num_person,buf;
	scanf("%d", &num_person);
	for (int i = 0; i < num_person; i++) // 진실을 아는 사람 입력받기
	{
		scanf("%d", &buf);
		person[buf] = true;
	}
	for (int k = 0; k < m; k++) // 파티의 수 입력 받기
	{
		scanf("%d", &buf);
		party[k][0] = buf; // 해당 파티 인원수 저장 해둬야지
		for(int i=1;i<=buf;i++)
		{
			scanf("%d", &party[k][i]);
			if (person[party[k][i]] == true)
				flag_p[k] = true;
		}
		if (flag_p[k] == true) // 진실을 아는 놈 싹 다 퍼뜨려줘야지
		{
			for (int i = 1; i <= buf; i++)
				person[party[k][i]] = true;
		}
	}
	return;
}

```

## 유니온 파인드로 풀기

이렇게 생각해서 풀고 난 뒤, 질문게시판으로 보니 유니온 파인드라는 알고리즘의 존재를 알게 되었다.

확실한 것은 brute-force와 달리 union 과정을 통해 바로 하나의 component에 속하는지 판별이 되기 때문에 더 효율적이라고 생각된다.

union find 알고리즘만 단순 구현하는게 아니라 union find 알고리즘을 문제 상황에 맞게 적절하게 사용해야 효율적으로 풀 수 있는 문제였다.

```c

#include <stdio.h>
#include <stdbool.h>

int parent[51];
int party[50][51]; // 파티의 정보를 가지고 있어야함.
int know_person[50];
bool flag[51];
int n, m,k; // 사람 수, 진실을 아는 사람 수

void init();
int find(int);
void Union(int, int);
bool check_parent(int, int);
void communicate();
int check_party();

int main()
{
	init(); // 모든 데이터 입력받고, union 과정까지 끝났음
	communicate();
	printf("%d",check_party());
	return 0;
}

void communicate() // 진실을 아는 놈이랑 같은 union이면 소문나야지
{
	for (int i = 1; i <= n; i++)
	{
		if (flag[i])
			continue;
		for (int j = 0; j < k; j++) // 진실을 모르는 놈이 진실을 아는 놈이랑 같은 union 이라면
		{
			if (check_parent(know_person[j],i)) // 같은 유니온이라면? 소문내야지
			{
				flag[i] = true;
				break;
			}
		}
	}
	return;
}

int check_party()
{
	int count = m;
	for (int i = 0; i < m; i++)
	{
		for (int j = 1; j <= party[i][0]; j++)
		{
			if (flag[party[i][j]])
			{
				count--;
				break;
			}
		}
	}
	return count;
}

void init()
{
	scanf("%d %d", &n, &m);
	for (int i = 1; i <= n; i++)
		parent[i] = i; // 부모 노드를 자기 자신으로 맞춰주기
	
	scanf("%d", &k);
	for (int i = 0; i < k; i++) // 진실을 아는 놈 입력받기
	{
		scanf("%d", &know_person[i]);
		flag[know_person[i]] = true; //진실 안다고 체크 해주기
	}

	for (int i = 0; i < m; i++) // 파티 입력받기
	{
		int least_parent = 51; // 한번에 union 처리 해주기 위한 변수
		scanf("%d", &party[i][0]); // 파티당 맨 첫번째 index는 해당 파티에 참여하는 사람 수
		scanf("%d", &party[i][1]); // 일단 한명 입력 받고
		for (int j = 2; j <= party[i][0]; j++)
		{
			scanf("%d", &party[i][j]);
			Union(party[i][1], party[i][j]);
		}
	}
	return;
}

void Union(int a, int b) // 더 작은 root로 union 하기 --- 이 둘의 차이가 뭐지?
{
	a = find(a);
	b = find(b);
	if (a < b)
		parent[b] = a;
	else
		parent[a] = b;
	return;
}

int find(int cur)
{
	if (parent[cur] == cur)
		return cur;
	else
		return parent[cur] = find(parent[cur]);
}

bool check_parent(int a, int b)
{
	if (find(a) == find(b))
		return true;
	else
		return false;
}

```

# 4. 후기

그래프가 하나의 component에 속하는지 판별 할 수 있는 union-find 알고리즘... 굉장히 효율적이고 좋은 알고리즘 같다. 이렇게 또 하나를 알아가는구만!
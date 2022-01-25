---
title: "트리 순회"
excerpt: "반복해서 문제를 작게 만들 수 있으면 divide & conquer을 의심하자."

categories:
    - DivideConquer
tags:
    - [PS, DivideConquer]
last_modified_at: 2022-01-23 
---
# 트리 순회 문제 풀이
1. **문제의 이해**
2. 소스코드
3. 후기

백준 문제 링크 [https://www.acmicpc.net/problem/2263](https://www.acmicpc.net/problem/2263)


---

# 1. 트리 순회 문제의 이해

뭐 처음에 이 문제를 봤을 때는 막막했다. 맨 처음 기준으로 생각해보면

1. 맨 처음 node는 왼쪽 끝 leaf node이다.
2. post order 기준으로 주어진 맨 오른쪽 node는 root node이다.
3. 이 root node를 기준으로 inorder에선 절반씩 뚝 자를 수 있다.

여기에서 생각이 멈춰가지고 진전이 없었는데 가만보니까 이러면 또 왼쪽 subtree, 오른쪽 subtree로 나눌 수 있고 위에 과정을 고대로 다시 해주면 된다는 생각이 들었다.

그래서 그냥 단순 재귀로 짤려고 했는데 문제가 오른쪽 subtree를 나눌 때 index가 꼬여버린다.

>결론은 이.. index를 맞추는게 핵심이었던 문제라고 생각한다...

오른쪽 subtree index 나누는게 조금 생각이 필요한 부분이었다.

---

# 2. 소스코드

```c

#include <stdio.h>

int in[100000];
int post[100000];
int n;

void init();
void divide(int,int,int,int); // 오른쪽 subtree의 경우 index가 자꾸 틀어지므로 post, in 따로 따로 매개변수가 필요함.

int main()
{
	init();
	divide(0, n-1 ,0 , n -1);
	return 0;
}

void divide(int ll, int lr, int rl, int rr)
{
	if (ll == lr)
	{
		printf("%d ", in[ll]);
		return;
	}
	int root;
	for (int i = ll; i <= lr; i++)
	{
		if (post[rr] == in[i])
		{
			root = i;
			break;
		}
	}
	printf("%d ", in[root]);
    // 이 subtree별 index를 정해주는게 조금 복잡했다.
	if (ll <= root - 1 && rl <= rl + (root - ll -1))
		divide(ll, root - 1, rl, rl + (root - ll -1)); // 왼쪽 subtree
	if (root + 1 <= lr && rr - (lr - root) <= rr-1)
		divide(root + 1, lr, rr - (lr - root) , rr - 1); // 오른쪽 subtree
}


void init()
{
	scanf("%d", &n);
	for (int i = 0; i < n; i++)
		scanf("%d", &in[i]);
	for (int i = 0; i < n; i++)
		scanf("%d", &post[i]);
	return;
}
```


---

# 후기

역시... 손으로 직접 써보면서 어떻게 할까?... 고민해보는게 제일 빠르고 정확하다. 그냥 일단 손으로 써!!!

index도 손으로 직접 test case 생각하면서 손으로 써보니까 빨리 나오잖아
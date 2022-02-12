---
title: "Marathon 1"
excerpt: "Dp 인줄 알았으나... 완전 탐색"

categories:
    - bruteforce
tags:
    - [PS, bruteforce]
last_modified_at: 2022-02-12
---
# Marathon 1 문제 풀이
1. **문제의 이해**
2. 소스코드
3. 후기

백준 문제 링크 [https://www.acmicpc.net/problem/10655](https://www.acmicpc.net/problem/10655)


---


# 1. Marathon 1 문제의 이해

정올 기출 문제이다.

일단 문제를 이해하는데 큰 어려움은 없다. check point하나를 건너뛰어서 갈 수 있는 최소거리를 찾는 것인데 여기서 짚고 넘어가야하는 부분이 거리 측정이 **"Manhattan" distance** 모는 종이에서 재는 x좌표 차이 + y좌표 차이 이기 때문에 건너뛰는게 이득이거나 같을 수 밖에 없다.

>즉, 거리측면에서 봤을 때 건너뛴다고 손해를 보는 경우는 절대 나올 수 없다는 것이다.

그래서 처음에는 거리 합계를 일일이 구하자니 비효율적이라고 생각해서 이건 거리의 합계를 누적시켜서 구하고, 건너 뛰는 부분이 이득인 점만 빼서 건너뛴 거리로 대체한다.

이렇게 생각했다.

물론 메모리를 사용하지 않고 거리의 차가 가장 큰 부분의 index만 기억해서 다시 그 부분의 거리를 구하여 sum에서 조정하면 되겠지만 그냥 메모리를 조금 더 사용해서 그 뒤에 거리 연산은 두번하지 않는다는 마인드로 풀었다.

>그니까 아래에 DP배열을 사용하지 않아도 되는데, 사용하지 않는다면 a-b + b-c VS a - c 거리의 차가 가장 큰 지점의 index만 계속 갱신하다가, sum에서 이 해당 index 부분만 a-b + b-c를 빼고 a-c로 조정해주는 방법으로 풀어야 한다.

이렇게 생각해서 보니까 모든 부분의 거리를 다 연산해서 구해보는 완전 탐색인 bruteforce 알고리즘이 되었다.


처음에는 input의 사이즈도 작지 않아서 이건 무조건 DP겠다 싶었는데, 대충 알고리즘을 구상해보니까 DP는 아니었던.. 그런 문제였다.

---

# 2. 소스코드

```c
#include <stdio.h>

typedef struct {
	int x;
	int y;
}node;

int n; // N >= 3 이므로 무조건 체크포인트는 하나 건너뛰어볼 수 있음.
node point[100000]; 
int dp_1[100000];
int dp_2[100000];

void init();
int distance(int, int);
int find_sum();

int main()
{
	init();
	printf("%d", find_sum());
	return 0;
}

int find_sum()
{
	int temp,index = 0, max = 0;
	for (int i = 2; i < n; i++)
	{
		temp = dp_1[i - 1] + dp_1[i] - dp_2[i];
		if (temp > max) // i-1번째 check point 건너뛰는거임.
		{
			max = temp;
			index = i - 1;
		}
	}
	int sum = 0;
	if (index == 0) // 체크 포인트 건너뛸 필요가 없음.
	{
		for (int i = 1; i < n; i++)
			sum += dp_1[i];
	}
	else
	{
		for (int i = 1; i < index; i++)
			sum += dp_1[i];
		sum += dp_2[index+1];
		for (int i = index + 2; i < n; i++)
			sum += dp_1[i];
	}
	return sum;
}

void init()
{
	scanf("%d", &n);
	for (int i = 0; i < n; i++) // 일단은 입력받고!
		scanf("%d %d", &point[i].x, &point[i].y);
	for (int i = 1; i < n; i++)
		dp_1[i] = distance(i, i - 1);
	for (int i = 2; i < n; i++)
		dp_2[i] = distance(i, i - 2);
	return;
}

int distance(int a, int b)
{
	int temp,dist;
	if ((temp = point[b].x - point[a].x)<0)
		temp *= -1;
	dist = temp;
	if ((temp = point[b].y - point[a].y) < 0)
		temp *= -1;
	return dist + temp;
}
```

---

# 3. 후기

보니까 마라톤 2 문제에서는, K개의 check point를 건너뛰어야 하는데 이건... 이렇게 해서는 절대 못푸는데? 어떻게 풀어야 할지 잘 모르겠다.

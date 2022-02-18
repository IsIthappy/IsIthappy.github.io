---
title: "Social Distancing"
excerpt: "넓은 범위에서 특정 수 찾기! && qsort 함수 사용하기"

categories:
    - BinarySearch
tags:
    - [PS, BinarySearch]
last_modified_at: 2022-02-18 
---
# Social Distancing 문제 풀이
1. 해석 어려웠던 부분
2. **문제의 이해**
3. 소스코드

백준 문제 링크 [https://www.acmicpc.net/problem/18877](https://www.acmicpc.net/problem/18877)


---

# 1. 해석 어려웠던 부분

>Farmer John is worried for the health of his cows after an outbreak of the highly contagious bovine disease COWVID-19.

1. contagious : 전염성의, 전염되는
2. bovine : 소의 // 우둔한, 미련한

존은 그의 소들의 건강을 걱정했다. 매우 전염성이 강한 소의 질병이 발발한 후에


>In order to limit transmission of the disease,

1. transmission : 전염, 전파, 전달

질병의 전염을 제한하기 위해,


---

# 2. Social Distancing 문제의 이해

음... 문제를 처음 봤을 때에는 최대한 소들을 멀찍 멀찍 떨어뜨려야 하니까, greedy를 떠올렸다. 근데 뭐.. 될리가 없지.. 최대 거리가 10^18인데 완전 탐색으로 거리 하나씩 떨어뜨릴거야? 말도 안된다..

그러고나서 예제로 주어진 것을 손으로 직접 시뮬레이션 해봤는데 양 끝에 박고 계속 최대한 절반씩 쪼개는 방향으로 소들을 배치해야 한다는 생각이 들었다.

>근데? 이 절반으로 쪼갰을 때 그 지점에 잔디가 없다면? 어떻게 할 것인가?

여기서 문제가 생겼다.

도저히 방법을 못찾겠어서 살짝 풀이를 커닝했는데... 이 소들을 떨어뜨려놓을 수 있는 거리 D를 이분탐색을 해버리는 것을 보게되었다.

>와우..

즉,

1. 우선 잔디밭이 순서대로 주어진다는 말이 없으므로 정렬한다.
2. 잔디밭 시작, 끝을 이용해 최대거리 D로부터 이분탐색을 진행한다.
3. 해당 거리가 가능하면 오른쪽 범위 다시 탐색
4. 불가능하면 왼쪽 범위 다시 탐색

>왜 꼭 배치할 소의 위치를 이분탐색으로 찾으려고 했을까... 그냥 정답의 범위 그 자체를 이분탐색으로 줄여나가는 방법이 있는데... 심지어 옛날에 드래곤 워리어 문제에서 해봤던 방식이잖아..

이러면 O( log(MAX_d) * (N + M)) 의 시간복잡도로 해결이 가능해진다...


---

# 2. 소스코드

```c
#include <stdio.h>
#include <stdbool.h>
#include <stdlib.h>

typedef struct
{
	long long s;
	long long e;
}node;


node arr[100000]; // 잔디밭
int n, m; // 소 마릿수, 잔디밭 개수

void init();
int cmp(const void*, const void*);

long long find_dist();
bool check(long long);

int main()
{
	init();
	find_dist(); // 1 ~ 잔디밭 최대거리까지 탐색
	printf("%lld", find_ans());
	return 0;
}

long long find_dist()
{
	long long ans = 0;
	long long left = 1, right = arr[m - 1].e - arr[0].s, middle;
	while (left <= right)
	{
		middle = (left + right) / 2;
		if (check(middle))
		{
			ans = middle;
			left = middle + 1;
		}
		else
			right = middle - 1;
	}
	return ans;
}

bool check(long long d)
{
	int index = 0, count = 0;
	long long cur = arr[0].s;
	while (index < m)
	{
		if (cur >= arr[index].s && cur <= arr[index].e)
		{
			count++;
			cur += d;
		}
		else if (arr[index].s > cur)
		{
			count++;
			cur = arr[index].s + d;
		}
		else // 배치해야 하는 소의 위치보다 작다면? 잔디밭 건너뛰어야지
			index++;
		if (count == n)
			return true;
	}
	return false;
}

void init()
{
	scanf("%d %d", &n, &m);
	for (int i = 0; i < m; i++)
		scanf("%lld %lld", &arr[i].s, &arr[i].e);
	qsort(arr, m, sizeof(node), cmp);
	return;
}

int cmp(const void* a, const void* b)
{
	node temp1 = *(node*)a;
	node temp2 = *(node*)b;
	if (temp1.s < temp2.s)
		return -1;
	if (temp1.s > temp2.s)
		return 1;
	return 0;
}
```


---
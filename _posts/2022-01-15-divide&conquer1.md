---
title: "행렬 제곱"
excerpt: "DP처럼 생각할 수 있지만 DP랑은 차이가 있는 알고리즘"

categories:
    - Divide&Conquer
tags:
    - [PS, Divide&Conquer]
last_modified_at: 2022-01-15 
---
# 행렬 제곱 문제 풀이
1. **문제의 이해**
2. 소스코드
3. 후기

백준 문제 링크 [https://www.acmicpc.net/problem/10830](https://www.acmicpc.net/problem/10830)


---

# 1. 행렬 제곱 문제의 이해

우선 이 문제를 처음 봤을 때 행렬 제곱을 바로 할 수 있는 공식을 찾아봤지만 그건 없었고

Top - down DP 형식으로 위에서부터 내려오며 이미 계산된 적이 있는 행렬 값이면 중복 호출을 막아주며 DP로 풀려 했었다.

>하지만 제곱수가 100,000,000,000 이나 된다....

메모리에 올려서 계산된 결과값들을 기억하게 한다면 메모리가 터질 수 밖에 없다... 메모리에 저장시켜서 풀면 안되는 문제이고 중복 호출이 없는 방향으로 알고리즘을 구상해야 한다는 것을 깨닫게 되었다.

그러고 질문 게시판을 조금 커닝해보니 분할정복으로 풀었다고 되어있길래 생각해보니까...

>지수를 절반씩 계속 뚝뚝 떼어가며 진행하니 중복 호출이 있을 수가 없다는 것을 알게 되었다.........

250 = 125 + 125

125 = 124 + 1

124 = 62 + 62

중복 호출이 있을 수가 없다!. 메모리에 올릴 필요없이 문제를 분할하고 아래에서부터 차곡차곡 쌓아올려오는 전형적인 분할정복 문제였던 것이다.

---

# 2. 소스코드

```c
#include <stdio.h>

int ans[5][5]; // 최종 답이 저장될 배열
int temp[5][5];
int arr[5][5]; // 원본
int n;
long long b;

void init();
void divide(long long);
void mul_ans();
void mul_1();

int main()
{
	init();
	divide(b);
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
			printf("%d ", ans[i][j]%1000);
		printf("\n");
	}
	return 0;
}

void divide(long long cur) // 손으로 차근 차근 써보면 중복 호출이 없다는 것을 알 수 있습니다.
{
	if (cur == 1) // base condition
	{
		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < n; j++)
				ans[i][j] = arr[i][j];
		}
		return;
	}
	if (cur % 2 == 0)
	{
		divide(cur / 2);
		mul_ans();
	}
	else
	{
		divide(cur / 2);
		mul_ans();
		mul_1();
	}
	return;
}

void mul_ans()
{
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
			temp[i][j] = 0;
	}
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
		{
			for (int k = 0; k < n; k++)
			{
				temp[i][j] += ans[i][k] * ans[k][j];
				temp[i][j] %= 1000;
			}
		}
	}
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
			ans[i][j] = temp[i][j];
	}
	return;
}

void mul_1()
{
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
			temp[i][j] = 0;
	}
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
		{
			for (int k = 0; k < n; k++)
			{
				temp[i][j] += ans[i][k] * arr[k][j];
				temp[i][j] %= 1000;
			}
		}
	}
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
			ans[i][j] = temp[i][j];
	}
	return;
}

void init()
{
	scanf("%d %lld", &n, &b);
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
			scanf("%d", &arr[i][j]);
	}
	return;
}
```


---

# 후기

음... DP랑 분할정복 모두 큰 문제를 작은 문제로 쪼개서 생각한다는 점에서 출발 선상이 비슷한 느낌인데 조금 더 여러 문제를 더 접해보면 분명한 차이점을 알게 될 것 같다.

일단 내가 지금 느낀 점으로는

DP : 완전 탐색 기반, 메모리에 이전 state들 모두 저장

분할 정복 : 중복 호출 없음. 작은 문제들을 합쳐가며 현재 state를 완성해 나감.

이렇게 느꼈다.
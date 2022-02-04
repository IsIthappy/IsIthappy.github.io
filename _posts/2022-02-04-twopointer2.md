---
title: "소수의 연속합(Sum of Consecutive Prime Numbers)"
excerpt: "에라토스테네스의 체 & two pointer"

categories:
    - two_pointer
tags:
    - [PS, Math ,two_pointer]
last_modified_at: 2022-02-04
---
# 부분합
1. **Sum of Consecutive Prime Numbers 문제의 이해**
2. 소스코드
3. **후기**

백준 문제 링크 [https://www.acmicpc.net/problem/1644](https://www.acmicpc.net/problem/1644)


---

#  Sum of Consecutive Prime Numbers

뭐... 내가 최근에 에라토스테네스의 체, two pointer 부분 문제를 풀었어가지고 이 문제는 그냥 보자마자 두개 다 써야하는 문제구나 라는 생각이 바로 들었다.

문제를 이해해보자면 [1 , 4,000,000] 범위내의 N이 주어지는데 N보다 작은 소수 수열에서 연속합을 통해 N을 몇가지의 방법으로 만들 수 있는지를 물어보는 문제였다.

1. 에라토스테네스의 체를 사용해서 N까지의 소수 배열을 구한다.
2. consecutive sum의 size가 고정적이지 않으니 two pointer를 이용한다.
3. n을 만들 수 있는 연속합을 카운트 해준다.

뭐... 이해가 안되는 부분이 하나도 없는 문제였다. 어떻게 문제가 나와도 이렇게 딱 공부한 두 알고리즘이 합쳐지는지 타이밍 좋네

>N까지의 최대 몇개의 소수가 존재하는지는 직접 한번 돌려서 카운트 해봤다.

---

# 소스코드

```c
#include <stdio.h>
#include <stdbool.h>

bool visit[4000001]; // int로 방문체크 했으면 이거 터졌음.

int arr[300000]; // 소수를 직접 count 해봤더니 4,000,000까지 283,146 개의 소수가 존재함.
int n,k; // 입력으로 주어진 n까지의 소수 개수 k

void filter(int); // 에라토스테네스의 체를 통해 소수 배열 구하기
int find_sum(); // N을 만들 수 있는 연속합 count하기

int main()
{
	scanf("%d", &n);
	if (n == 1) // 1이면 소수 판별을 할 필요조차 없음.
	{
		printf("0");
		return 0;
	}
	filter(n);
	printf("%d", find_sum());
	return 0;
}

int find_sum()
{
	int count = 0, sum = 0, left = 0, right = -1;
	while (right < k)
	{
		if (right == k - 1 && sum < n) // 더 이상 수열을 확장할 수 없는데, 만들고자 하는 수보다 작을 때 가지치기
			break;

		if (sum < n) // 오른쪽 포인터 무빙
			sum += arr[++right];
		else if (sum > n) // 왼쪽 포인터 무빙
			sum -= arr[left++];
		else // sum == n 이므로 count 해주고 왼쪽 포인터 무빙
		{
			count++;
			sum -= arr[left++];
			// 사실 오른쪽 포인터 무빙을 해줘도 되지만, n == k-1 이었다면 sum+=arr[++right] 에러뜬다..
		}
	}
	return count;
}

void filter(int target) // 에라토스테네스의 체
{
	for (int i = 2; i <= target; i++)
	{
		if (visit[i] == false) // 가장 작은 소수 선택
		{
			arr[k++] = i; // 소수 배열에 저장 잘 해줘야지.
			int cur = i, order = 2;
			while (cur <= target)
			{
				visit[cur] = true;
				cur = i * order;
				order++;
			}
		}
	}
	return;
}
```

---


# 후기

흠.. 역시 수학과 관련된 부분이나 특정 알고리즘 문제는 알고나서 풀면 개쉽다.
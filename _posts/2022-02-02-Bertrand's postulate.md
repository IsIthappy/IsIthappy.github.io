---
title: "베르트랑 공준(다국어 문제)"
excerpt: "소수 판별 알고리즘 & 에라토스테네스의 체"

categories:
    - Math
tags:
    - [PS, Math]
last_modified_at: 2022-02-02
---
# Σ
1. **Σ 문제의 이해**
2. 소스코드
3. **후기**

백준 문제 링크 [https://www.acmicpc.net/problem/4948](https://www.acmicpc.net/problem/4948)


---

#  베르트랑 공준 두가지 풀이

우선 베르트랑 공준이란 어떤 자연수 n이 주어졌을때, n과 2n 사이에는 반드시 소수 한개가 존재한다는 정리이다. 베르트랑이 증명하는 것은 실패했지만 후대에 수학자들이 증명을 해뒀다.

우선 맨 처음에는 문제 그대로, n 과 2n 사이 for문을 돌려서 해당 숫자가 prime number인지 체크한 뒤 count를 통해 풀었다.

> prime number 판별은 2 ~ root(n) 까지 직접 나눠보는 방법이 있다.

소수 판별법에 이것보다 더 효율적인 알고리즘이 있는지는 잘 모르겠다.

---

```c
#include <stdio.h>
#include <math.h>
#include <stdbool.h>

bool check_prime(int); // 2~ root(n) 까지 나눠지는 수 있나 확인을 통한 prime number 판별

int main()
{
	int n,count;
	scanf("%d", &n);
	while (n != 0)
	{
		count = 0;
		for (int i = n + 1; i <= 2 * n; i++)
		{
			if (check_prime(i))
				count++;
		}
		printf("%d\n", count);
		scanf("%d", &n);
	}
	return 0;
}

bool check_prime(int cur)
{
	double end = sqrt(cur);
	for (int i = 2; i <= end; i++)
	{
		if (cur % i == 0)
			return false;
	}
	return true;
}
```

근데 소수 판별하는 법을 더 알아보던 중 에라토스테네스의 체라는 방법을 알게 되었다. 이 방법은 모든 자연수 (N>1)은 소수들의 곱으로 표현할 수 있고 이를 이용해, 2 ~ N까지 소수를 찾는 방법이다.

>주의해야 할 점은 소인수분해를 해주는 것이 아니라, [2 ~ N] 범위 내에서 prime number를 찾는 알고리즘이다.

어떻게 하는 것이냐?

1. N까지 2의 배수들을 다 삭제한다.
2. 남아있는 수 중에서 가장 작은 숫자를 선택 -> 3, 3의 배수들을 다 삭제한다.
3. 5의 배수들을 다 삭제한다.
4. ...

위 과정을 남아있는 수가 없을 때까지 반복하면 N까지의 prime number들을 다 구할 수 있다.

>남아있는 수 중에서 가장 작은 수는 무조건 prime number일 수 밖에 없다. 한번 써서 직접 해봐라....

에라토스테네스의 체도 수학자들이 증명해둔 방법이다.

---

```c
#include <stdio.h>
#include <stdbool.h>

bool visit[250000]; // 체로 걸러내지는 수 체크

int count_prime(int);

int main()
{
	int n;
	scanf("%d", &n);
	while (n != 0)
	{
		for (int i = 1; i <= 2 * n; i++)
			visit[i] = false;
		printf("%d\n", count_prime(n));
		scanf("%d", &n);
	}
	return 0;
}

int count_prime(int start) // 에라토스테네스의 체를 통해 2n 까지 소수 구해내기
{
	int count = 0,end = start*2;
	for (int i = 2; i <= end; i++)
	{
		if (!visit[i])
		{
			if (i > start) // 소수는 맞지만 n보다는 클 때 카운트를 해줘야함.
				count++;
			int cur = i, order = 2;
			while (cur <= end) //선택된 소수의 배수 다 걸러내기
			{
				if (!visit[cur])
					visit[cur] = true;
				cur = i * order;
				order++;
			}
		}
	}
	return count;
}
```



---


# 후기

주어진 범위 내에 소수들을 구해내는 방법이 있다는 것을 처음 알았다...

신기하네.. 물론 이 문제의 경우에는 test case 범위가 작아서 2 ~ root(n)까지 직접 나눠보는 소수 판별법으로 범위 내의 소수를 찾아내도 되는 문제였긴 하지만

에라토스테네스의 체 방법을 사용하니까 걸리는 시간이 확 줄었다. 이런 짜잘 짜잘한 알고리즘 잘 알아두자!
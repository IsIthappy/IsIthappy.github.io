---
title: "부분합(다국어)"
excerpt: "scanf로 파일 끝까지(EOF) 입력받기"

categories:
    - two_pointer
tags:
    - [PS, two_pointer]
last_modified_at: 2022-02-03
---
# 부분합
1. **부분합 문제의 이해**
2. 소스코드
3. **후기**

백준 문제 링크 [https://www.acmicpc.net/problem/1806](https://www.acmicpc.net/problem/1806)


---

#  부분합

일단 뭐... 문제 자체가 이해하는데 크게 어렵진 않았다. 영어로 읽어도 무난하게 이해가 갈 정도의 난이도였다. 연속된 수들을 더해서 문제에서 주어진 합을 넘겨야 하는데 내가 생각한 흐름은 이렇다.

>이런 문제는 test case 범위를 확인해서 overflow 체크하는 것은 기본이다.

1. 한번에 훑어서 끝내야 한다? -> stack이네
2. stack으로 합들을 쌓아가려면 stack에 들어있는 것들을 다~ 빼서 하나더 합해주는 갱신 과정이 필요하네???? -> 선입선출이 필요할테니까 queue를 쓸 까?
3. 근데 이런 방식으로 합들을 계속 쌓아가면 O(N^2)의 시간복잡도 아닌가?
4. 슬라이딩 윈도우를 사용하기엔 수열의 사이즈가 유동적인데???

이렇게 생각이 흘러가서 결국, 질문게시판을 한번 커닝했고, 제목에서 투 포인터를 발견했다.........

>투포인터를 사용해서 풀면.. 합들을 차곡차곡 쌓아가는 미련한 짓은 하지 않아도 되는 것이었다.


이렇게 해서 푸는데 원문 문제를 잘 보면 test case가 여러개 주어질 수 있다고 한다. 따라서 입력을 받는 부분에서 eof까지 받고 프로그램을 종료시키는 과정이 필요한데, 여기서 멍청하게 while(scanf("%d %d",&n,&s)) 이렇게만 써서 시간초과가 났었다.

>scanf가 EOF를 읽으면, EOF 혹은 -1을 return 합니다.


---

# 소스코드

```c
#include <stdio.h>

int n, s;
int arr[100000]; // 수열을 저장할 배열

int ans = 100001; // 최소 수열의 길이를 저장할 변수

void run();

int main()
{
	while (scanf("%d %d", &n, &s)!= EOF) // 이러면 EOF까지 입력을 받는다.
	{
		ans = 100001;
		run();
		if (ans == 100001)
			printf("0\n");
		else
			printf("%d\n", ans);
	}
	return 0;
}

void run()
{
	for (int i = 0; i < n; i++)// 수열 입력 받아주고
		scanf("%d", &arr[i]);
	int sum = 0, left = 0, right = -1;
	while (right < n) // two pointer를 이용해서 연속된 수열의 합의 크기를 체크하는 부분
	{
		if (right == n - 1 && sum < s)
			break;
		if (sum < s)
			sum += arr[++right];
		else if (sum >= s)
		{
			if (ans > right - left + 1)
				ans = right - left + 1;
			sum -= arr[left++];
		}
	}
	return;
}
```

---


# 후기

슬라이딩 윈도우 기법은 연속된 수열의 크기가 고정적일 때 사용하는 기법이고 two pointer는 수열의 크기가 유동적일 때 사용하는 기법이다!

>two pointer... 오늘도 새롭게 하나 알아간다.
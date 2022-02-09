---
title: "class 와 struct의 차이점"
excerpt: "C++에서의 struct는 C에서의 struct와 차이가 있다."

categories:
    - Cpp
tags:
    - [Note, Cpp]
last_modified_at: 2022-02-09 
---

# C++에서의 class와 struct의 차이는?

>일단 기본적으로 C의 구조체와 C++의 구조체는 조금 다르다.

C에서의 struct는 연관있는 데이터를 묶는 기능만 가지고 있지만, C++에서의 struct는 class의 동작방식과 일치하기 때문에

>함수 선언 및 정의, 생성자 및 소멸마 선언 및 정의, 상속 구조 등등을 사용할 수 있다.


```c++
struct node{
    int x;
    int y;
};
```

이게 지금껏 사용해온 구조체이다.

여기서 C++의 class를 사용하고 싶다면?

```c++
class node{
    int x;
    int y;
}
```

그냥 struct에서 class로 바꿔써주기만 하면 된다. C++에서는 struct(구조체)가 class의 일종이기 때문이다.

>c++에서의 class와 구조체의 차이는 멤버의 접근 지시자의 차이가 있다.

>또한 class 사용시 typedef 없이 그냥 바로 class의 이름으로 선언이 가능하다.

struct node 변수이름 이렇게 안쓰고 node 변수이름이 가능하다는 소리)

struct(구조체)는 모든 멤버의 접근 지시자가 항상 public이고

>class는 멤버별로 각각 다른 접근 지시자를 정해줄 수 있다. (default 값으로는 private 설정이 된다.)

1. public : 어디에서나 접근 가능
2. protected : 상속관계에 있을 때, 유도 class에서의 접근 허용
3. private : class 내에 정의된 함수에서만 접근 허용

즉, 위에 예시로 선언한 class node의 멤버들은 private 처리가 되어있어, class 내부 함수들이 아니라면 접근이 불가한 변수들이다.


```c++
class point
{
private:
    int x;
    int y;
public:
    void init()
    {
        x = 0;
        y = 0;
    }      
}
```

이런식으로 사용할 수 있다.

그니까 요약하자면, C에서의 구조체와 C++에서의 구조체는 약간의 차이가 있으며 

C++에서는 구조체와 class 역할의 차이는 없다.

둘 다 data들을 묶는 역할을 하니까, 하지만 구조체는 접근제어 지시자가 모두 public이고, 사용자가 따로 지정해줄 수 없다.

하지만 class는 사용자가 지정해 줄 수 있으며, default값은 private이라는 것이다.

---
title: "namespace란?"
excerpt: "using namespace std 왜 하는건데? 키워드 & 식별자란"

categories:
    - Cpp
tags:
    - [Note, Cpp]
last_modified_at: 2022-02-09 
---

# namespace란 무엇인가?

열혈 윤성우 C++을 읽고 간단하게 정리해보는 글

namespace에 관해 알아보기 앞서 용어 키워드 & 식별자 부터 알아두고 갑시다.

>키워드(keyword) : 언어의 예약어(int, cahr, if, while, do, class 등등)

>식별자(identifire) : 변수, 함수 및 레이블의 사용자 정의 이름


namespace라는 개념의 등장 배경은 **식별자들의 충돌** 즉, (함수와 변수)의 이름들이 중복되어 충돌하는 것을 막기 위해 등장함.

```cpp
#include <iostream>
 
void print() {
    std::cout << "a" << std::endl;
}
 
void print() {
    std::cout << "b" << std::endl;
}
 
int main(void)
{
    print();
    return 0;
}

```

이렇게 함수의 이름이 겹쳐버리면 충돌이 난다. 이를 namespace로 묶어서 구분지어 주는 것이다.


```cpp
#include <iostream>

namespace A{
    void print() 
    {
        std::cout << "a" << std::endl;
    }
}

namespace B{
    void print() 
    {
        std::cout << "b" << std::endl;
    }
}
 
int main(void)
{
    A::print();
    B::print();
    return 0;
}

```

A::print();

B::print();

>이렇게 :: 라는 범위지정 연산자를 사용함.

매번 이렇게 연산자를 붙여서 사용하는 것이 귀찮기 때문에 using std::cout; 이렇게 선언해두면 이 선언문이 있는 scope 내에서는 더 이상 범위지정 연산자를 붙여주지 않아도, cout을 호출하면 std namespace에 있는 cout을 호출했구나라고 컴파일러가 알아먹는다.

```cpp
#include <iostream>

namespace A{
    void print() 
    {
        std::cout << "a" << std::endl;
    }
}

namespace B{
    void print() 
    {
        std::cout << "b" << std::endl;
    }
}
 
int main(void) // 이러면 main함수 내에서는 A 이름공간에 있는 print만 호출된다.
{
    using A::print;
    print();
    print();
    return 0;
}

```

namespace는 필요에 따라 지정해주는 것이며, 지정해주지 않아도 별 문제는 없지만 식별자의 이름이 같을 수 있는 대규모 프로그램의 경우 충돌이 날 수 있기 때문에 사용해준다.


>그럼 나는 왜 using namespace std;를 전역으로 선언해두는가?

대부분의 C++에서 제공하는 표준 라이브러리에 들어있는 기능들은 namespace std에 선언되어 있다.

따라서 이 기능들을 사용하기 위해서는

1. 매번 std:: 로 범위 지정을 해준다.
2. 해당 기능을 사용하는 scope 내에서 using std::식별자;  이렇게 선언해준다.

이 과정이 필요한데, **번거롭다 상당히**.


따라서 using namespace std;를 전역에 선언해서 프로그램 어디에서나 std에 들어있는 식별자들을 범위 지정 연산자를 붙이지 않고서도 호출이 가능하게 하는 것이다.

위에 코드에서 전역으로 using namespace std; 를 선언해두면 std::를 다~ 안써줘도 된다.


대신 이러면 내가 정의한 변수나, 함수가 std에 들어있는 것과 이름이 겹치면 충돌이 나기 때문에 프로그램이 커질 경우 위험한 방식이다.
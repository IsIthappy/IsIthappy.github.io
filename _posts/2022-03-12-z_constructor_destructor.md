---
title: "생성자와 소멸자"
excerpt: "대충 간단하게 문법만 스윽 알아봅시당"

categories:
    - obejective-oriented
tags:
    - [obejective-oriented, Cpp]
last_modified_at: 2022-03-12
---

# class 생성자(Constructor)와 소멸자(Destructor)에 대해서


우선 뒤에서 계속 나오겠지만 간단하게 class란

```c++
#include <iostream>

using namespace std;

class knight
{
public:
	void move(int y, int x)
    {
        _posY += y;
        _PosX += x;
        this->_posX += x;
    }
	void attack();
	void die();
// 여기서 _posx += x // this -> _PostX += x; 는 같은 표현이다.
// 멤버 함수를 main영역에서 호출할 때, 해당 class에 대한 주소를 넘겨줘서 멤버 함수에서 어떤 class의 멤버 변수인 지 알 수 있는 것이다.
public:
	int _hp;
	int _attack;
	int _posY;
	int _posX;
};

int main()
{
	knight K1;
	K1._hp = 0;
	K1._attack = 0;
	K1.move(50, 50); // 이런식으로 호출 됐을 때, move에 K1 class의 pointer가 전달되고 그걸로 멤버 변수 접근하고 하는 것이다. 
	return 0;
}
```

이런식으로 멤버 변수와 멤버 함수를 가지는 객체? 덩어리?를 class라고 한다.

이때 class에 소속된 멤버 함수들 중에서 class가 생성될 때 호출되는 것이 생성자(여러개 생성 가능), 소멸될 때 호출되는 것이 소멸자(무조건 하나)라고 한다.

# 1. 생성자(Constructor)

>우선 생성자의 문법은 반환형 없이 class의 이름 그대로 가져와서 쓰면 된다. 또한, 세미콜론도 안붙여도 되고 범위 scope인 중괄호는 무조건 써야 하지만 안에 내용을 아무것도 넣지 않아도 에러는 안뜬다.

여기에 암시적 생성자와 명시적 생성자가 있는데 우선 암시적 생성자부터 보면 위에 class의 대한 예시에서는 생성자가 없지만 knight k1;를 통해서 class 선언이 가능하다.

이는 암시적 생성자 덕분인데 생성자를 user가 명시적으로 만들어두지 않으면 컴파일러가 default로 모든 멤버 변수들을 0으로 초기화하는 생성자를 만들어서 호출한다.


하지만 사용자가 어떠한 생성자든 직접 명시적으로 만들었다면 이 default 생성자는 만들어지지 않으며 사용자가 직접 정의한 생성자를 class를 새롭게 선언할 때 사용해야 한다.(명시적으로 생성자 정의하면 class 선언할 때 무조건 생성자와 같이 호출해야 한다.)


## (1) [1] 기본 생성자 --- 인자가 없음.

```c++
class knight
{
public:
	knight() // 반환형 없고 함수의 이름 그대로 가져온 모습. 기본 생성자라 인자는 없음.
	{
		_hp = 0;
		_attack = 0;
		...
	}
public:
    멤버 변수들 주르륵
}

int main()
{
    knight k1();
    knight k2(); // 기본 생성자가 호출되어 class가 선언된다.
}
```

## (2) 복사 생성자 - 자기 자신의 class 참조 타입을 인자로 받아서 멤버들 복사하는 생성자다.

```c++
class knight
{
	knight(const knight& temp) // 복사 생성자도 명시적으로 선언하지 않으면 모든 멤버 단순 복사하는 default 복사 생성자가 호출된다.
    {}
    //평소에는 구조체 복사처럼 진행해도 되지만 멤버 중 동적할당 같은 것이 있다면 직접 구현을 해줘야 한다.
}
	int main
	{
		knight k2(k1); // k2를 k1의 멤버 변수 그대로 옮겨와서 생성 (복사 생성사 호출)
		knight k2 = k1; // k2(k1)과 똑같은 문법임. 이것도 마찬가지로 복사 생성자 호출임

		knight k2; // defalut생성자 혹은 기본 생성자 호출
		k2 = k1; // 복사
		// class간의 복사와(C 구조체 복사 생각해봐), 복사 생성자 호출은 다른 개념이다.
	}
```


## (3) 기타 생성자 -말 그대로 이것저것 기타 생성자 이 부분에 명시적 형변환 생성자(explicit)도 있음.

```c++
knight class
{
public:
	knight(int hp)
	{
		_hp = hp;
		_attack = 0;
		...
	}
    knight(int hp, int a)
public:
    int _hp;
    int_attack;
}

int main()
{
    knight k1(50);
    knight k2(50,100);
}
```
생성자는 오버로딩이 가능하기 때문에 인자에 따라서 다른 생성자가 호출될 수 있다.



// 소멸자의 문법은 생성자와 비슷한데 class 이름 앞에 '~'를 붙이면 된다.



# 2. 소멸자(Destructor)

얘는 오버로딩이 안되는(하나밖에 없는) 친구이기 때문에 종류가 그냥 소멸자 그 자체로 하나이다.

>문법으로는 생성자와 비슷하게 반환형이 필요없고, class 이름 앞에 '~'을 붙여서 사용한다.

```c++
class knight
{
public:
    ~knight()
    {}
public:
    int _hp;
    int _attack;
}
```

이런식으로 사용하고 객체가 소멸할 때 (동적할당이 해제되거나, 지역변수 scope가 끝나거나 프로그램이 끝나거나) 자동으로 호출되는 함수이다.





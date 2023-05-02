---
layout: single
title: "[C++] 클래스."
categories: Cpp
tag: [Cpp]
toc: true
toc_sticky: true
---

## C++ 클래스에 대한 기본개념 정리.  

### 클래스란?  
C의 구조체에서 확장된 C++ 구조체의 또 다른 이름입니다.  
변수 뿐 아니라 함수도 포함시킬 수 있습니다.  

### 구조체와 클래스의 차이점은?  
가장 큰 차이점은 접근지정자입니다.  

구조체의 기본 접근지정자는 **public**이며
클래스의 기본 접근지정자는 **private**입니다.

### 클래스의 기본 구조.

```c++
#include <iostream>

using namespace std;

class MyClass {
private:
	int num;
public:
	MyClass(int _num); // 생성자 선언
	~MyClass(); // 소멸자 선언
	void add(); // 멤버함수 선언
};

MyClass::MyClass(int _num) { // 생성자 정의
	num = _num;
	cout << "생성자 호출" << endl;
	cout << "num = " << num << endl;
}
MyClass::~MyClass() { // 소멸자 정의
	cout << "소멸자 호출" << endl;
}

void MyClass::add() { // 멤버함수 정의
	num *= 2;
	cout << "add 함수 호출" << endl;
	cout << "num = " << num << endl;
}

int main(void) {

	MyClass test(10);
	test.add();

	MyClass test2(500);
	test2.add();

	return 0;
}
```
위 코드를 실행시켜 보면 결과는 아래와 같습니다.  

![image-20230502213718253](../../images/2023-05-02-CppClassBase/image-20230502213718253.png)

test, test2의 생성자가 순서대로 호출되며 소멸자는 반대 순서로 호출됩니다.  

### 오버로딩.  
생성자의 특징 중 하나는 오버로딩이 가능하다는 점입니다.  
넘겨주는 인자의 형식에 따라서 그 수에 맞는 생성자가 호출됩니다.  

```c++
#include <iostream>

using namespace std;

class ExConstructor {
public:
	ExConstructor() {
		cout << "ExConstructor() called!" << endl;
	}

	ExConstructor(int a) {
		cout << "ExConstructor(int a) called!" << endl;
	}

	ExConstructor(int a, int b) {
		cout << "ExConstructor(int a, int b) called!" << endl;
	}
};

int main() {
	ExConstructor ec1;
	ExConstructor ec2(10);
	ExConstructor ec3(20, 10);

	return 0;
}
```

위 코드를 실행시켜 보면 결과는 아래와 같습니다.  

![image-20230502214944240](../../images/2023-05-02-CppClassBase/image-20230502214944240.png)

1. 넘겨주는 인자가 없을 때.  
2. 넘겨주는 인자가 1개일 때.  
3. 넘겨주는 인자가 2개일 때.  

각각 다른 생성자 함수가 호출된 것을 확인할 수 있습니다.  

### 복사 생성자 (Copy Constructor)  
복사생성자에 대한 이야기를 시작하기 전에 C와 C++의 초기화 스타일에 대해 한 번 짚고 넘어가겠습니다.  

```c++
int a(50) // C++ 스타일 초기화
int b = 40; // C 스타일 초기화
```
C++ 에서는 '=' 연산자를 사용하지 않고 괄호로 인자값을 집어넣는 형식으로 변수에 대한 초기화를 진행할 수 있습니다.  
이걸 굳이 설명드린 까닭은 뒤에서 언급되기 때문입니다.  


클래스의 복사생성자는 일반 변수와 사용방법은 동일하지만 개념은 조금 다릅니다.  
일반 변수의 경우 아래와 같은 코드를 실행시켰을 때  
```c++
int num1 = 10;
int num2 = num1;
num1 = 0;
std::cout << num2 << std::endl;
```
num2의 값은 10이 나옵니다.  
포인터를 참조하는 것이 아닌 값을 대입하는 형식이기 때문인데요.  
클래스는 포인터를 참조하는 형식입니다 마저 살펴보겠습니다.  
```c++
#include <iostream>

using namespace std;

class MyClass {
private:
	int num1;
	int num2;
public:
	MyClass(int a, int b) {
		num1 = a;
		num2 = b;
	}
	void ShowData() {
		cout << "num1: " << num1 << " num2: " << num2 << endl;
	}
};

int main() {
	MyClass mc1(50, 40);
	MyClass mc2 = mc1;

	mc2.ShowData();

	return 0;
}
```
위 코드를 실행시켜 보면 결과는 아래와 같습니다.  

![image-20230502215857600](../../images/2023-05-02-CppClassBase/image-20230502215857600.png)

m2를 선언하면서 m1의 값으로 초기화 시켰습니다.  
그리고 m2의 데이터를 확인해보니 m1과 같은 값이 출력되었습니다.  

클래스 내에 묵시적으로 삽입되어있는 복사 생성자가 실행된 것인데요.  
이것을 **얕은복사**라고 부릅니다.  

그런데 **얕은복사**에는 문제점이 하나 있습니다.  
다음 코드를 보겠습니다.  

### 얕은 복사의 문제점  

```c++
// 얕은 복사의 문제점 예시.

#define _CRT_SECURE_NO_WARNINGS
#include <iostream>

using namespace std;

class MyClass {
private:
	char* str;
public:
	MyClass(const char* aStr) { // 생성자 선언 및 정의.
		str = new char[strlen(aStr) + 1]; // 동적할당.
		strcpy(str, aStr);
	}
	~MyClass() { // 소멸자 선언 및 정의.
		delete[]str; // 메모리 해제.
		cout << "~MyClass() called!" << endl;
	}
	void ShowData() { // 멤버 함수 선언 및 정의.
		cout << "str: " << str << endl;
	}
};

int main() {
	MyClass mc1("MyClass!");
	MyClass mc2 = mc1;

	mc1.ShowData();
	mc2.ShowData();
	return 0;
}

/*
mc2의 디폴트 소멸자가 이미 동적 할당된 메모리를 해제하였는데
mc1에서 또 한번 해제하려고 하니 오류가 발생한다.

얕은 복사는 메모리를 할당하지 않고 포인터만 복사하기 때문.
*/
```

위 코드를 실행시켜 보면 결과는 아래와 같습니다.  

![image-20230502221459398](../../images/2023-05-02-CppClassBase/image-20230502221459398.png)

"~MyClass() called!"가 한 번만 출력되고 오류가 발생합니다.  
이는 m2 선언 시 m1의 값으로 초기화 시켜주었지만 일반 변수와 달리 <u>값을 대입하는 것이 아닌, 포인터를 참조</u>하는 형태이기 때문입니다.  

char* str 멤버변수는 m2 소멸자에 의해 동적할당 된 메모리가 해제된 상태에서 m1 소멸자로 다시 한 번 메모리를 해제하려고 하고있습니다.  
해제할 메모리가 없는데 메모리를 해제하라고 하니 컴퓨터는 오류를 출력한 것입니다.  

해결 방법은 **깊은 복사 (Deep Copy)**에 있습니다.
다음은 깊은 복사의 코드입니다.  

### 깊은 복사 (Deep Copy)

```c++
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>

using namespace std;

class MyClass {
private:
	char* str;
public:
	MyClass(const char* aStr) {
		str = new char[strlen(aStr) + 1];
		strcpy(str, aStr);
	}
	MyClass(const MyClass& mc) {
		str = new char[strlen(mc.str) + 1];
		strcpy(str, mc.str);
	}
	~MyClass() {
		delete[]str;
		cout << "~MyClass() called!" << endl;
	}
	void ShowData() {
		cout << "str: " << str << endl;
	}
};

int main() {
	MyClass mc1("MyClass!");
	MyClass mc2 = mc1;

	mc1.ShowData();
	mc2.ShowData();
	return 0;
}
```

위 코드를 실행시켜 보면 결과는 아래와 같습니다.  

![image-20230502222244076](../../images/2023-05-02-CppClassBase/image-20230502222244076.png)

```c++
MyClass mc2 = mc1;
```
이 문단은 C++ 스타일 초기화로 변형되어
```c++
MyClass mc2(mc1);
```
이런식으로 변형됩니다.  

그러면 생성자의 오버로딩에 의해 
```c++
MyClass(const MyClass& mc) {
		str = new char[strlen(mc.str) + 1];
		strcpy(str, mc.str);
	}
```
생성자가 실행될 것이고, 메모리 공간 할당 후 문자열을 복사합니다.  
그 다음에는 할당된 메모리의 주소를 sre에 저장합니다.  
이렇게 깊은 복사를 통해 오류없이 생성자와 소멸자가 실행될 수 있도록 할 수 있습니다.

출처 : https://blog.hexabrain.net/168 (2023.05.02)  
[출처](https://blog.hexabrain.net/168){: .btn .btn--danger}
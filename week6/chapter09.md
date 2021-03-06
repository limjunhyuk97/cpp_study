# 가상의 원리(가상함수, 가상상속)와 다중상속

## 01. 객체와 멤버함수의 존재
  - 정리
    - fact : 객체 안에는 멤버함수가 존재하지 않는다
    - 현실 : 객체 내에 멤버함수가 존재한다고 여기는 것이 이롭다. (객체지향적 논리로는 그렇게 인식하는 것이 옳다.)
  - 원리
    - 객체 생성시 멤버변수는 객체 내에 존재한다.
    - 객체 생성시 멤버함수는 별도의 메모리 공간에 위치해 있게 되고, 그 멤버함수를 포함하는 클래스의 객체들이 멤버함수 호출 시에 이를 공유한다. 
```c
// C 구조체로 표현한다면 이런 느낌이다.

#include <stdio.h>
typedef struct Data{
  int num;
  void (*ShowData)(Data *);
  void (*Add)(Data *, int adder);
  // 함수 포인터 : 함수를 저장하는 포인터이다. 함수를 배열이나 구조체에 넣거나, 함수 자체를 함수의 매개변수나 반환값으로 가져오기 위해 사용한다.
  // 반환형의 자료형과, 매개변수의 자료형과 매개변수의 수가 일치해야 함수포인터에 함수를 할당할 수 있다.
} 

void ShowData(Data *ptr) { printf("Data : %d", ptr->num); }
void Add(Data *ptr, int adder) { ptr->num += adder; }

int main(void){
  Data obj1 = {5, ShowData, Add};
  Data obj2 = {13, ShowData, Add};
  // 전역변수에서 정의된 함수 주소를 구조체 포인터 함수에 할당하여 obj1, obj2가 같은 함수를 공유하고 있는 형식이다.
  // class에서의 멤버함수의 존재에 대한 비유이다.

}
```

## 02. 가상함수의 원리와 가상함수의 테이블
  - V-Table과 가상함수의 동작
    - 가상함수가 존재하는 class에서는 컴파일러가 "가상함수 테이블(V-Table)"을 만든다.
    - main이 실행되기도 전에 V-Table이 이미 만들어진다.
    - 후에 객체가 생성되면 각 class 별 V-Table의 주소값이 각 객체에 저장된다.
    - **가상함수의 동작 : 객체에서 함수 실행 -> V-Table참조 -> 함수의 Value확인해서 주소 확인 -> 함수 실행**
  - V-Table의 구성. (Key , Value)
    - Key값에는 어디class의 무슨 함수인지를 구분지어주는 역할의 값이 들어간다.
    - Value값에는 그 함수의 주소정보가 저장된다.
  - V-Table의 생성.
    - 기초 class에서 파생된 유도 class가 생성되면, 유도 class의 가상함수가 기초 class 가상함수의 존재를 덮어버린다.(오버라이딩 됨)
    - 오버라이딩은 유도 class에 존재하는 함수만 호출되게끔 V-Table상의 key값과, value값을 변경시켜버린다.
    - 예시
      - AAA class의 virtual 함수 Func1과 Func2, V-Table
      - BBB class의 재정의된 virtual 함수 Func1과, 새로 정의된 함수 Func3, V-table
      - 가상함수가 유도 class에서 재정의되는 순간, V-Table 상에서 대체되어버렸다. (오버로딩)
      
|Key|Value|
|:---:|:---:|
|void AAA::Func1()|1번지|
|void AAA::Fucn2()|2번지|
      
|Key|Value|
|:---:|:---:|
|void BBB::Func1()|5번지|
|void BBB::Fucn2()|2번지|
|void BBB::Func3()|3번지|

## 03. 다중상속 (Multi Inheritance)
  - 동시에 두개의 class를 상속하는 상태이다.
  - 특수한 경우에만 사용하고, 그외의 경우에는 문제가 발생할 가능성이 농후해서 가급적 사용하지 않는 것이 좋다.
  - 다중상속의 방법 & **모호성(ambiguous)** 극복 방법
```cpp
class AAA{
public:
  void Foo() { cout << "Foo() called" << endl; }
};

class BBB{
public:
  void Foo() { cout << "Foo() called" << endl; }
};

// 다중상속의 방법을 나타낸다.
class MultiDerived : public AAA, protected BBB{
public:
  void complexFoo(){
   // 다중상속시 멤버 명이 중첩되는 문제를 극복하는 방법까지 나타냈다.
   AAA::Foo();
   BBB::Foo();
  }
}
```

## 04. 가상상속 (Virtual Inheritance)
  - **Base class** <- **Middle1 class**, **Middle2 class**가 동시에 상속 <- 이 둘을 **Last class**가 다중상속 한 경우의 문제 해결방식
  - 가상상속의 사용 이유
    - 가상상속을 사용하지 않으면 Last class에 Base class가 두개 존재하게 된다.
    - Base class가 두개 존재하면 Base class의 함수를 사용할때 모호성 문제 발생. (둘 중 어떤 Base class의 함수를 이용하는 것인지에 대한 문제 발생.)
    - **virtual 상속을 해준다면, Base class가 Last class에 하나만 상속된 것같은 효과를 만들어준다. / 모호성 문제의 해결**
    
```cpp
class Base{
...
};

// virtual 가상상속
class Middle1 : virtual public Base{
...
};

class Middle2 : virtual public Base{
...
};

// Base가 한번 상속된 효과를 얻게됨.
class Last : public Middle1, public Middle2{
...
};
```

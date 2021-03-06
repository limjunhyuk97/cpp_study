# Const, Friend, Static, Mutable

## Const

### 1. const 객체 선언
  - 객체의 const 멤버함수만 부를 수 있게, const 객체 선언을 할 수 있다.
  - const로 한정하지 않은 멤버함수들은 호출이 불가능하다.
```cpp
// class 선언
class A{
private:
  int num;
public:
  void FooFunc(){ ... }
  void FooConstFunc() const { ... }
};

// const 객체 생성
const A a;
a.FooFunc();      // 호출 불가능
a.FooConstFunc(); // 호출 가능
```

### 2. const 함수와 함수 오버로딩
  - const 함수인지, 아닌지를 바탕으로 함수 오버로딩이 이루어진다.
```cpp
// class 선언
class A{
private:
  int num;
public:
  void FooFunc(){ ... }
  void FooFunc() const { ... }
};

// const 객체 생성
const A a;
A b;
a.FooFunc();  // FooFunc() const{ ... } 호출
b.FooFunc();  // FooFunc(){ ... } 호출
```

## Friend

### 1. class의 friend 선언 / (private 멤버 접근) / friend class class_name
  - class A가 class B를 friend라고 선언했을 때, B는 A의 private 멤버들에 접근이 가능하다.
  - **public 영역에서든, private 영역에서든, friend 선언이 이루어 진다면**, private 멤버들에 접근 가능.

```cpp
#include <iostream>
#include <string>
using namespace std;

class Girl;
// Girl이라는 것이 class임을 미리 선언해 줌

class Boy {
private:
	int height;
	friend class Girl;
  // 1. 이 선언 상에서 Girl이 class 임을 선언해주는 역할
  // 2. friend로서 Boy class의 private 멤버변수를 참조 가능하다고 알박기.
  
public:
	Boy(int len):height(len){}
	void ShowYourFriendInfo(Girl& frn);	
  // 아직 Girl의 멤버변수들이 정의되지 않았기 때문에 phNum을 사용할 수 없다.
  // Boy가 Girl의 private 멤버를 이용가능하도록 알박고, 함수로 끌어와서 사용
  
};

class Girl {
private:
	string phNum;
  
public:
	Girl(string num):phNum(num){}
	void ShowYourFriendInfo(Boy& frn) {
		cout << "His height : " << frn.height << endl;
	}
  // Girl도 Boy의 private 멤버를 이용가능하게 알박고, 함수로 끌어와서 사용
  
	friend class Boy;	
  // public 위치에 friend 선언이 되더라도, 멤버변수에 접근이 가능해진다.
  // friend로서 Girl class의 private 멤버변수를 참조가능하다고 알박기.
};

void Boy::ShowYourFriendInfo(Girl& frn) {
	cout << "her phone number : " << frn.phNum << endl;
}


int main(void) {

	Boy boy(177);
	Girl girl("010-2271-2971");
	boy.ShowYourFriendInfo(girl);
	girl.ShowYourFriendInfo(boy);

	return 0;
}
```

### 2. friend 선언 사용시기
  - friend 선언은 지나치면 정보은닉을 무너뜨릴 수 있기에 위험할 수 있다.
  
### 3. 함수의 friend 선언 / (전역, 멤버 함수 접근) / friend return_type_def class_name::func_name()
  - 예시를 봤을 때 전역함수와, 다른 class의 멤버함수를 friend로 사용하는 이유로 두가지를 추릴 수 있을 것 같다.
    - friend로 선언하였기에 **class2 함수가**, **class1 private 멤버변수에 직접접근** 가능하다.
    - **class2 함수가 구동**하면서 **class2와 class1의 멤버변수들의 변화를 모두 견인**할 수 있다.
    - **자주 사용되는 함수를 전역에** 빼두고, **여러 class에서 friend 선언을 통해서 끌어와서 쓸 수 있을 것**이다.
```cpp
#include <iostream>
using namespace std;

class Point;
// Point class 존재 선언.

class PointOP {
private:
	int opcnt;
public:
	PointOP() : opcnt(0) {}

	Point PointAdd(const Point&, const Point&);
	Point PointSub(const Point&, const Point&);
	// 매개인자 명을 포함하지 않는 방식의 선언도 가능하다.

	~PointOP() {
		cout << "Operation times : " << opcnt << endl;
	}
	// 종료되면서 몇번 실행됬는지 출력해주는 역할.
};

class Point {
private:
	int x;
	int y;
public:
	Point(const int &xpos, const int &ypos) :x(xpos), y(ypos){}

	friend Point PointOP::PointAdd(const Point&, const Point&);
	friend Point PointOP::PointSub(const Point&, const Point&);
	// Point 객체를 반환하는 PointOP라는 class 선언 안에 있는 PointAdd함수와, PointSub함수에 대해 friend선언
	
	friend void ShowPointPos(const Point&);
	// void를 반환하는 전역에 있는 ShowPointPos함수에 대해 friend 선언
};

Point PointOP::PointAdd(const Point& pnt1, const Point& pnt2) {
	opcnt++;
	return Point(pnt1.x + pnt2.x, pnt1.y + pnt2.y);
	// Point 클래스의 friend로 선언되었기에, PointOP의 함수 PointAdd가 Point클래스의 private 멤버변수에 직접 접근 가능. 
}

Point PointOP::PointSub(const Point& pnt1, const Point& pnt2) {
	opcnt++;
	return Point(pnt1.x - pnt2.x, pnt1.y - pnt2.y);
	// Point 클래스의 friend로 선언되었기에, PointOP의 함수 PointSub가 Point클래스의 private 멤버변수에 직접 접근 가능.
}

int main(void) {

	Point pos1(1, 2);
	Point pos2(2, 4);
	PointOP op;
	ShowPointPos(op.PointAdd(pos1, pos2));
	ShowPointPos(op.PointSub(pos2, pos1));
	return 0;
}

void ShowPointPos(const Point& pos) {
	cout << "x : " << pos.x << ", ";
	cout << "y : " << pos.y << endl;
}
```

## Static
  - **외부 static 변수**
  - **내부 static 변수**
  - **class static 멤버 변수**
  - **class static 멤버 함수**

### 1. C 언어에서의 static
  - **전역변수에서의 static (외부 static 변수)**
     - 프로그램 시작과 동시에 0으로 초기화됨. 이후로 초기화 되지 않음. 프로그램 종료시 소멸됨.
     - 해당 소스파일 내에서만 변수 참조 가능. (다른 소스파일들은 이 변수 이용할 수 없음. 파일단위 정보은닉.)
     - 해당 소스파일 내의 모든 함수에서 변수 참조 가능.
```cpp
// static.cpp 파일

#include <stdio.h>
static int num = 0;
// '정적' 전역 변수
void foo(){
   ...
}

// main.cpp 파일

#include <stdio.h>
extern int num;
// 다른 파일에서 변수가져오는 extern 사용할 수가 없음.
int main(){
   ...
}

```     
  - **함수 내에서의 static (내부 static 변수)**
     - 프로그램 시작과 동시에 0으로 초기화됨. 이후로 초기화 되지 않음. 프로그램 종료시 소멸됨.
       - 지역변수와는 다르게 함수종료되도 값이 소멸되지 않음
     - 해당 소스 파일 내의 이 함수 내에서만 변수 참조 가능. (다른 함수들에서는 이용 불가능. 함수단위 정보은닉.)
     
```cpp
#include <stdio.h>

int foo(void){
  static int num;
  ++num;
  return num;
}

int main(void){
  printf("%d ", foo());
  printf("%d ", foo());
  printf("%d ", foo());
  // 1 2 3 출력
  
  return 0;
}
```
     
  - static 정적변수는 프로그램 시작시 생성, 초기화되고, 프로그램 종료시 사라진다.
     
### 2. C++ 에서의 static 등장
  - 전역공간에 class들과 관여하는 전역변수들을 풀어 놓으면 문제 생길 수도 있음
    - **class 내부에 전역변수를 넣어버리자.** -> **class 내부에 static 멤버변수를 넣자.** (**static 멤버변수**를 **class 변수**라고도 함)
  - static 멤버변수와 멤버함수는 클래스 이름으로 한번에 감싸놓은 전역 변수 및 함수와 같다.

### 3. static 멤버변수(class static 변수) 특성 (객체 멤버 아니다!)
  - 객체들 각각에 할당되는 메모리 공간이 아니라, **객체들이 공유**하는 **하나의 메모리 공간**
  - **객체들 외부에 메모리 공간 존재.**
  - **멤버함수로 접근할 수 있다.**
  - **생성자 함수 내에서 초기화 해서는 안되고, 외부에서 초기화시켜야 한다.**
    - 프로그램 시작과 동시에 메모리 공간에 할당되는 변수이기 때문에, 동시에 할당시켜주려면 객체 생성보다 먼저 초기화 해야함.
    - 그러므로, 외부에서 할당과 동시에 초기화 해야함..
```cpp
class SoSimple{
public:
  static int simObjCnt;
private:
  SoSimple(){
    ++simObjCnt;
  }
};
  int SoSimple::simObjCnt = 0;	// 초기화 시 class 선언 외부에서 해줘야 한다.
```
  - **static 멤버변수는 클래스 이름으로 외부에서 접근이 가능하다!** / **일반 멤버변수는 클래스 이름으로 외부에서 접근할 수 없다!**
```cpp
SoSimple::simObjCnt	// class명으로 접근
SoSimple sim1;
sim.simObjCnt		// 객체명으로 접근
```

### 4. static 멤버함수(class static ) 특성 (객체 멤버 아니다!)
  - 선언된 클래스의 **객체들이 공유**한다.
  - **객체들 외부에 존재**한다. (**객체의 멤버가 아니다!**)
  - **static 멤버함수 내에서는 static 멤버변수와 static 멤버함수만 호출이 가능하다!**
  - **static 멤버함수는 클래스 이름으로 외부에서 접근이 가능하다!** / **일반 멤버함수는 클래스 이름으로 외부에서 접근할 수없다!**
```cpp
class SoSimple{
private:
  static int simObjCnt;
  int num;
public:
  static void Adder(int n){
    simObjCnt += n;
    num += n; // 컴파일 에러 발생, static 멤버끼리 연관에서 엮일 수 있음. (static 멤버함수와, static 멤버변수만 호출 가능.)
  }
};
int SoSimple::simObjCnt = 0;

SoSimple::Adder(3);
// class 내 함수에 접근하는 방식.
// simObjCnt값이 0에서 3으로 증가!
```

### 5. const static 멤버변수의 특성
  - class 정의 시에 **선언과 동시에 초기화 가능**
  - **지정된 값이 유지**된다!
```cpp
class Foo{
public:
  const static int a = 20;
  const static int b = 30;
};

int main(void){
  foo f1;
  f1.a	// a에 저장된 20 값을 나타냄.
  
  ...
}
```

## Mutable

### 1. mutable의 역할
  - mutable은 const 함수 내에서 한시적으로 값의 변경을 허용할 변수를 지정해주는 것이다.
```cpp
mutable int a = 3;
...
void foo(int a) const{
  ++a;	// const 함수더라도 a 변수에 mutable 선언을 했기에, 변수 값의 변경이 가능하다.
};
```
  - mutable 은 c에서 goto: 급으로 권장되지 않는 것 같다.

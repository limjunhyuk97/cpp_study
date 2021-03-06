# 문자, 혹은 문장을 입력 받는 방법

## 1. cin >>
 - **화이트스페이스가 아닌 첫 문자부터, 다음 화이트 스페이스까지의 문자를 입력받는다.**
 - **입력 버퍼에 화이트 스페이스 문자들을 남겨둔다.**
 - **화이트스페이스**
   - ' '(공백), \t, \n, \v, \f, \r

## 2. getline( cin , string )  /  string 라이브러리
 - **공백 포함 입력받고, 앞에 있는 \n을 무시하고 입력받는다.**
 - **입력 버퍼에 개행 문자를 남겨두지 않는다**
 - 받은 string에서 마지막 문자를 NULL로 안바꿈.
 
## 3. cin.getline( char형, n )  /  istream 라이브러리
 - 공백 포함 입력받고, \n을 제외하고 까지 입력받는다. (개행 무시, 남음)
 - n길이만큼 문자열을 받아서 char형 변수에 넣고, 마지막 문자열 공간을 NULL로 바꾼다. (n보다 길게 받으면 n-1까지 받고, 마지막 문자 NULL로 바꿈)
 - 위의 예에서는..
 
## 4. cin.get()
 - 공백, \n 포함해서 한문자를 입력받는다.
 - 위의 예에서는..
 ```cpp
 str3 = cin.get();
// (a  b  c  d)
// (\n)
// (e  f)              
// (\n)                 // 처리
// g  h  \n             // 남음
 ```
 
## 5. cin 입력에러 해결
  -**cin의 입력에러** 해결 방법
     - **while과 cin 같이 사용**했을 때, **잘못된 자료형이 들어오면 무한루프**에 빠짐!
     - **cin.fail()** / **cin.clear()** / **cin.ignore()** 로 해결 가능하다.
```cpp
int tmp;
cin >> tmp; (문자형이 들어온 경우!)
while(1){
   if(cin.fail()){
      cin.clear();	// 에러비트를 초기화 시킴. cin.fail()의 값을 거짓으로 바꿈.
      cin.ignore(256, '\n');	// 입력 버퍼를 제거함. 버퍼를 비워야 loop 했을 때 cin에 아무것도 들어오지 않으므로 해결됨.
      ...
   }
}
```
  - **cin.fail()**
     - 잘못된 입력이 들어오면 true를 반환
  - **cin.clear()**
     - 에러비트를 초기화 시킨다. cin.fail()을 false로 바꿈
  - **cin.ignore(읽어들일 문자 갯수, '종료지점문자')**
     - 읽어들일 문자 갯수만큼 최대로 읽어들이고, 종료지점 문자가 나오면 포함해서 입력버퍼에서 제거한다.
 
## 6. cin, getline 같이 사용시 발생가능한 문제
  - cin은 입력 버퍼에 화이트 스페이스를 남겨두고, getline은 남기지 않기에,,
  - cin.ignore()를 사용하여 입력 버퍼의 모든 내용을 지우도록 하자!

```cpp

string str1, str2;

cin >> str1;
getline(cin, str2);
// str2에 \n 문자가 들어가게 된다.

// cin.ignore()을 사용하여 입력 버퍼의 모든 내용을 지우도록 하자!
cin >> str1; cin.ignore();
getline(cin, str2);

```
 

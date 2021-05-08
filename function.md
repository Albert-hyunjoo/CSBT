# 함수 (w/ 스택)

## 전역변수와 지역변수
* `전역변수`는 전체 영역에서 접근 가능한 함수인 반면,    
  `지역변수`는 특정 함수에서 정의되어 함수 호출이 끝나면 사라지는 변수
```python
g_var = 10 # 전역 변수를 정의

def func():
    print("g_var?", g_var)

def func2():
    g_var = 20
    print("G_var2?", g_var)
if __name__ == "__main__":
    func()  # 이 경우 나오는 건 10 -- 전역 변수에 접근
    func2() # 이 경우에는 20 -- 지역 변수를 따른다
```
### 전역변수 접근 및 전환
* 만약 어떤 함수 상에서 `전역 변수` 상에 접근, 값을 바꾸고 싶으면 `global`을 활용한다.
* 외부 함수의 변수는 내부 함수가 변경하고 싶은 경우에는 `nonlocal`을 활용한다.
```python
var1 = 10

def func():
    global var1
    var1 = 20
    print("var1?", var1) # 이 경우에는 전역변수인 var1이 10->20

def outer():
    var2 = 20
    def inner():
        nonlocal var2
        var2 = 300
    inner() # 이 경우에는 var2거 20->300
```

## 인자 전달 방식에 따른 분류

### 값에 의한 전달
* `스택 프레임`에는 정의된 변수 순서대로 **독립적인** 공간에 변수가 생긴다.
* `스택` 자료구조의 특성상 위의 function이 제일 먼저 실행된 후 사라진다.
```C++
# 스택 프레임 파악하는 코드
# include <iostream>

using namespace std;

int test(int a, int b);
########## First Section ############
# 여기서 main이 실행되면서 독립적인 변수 a,b,res가 저장된다.
# 이 a,b는 아래의 test 상의 a,b와 전혀 다르다.
int main(void)
{
    int a = 10, b = 5;
    int res = test(a,b);
    /* print the result of test*/
    return 0;
}
######################################

########## Second Section ############
# 여기서 test이 실행되면서 독립적인 변수 a,b,c,d가 저장된다.
# 이 a,b는 위의 main 상의 a,b와 전혀 다르다.
int test(int a, int b) 
{
    int c = a+b;
    int d = a-b;
    return c+d;
}
######################################
```
```C++
# include <iostream>

using namespace std;

void change_value (int x, int value) 
{
    x = value;
    /* print the x */
}
############# function start point ##############
# 먼저 x = 10이 정의된 다음
# change_value가 실행되면서 value = 20. x = 10이 *별도의 공간*에서
# reference by value 형식이르노 위의 chance_value에서 x = 20이 되도
# 그 아래에 있는 main의 10에는 전혀 영향이 가지 않는다.
int main (void)
{
    int x = 10;
    change_value(x, 20);
    /* print the main value in main */
    
    return 0;
}
#################################################
```
### 참조에 의한 전달
* **참조에 의한 전달**방식은 인자를 전달할 때, **값이 아닌 메모리 주소**를 전달한다.
```C++
# include <iostream>

using namespace std;

void change_value (int *x, int value) 
{
    *x = value;
    /* print the x */
}
#################################################
# 아까와 비슷하게 먼저 main 스택 프레임에 x=10 변수가 저장되고,
# change_value 스택 프레임에 &x (x가 저장된 공간), value=20이 저장된다.
# *x로 역참조 (deference) 해서 메모리 주소에서 직접 변경작업이 들어가므로
# 이 경우에는 독립적인 main()의 변수 x=10이라도 -> 20으로 변경된다.
int main (void)
{
    int x = 10;
    change_value(&x, 20);
    /* print the main value in main */
    return 0;
}
#################################################
```

## 람다 함수
* `람다 함수`는 **익명 함수**로, 임시적으로 쓸 때만 주로 사용한다.
* `람다 함수`에는 `return`문이 없으며 몸체에는 반드시 식이 들어가야 한다.
```python
# 이 경우에는 key가 True, False를 판단하므로
# 짝수인 경우에는 True므로 1, 홀수는 False므로 0
# 0->1 기준으로 sort가 되므로 [1,3,5,7,9,2,4,6,8,10]
li = [i for i in range(1,11)]
li.sort(key = lambda x: x%2 == 0)
```
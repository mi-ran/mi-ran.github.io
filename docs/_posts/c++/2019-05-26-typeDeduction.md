---
title: "C++ Type Deduction"
categories:
   - c++
tags:
   - c++
---

c++98에서는 type deduction은 템플릿에 대한 것만 존재한다. c++11에 와서는 그 규칙을 조금 수정하고 auto와 decltype을 위한 규칙이 추가되었다.

### Template Type Deduction
{% highlight c++ %}
template<typename T>
void f(ParamType param);

f(expr);
{% endhighlight %}

컴파일 도중 컴파일러는 expr을 통해서 두가지 형식을 추론한다. T와 ParamType이다. 이 두가지 타입은 서로 다른 타입으로 추론되는 경우가 많다. ParamType은 const나 포인터 또는 레퍼런스가 붙는 경우가 흔하기 때문이다.

{% highlight c++ %}
template<typename T>
void f(const T& param);
int x = 0;
f(x);
{% endhighlight %}

위의 경우 T는 int형으로 추론되지만 ParamType은 const int&으로 추론된다. 이 예시에서는 T의 타입이 x의 타입과 같은 것으로 추론되지만, 실제로는 다르게 추론되는 경우도 있다. T에 대해 추론되는 형식은 x의 형태와 ParamType의 형태에 의존한다. 그 형태에 따라 세 가지 경우로 나뉜다.

#### ParamType이 포인터 또는 참조형식인 경우
1. 만일 expr이 참조 형식이면 참조 부분을 무시한다.
2. expr의 형식을 ParamType에 대해 패턴 부합 방식으로 대응시켜서 T의 형식을 결정

{% highlight c++ %}
template<typename T>
void f(T& param)

int x = 27;
const int cx = x;
const int& rx = x;

f(x);    // T는 int, param은 int&
f(cx);   // T는 const int, param은 const int&
f(rx);   // T는 const int, param은 const int&
{% endhighlight %}

cx와 rx는 T의 형식에 const도 붙어서 추론되었다. 이는 const(상수) 키워드가 붙은 변수는 값이 변하지 않아야 하기 때문이다. ParamType에 &이 붙었기 때문에 상수를 지키기위해서는 const 키워드도 함께 추론해야하는 것이다.

{% highlight c++ %}
template<typename T>
void f(const T& param)

int x = 27;
const int cx = x;
const int& rx = x;

f(x);    // T는 int, param은 const int&
f(cx);   // T는 int, param은 const int&
f(rx);   // T는 int, param은 const int&
{% endhighlight %}

하지만 위의 경우는 ParamType에 이미 const가 붙어서 추론되어있으므로 T형식 추론결과에는 const 키워드가 붙지 않는다.
참조가 아닌 포인터인 경우도 위의 규칙을 따른다.

#### ParamType이 포인터도 아니고 참조도 아닌 경우
ParamType이 포인터도 아니고 참조도 아닌 경우는 매개변수가 값으로 전달된다는 의미이다.
1. expr의 형식이 참조이면 참조부분은 무시된다.
2. expr이 const이면 const 역시 무시한다.

{% highlight c++ %}
template<typename T>
void f(T param)

int x = 27;
const int cx = x;
const int& rx = x;

f(x);    // T는 int, param은 int
f(cx);   // T는 int, param은 int
f(rx);   // T는 int, param은 int
{% endhighlight %}

#### ParamType이 보편 참조인 경우
이후 업데이트 예정

### Auto
템플릿 형식 추론 방식을 기반으로 작동한다. 템플릿 형식 추론과 마찬가지로 형식 지정자의 형태에 따라 3가지로 나뉨

- 형식 지정자가 포인터나 참조형식인 경우
- 형식 지정자가 보편 참조인 경우
- 형식 지정자가 포인터도 아니고 참조도 아닌 경우

위의 경우는 모두 템플릿과 동일하게 동작한다.

#### 템플릿 추론 방식과 다른 경우
std::initializer_list는 동일 타입의 요소를 여러 개 보관하는 타입(정확히는 템플릿)이다. C++11은 {}을 이용한 초기화시 initializer_list이 생성된다.

{% highlight c++ %}
initializer_list<int> e = {1,2,3,4};
vector a = {1,2,3,4};

int i = {1}; // 하나만 값이 있는 경우는 그냥 1로 값을 넣어줌
vector a = {1,2,3.0}; // 오류! std::initializer_list<T>에서 T를 추론할 수 없음
{% endhighlight %}

그렇다면 다음과 같이 auto를 사용한 코드에서 타입추론은 어떻게 이루어질까?

{% highlight c++ %}
auto a = {1};
{% endhighlight %}

결론적으로 볼때 a는 `initializer_list<int>`로 추론되게 된다.

이는 일반적으로 `int a = {1}`과는 다르게 타입 추론이 한번 더 일어나기 때문이다.

a의 타입을 추론하기 위해서 {1}을 확인하게 되는데 이 과정에서 중괄호 형태로 초기화되는 경우는 무조건 std::initializer_list로 추론된다.

그 다음 중괄호 안의 내용물이 같은 타입의 요소인지를 확인하는 추론을 하게 되는 것이다.

### decltype
decltype은 주어진 이름이나 표현식의 형식을 알려준다.
c++11에서 주로 함수의 반환 형식을 그 매개변수 형식들에 의존하는 함수 템플릿을 선언할 때 주로 쓰인다.
decltype의 반환 값을 거의 우리가 기본적으로 생각하는 값에서 변하지 않는다.

{% highlight c++ %}
const int i = 0; // decltype(i)는 const int
vector<int> v; // decltype(v)는 vector<int>
{% endhighlight %}

decltype을 이름에 적용하면 그 이름에 대해 선언한 형식이 산출된다. 하지만 이름보다 복잡한 왼쪽값에 대해서는 일반적으로 항상 왼쪽값 레퍼런스를 반환한다.

{% highlight c++ %}
int x = 0;
decltype(x); // int
decltype((x)); // int&
{% endhighlight %}

이러한 규칙은 잘못해서 지역변수에 대한 참조를 반환하게 하는 실수를 야기할 수 있다. 아래와 같이 말이다..

{% highlight c++ %}
decltype(auto) f1()
{
    int x = 0;
    return (x); //!!!
}
{% endhighlight %}
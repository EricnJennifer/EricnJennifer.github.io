---
layout: post_study
title:  5. 파이썬 함수 (Functions)
date: 2017-12-28 06:01:00
---
## 5. 함수(function)
<br/>
<br/>
우리가 어떠한 일을 처리하다 보면 반복적으로 특정 작업을 해야 하는 경우가 있다. 가령 예를 들어 음료수 자판기에 동전을 넣은 후 음료수 종류(콜라, 사이다, 커피 등)를 선택하면 음료수 자판기는 해당 음료를 배출구를 통해 제공한다. 여기서 자판기는 동전이 들어가면 단순하게 지정한 음료수를 제공하는 기능을 항상 수행하고 어디에 가서라도 항상 자판기만 찾으면 음료수를 선택할 수 있다. 마찬가지로 함수란 특정한 어떠한 일을 수행하도록 만들어 놓은 기능(절차(procedure) 또는 루틴(routine))이라고 할 수 있다.
<br/>
<br/>
이미 앞에서 배운 print(), str()등이 함수로써 함수는 특정값을 전달하면 화면에 출력을 해준다든가(내부적으로는 어떤 처리를 하는지 모르겠지만 모니터에 출력을 해준다) 숫자를 문자로 변환해주는 기능을 한다. 이렇게 자주 사용하는 유용한 기능을 우리는 함수라고 하며 함수는 다음과 같은 구조로 구성한다.
<br/>
<br/>

{% highlight python %}
>>> a = input()
def 함수명(인자1, 인자2, … , 인자n):
    수행할 명령 1
    수행할 명령 2
    …
    return 반환할 값 (반환할 값이 없으면 지정하지 않아도 된다)
{% endhighlight %}

<br />
<br />
앞에서 설명한 자판기의 예를 조금 억지스럽지만 다음과 같이 구성할 수 있을 것이다.
<br />
<br />

{% highlight python %}
def 자판기(동전, 음료수 종류):
    음료수 종류를 찾는다
    음료수 가격이 입력한 동전과 같은지 확인한다
    return 음료수를 배출한다
{% endhighlight %}

<br />
<br />
자판기라는 함수는 인자값으로 동전과 음료수 종류를 받는다. 자판기 함수 블록에서는 먼저 음료수가 있는지 확인하고, 음료수 가격과 동전이 일치하는지 확인한 후 음료수를 반환한다. 이제 실질적인 예를 들어 보도록 하자.

{% highlight python %}
>>> def sum(a, b):
	result = a + b
	return result

>>> a = 1
>>> b = 2
>>> ret = sum(a, b)
>>> print(ret)
3
>>> print(sum(4, 10))
14
>>>
{% endhighlight %}

<br />
<br />
위의 예는 sum()이라는 함수를 작성하고 두 개의 인자 a, b를 입력 받도록 하였다. 함수 블록에서는 전달받은 a와 b값을 더해서 result라는 변수에 저장하고 그 값을 반환한다. 실제 프로그램에서는 sum(a, b)와 같은 형식으로 변수를 인자로 전달하거나 sum(4, 10)과 같이 실제 값을 전달하여 합계값을 출력할 수 있는 것이 확인되었다. 즉, sum()이라는 함수를 만들어 놓고 우리는 어디에서나 호출하여 덧셈 연산을 할 수 있는 것이다.
<br />
<br />

### 5.1 화면 입력과 출력
<br/>
<br/>
함수의 구성 방법에 대해 좀더 자세하게 알아보자. 함수는 매개변수(arguments)를 이용하여 데이터를 전달 받고 처리한 값을 return을 이용하여 반환한다. 파이썬에서는 프로그래밍을 편하게 하기 위하여 매개변수 처리에 몇 가지 기능이 추가되어 있다.
<br/>
<br/>
#### 1) 기본 인수값
<br/>
<br/>
다음의 예를 작성해 보자.
<br/>
<br/>

{% highlight python %}
def display_msg(msg, times=1):
    print(msg * times)

display_msg("TEST")
display_msg("TEST", 5)
{% endhighlight %}

<br/>
<br/>
저장한 후 실행하여 보면 다음과 같이 나타날 것이다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
TEST
TESTTESTTESTTESTTEST
>>>
{% endhighlight %}

<br/>
<br/>
위의 예에서 display_msg()함수는 msg와 times라는 인자를 가진다. 함수를 호출할때는 반드시 인자의 개수를 맞추어 주어야 하는데, 예제와 같이 인자의 기본값이 있는 경우에는 생략하여도 된다. 주의할 점은 기본값을 지정하는 인자들은 항상 뒤쪽에 기입하여야 한다. 즉, def func(x, y=1):의 형식이 아닌 def func(y=1, x):으로 함수를 작성하면 오류가 발생한다.
<br/>
<br/>
#### 2) VarArgs 인자
<br/>
<br/>
함수를 구성할 때 전달하여야 하는 인자의 개수가 몇 개인지 정확하지 않은 경우가 있다. 이러한 경우 임의(Variable) 개수의 인자(Arguments)를 전달할 수 있다.
<br/>
<br/>

{% highlight python %}
def sum(*args):
    result = 0
    for i in args:
        result = result + i
    return result

print(sum(1, 2))
print(sum(1, 2, 3, 4))
{% endhighlight %}

<br/>
<br/>
실행한 결과는 다음과 같다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
3
10
>>>
{% endhighlight %}

<br />
<br />

### 5.2 지역(local)변수와 전역(global)변수
<br/>
<br/>
우리가 함수를 공부하면서 많이 혼동하는 부분이 지역 변수와 전역 변수이다. ‘지역’과 ‘전역’이라는 것은 단어 뜻에서도 유추할 수 있듯이 지역적으로만 사용되는 변수와 전체적으로 사용되는 변수라는 의미로써 지역변수는 함수 내에서만 사용이 되는 변수, 전역 변수는 프로그램 전체에서 사용하는 변수하고 이해하면 된다. 다음의 코드를 에디터에서 작성한 후 실행하여 본다.
<br/>
<br/>

{% highlight python %}
def local():
    a = 3              #3-1번째 수행
    print("local a: " + str(a))


a = 10                 #1번째 수행
print(a)               #2번째 수행
local()                #3번째 수행
print(a)               #4번째 수행
{% endhighlight %}

<br/>
<br/>
실행한 결과는 다음과 같다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
10
local a: 3
10
>>>
{% endhighlight %}

<br/>
<br/>
먼저 ‘a’ 라는 변수에 10을 넣고(함수는 호출이 시점에서 동작한다) 출력을 하면 10이 정상적으로 출력된다. 그리고 난 후 local()라는 함수를 호출하면 ‘a’라는 변수에 3을 대입한 후 출력한다. 그 후 print()문을 이용하여 ‘a’값을 출력하면 다시 10이 출력되는 것을 확인할 수 있다. 우리가 작성한 local()의 내부에서 사용한 ‘a’ 변수는 함수 내에서만 유효하고 함수의 동작을 마치면 없어진다. 즉, #1 에서 선언한 ‘a’ 변수와 #3-1의 ‘a’ 변수는 이름이 같지만 서로 다른 공간에 존재하는 것이다. 다음과 같이 코드를 수정하여 보자.
<br/>
<br/>

{% highlight python %}
a = 1             #1번째 수행

def local():
    global a
    a = 3         # 4-1번째 수행
    print("global a:" + str(a))

a = 10            #2번째 수행
print(a)          #3번째 수행
local()           #4번째 수행
print(a)          #5번째 수행
{% endhighlight %}

<br/>
<br/>
위의 코드를 수행하면 다음과 같다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
10
global a:3
3
{% endhighlight %}

<br/>
<br/>
차이점은 local() 함수에서 사용한 ‘a’ 변수 앞에 ‘global’이라 표시를 해 준 것이다. ‘global’ 선언을 하면 함수 외부에서 선언된 전역 변수를 직접 사용할 수 있다. 즉, #4-1에서 사용하는 ‘a’ 변수는 #1에서 선언한 변수와 동일한 것이다. 사실 프로그래밍에서는 global 변수의 사용을 권하지 않는다. 그 이유는 프로그램이 길어지면 어디에서 변수값을 바꾸는지 일일이 확인하기 힘들기 때문에 프로그램의 오류를 찾기가 힘들기 때문이다. 다음은 위의 코드를 지역변수로 사용하기 위해 return을 사용한 예를 보여준다.
<br/>
<br/>

{% highlight python %}
a = 1               #1번째 수행

def local():
    a = 3           # 4-1번째 수행
    print("global a:" + str(a))
    return a

a = 10              #2번째 수행
print(a)            #3번째 수행
a = local()         #4번째 수행 (return으로 반환한 값을 a 변수에 대입)
print(a)            #5번째 수행
{% endhighlight %}

<br/>
<br/>

![](/asset/study/python_basic/2/19.png)
    [그림 1] 지역 변수와 전역 변수
{: .borderBox}



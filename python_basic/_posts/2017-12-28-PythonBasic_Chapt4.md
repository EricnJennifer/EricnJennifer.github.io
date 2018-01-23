---
layout: post_study
title:  3. 파이썬 흐름 제어 (Flow Control)
date: 2017-12-28 04:01:00
---
## 3. 흐름 제어
<br/>
<br/>
프로그래밍은 입력, 처리, 출력의 과정을 거친다고 하였다. 우리는 앞서 데이터 처리를 위하여 한 라인씩 프로그래밍을 하고, 그 결과값을 확인하였다. 그러나 실제 프로그램에서는 조건이 맞는 경우에만 처리하거나 반복적으로 어떤 수행을 해야 하는 경우가 많이 발생한다. 이러한 과정을 수행하기 위하여 ‘흐름 제어’ 명령문을 사용하게 된다.
<br/>
<br/>

### 3.1 조건 판단
<br/>
<br/>
‘조건 판단’은 특정한 조건에 만족하면(조건이 True 이면) 어떠한 처리를 한다는 것을 의미한다. 다음의 예를 보자
<br/>
<br/>
```
시험점수가 60점 이상이면 합격이고 그렇지 않으면(미만)이면 실격이다.
--->>> IF(만약에) 시험점수가 60이상 이면(THEN) 합격이고 그렇지 않으면(ELSE) 실격이다
```

<br/>
<br/>
위의 예는 일반적으로 우리가 논리 판단하는 것을 컴퓨터 언어로 풀어 놓은 것이다. 우리는 영어에서 어떠한 가정을 할 때 ‘IF…’ 라는 식으로 문장을 만든다. 마찬가지로 컴퓨터에서도 ‘if’를 이용하여 조건식을 만들 수 있다. 다음은 앞의 예를 파이썬으로 구현한 것이다.
<br/>
<br/>
{% highlight python %}
>>> score = 90
>>> if score >= 60:
	print("합격")
…   else:
	print("실격")

합격
>>> score = 59
>>> if score >= 60:
	print("합격")
…   else:
	print("실격")

실격
>>>
{% endhighlight %}

<br/>
<br/>
위의 예에서처럼 if 문은 다음과 같은 구조를 가진다.
<br/>
<br/>
{% highlight python %}
if [조건식]:
    조건식에 맞는 처리 1
    조건식에 맞는 처리 2
    …
else:
    조건식에 맞지 않는 경우 처리 1
    조건식에 맞지 않는 경우 처리 2
    …
{% endhighlight %}

<br/>
<br/>
여기서 “조건식에 맞는 처리 1, 2, …” 을 if 블록(block)이라고 하고 “조건식에 맞지 않는 경우 처리 1, 2, …”를 else 블록이라고 하며 하나의 블록은 상위 처리문에서 4칸 들여 써서 작성해야 한다. else 블록의 경우에는 처리할 필요가 없는 경우에는 ‘else:’ 이하를 전부 사용하지 않으면 된다.
<br/>
<br/>
#### 1) 조건식
<br/>
위의 예에서 [조건식]이란 참과 거짓을 판단하기 위한 문장(statement)이다. 앞의 예제에서는 점수가(점수는 score라는 변수에 들어있다)가 60점 이상(>=: 크거나 같다)를 표시하기 위하여  “score >= 60”으로 표현한 것이다.
<br/>
<br/>
##### a. 비교 연산자
<br/>
비교 연산자는 두 개의 값을 비교하는데 사용하는 연산자이다.
<br/>
<br/>

| 비교연산자 | 설명 |
| -------- | -------- |
|X > Y	|X가 Y보다 큰 경우 참(True)|
|X >= Y	|X가 Y보다 크거나 같은 경우 참(True)|
|X < Y	|X가 Y보다 작은 경우 참(True)|
|X <= Y	|X가 Y보다 작거나 같은 경우 참(True)|
|X == Y	|X가 Y와 같은 경우 참(True): 파이썬에 ‘=’은 대입 연산자(우측의 값을 오른쪽에 넣는다는 의미)로 사용되기 때문에 ‘=’를 두개 연속해서 사용한다|
|X != Y	|X가 Y와 같지 않은 경우 참(True)|
{: .table table-striped}

<br/>
<br/>
다음의 예를 확인해 보자.
<br/>
<br/>
{% highlight python %}
>>> x = 20
>>> y = 10
>>> x > y
True
>>> x >= y
True
>>> x < y
False
>>> x <= y
False
>>> x == y
False
>>> x != y
True
>>> x = 10
>>> y = 10
>>> x > y
False
>>> x >= y
True
>>> x == y
True
>>> x != y
False
{% endhighlight %}

<br/>
<br/>
##### b. 논리 연산자
<br/>
논리 연산자는 논리식으로 판단하기 위하여 사용하는 연산자이다.
<br/>
<br/>

| 논리연산자 | 설명 |
| -------- | -------- |
|X and Y| X가 참이고 Y가 참인 경우에 참(True)이다|
|X or Y| X 또는 Y중 한가지 이상이 참인 경우에 참(True)이다|
|not X| X가 참(True)이면 거짓(False), 거짓(False)이면 참(True)이다|
{: .table table-striped}

<br/>
<br/>
다음의 예를 확인해 보자.
<br/>
<br/>
{% highlight python %}
>>> korean = 90
>>> math = 85
>>> (korean >= 90) and (math >= 90)
False
>>> (korean >= 90) or (math >= 90)
True
>>> not korean #korean 점수가 1이상이면 무조건 참이다. 만약 korean = 0 이라면 False
False
{% endhighlight %}

<br/>
<br/>
#### 2) 다양한 비교: elif
<br/>
우리가 조건을 비교하는 경우에는 단순하게 이분법적으로 판단할 수 없는 경우가 있다. 만약 시험 점수에 따라서 학점을 주는 프로그램을 작성한다면 다음과 같이 작성할 수 있을 것이다. 파이썬 편집창을 띄워 아래와 같이 작성한 후 저장하고, F5키를 눌러 실행하면 결과를 확인할 수 있다.
<br/>
<br/>
{% highlight python %}
score = 91
if score >= 90:
    print("A")
else:
    if score >= 80:
        print("B")
    else:
        if score >= 70:
            print("C")
        else:
            print("D")
{% endhighlight %}

<br/>
<br/>
위의 예에서 만약 ‘score’ 변수의 값이 90 이상이면 ‘print(“A”)’ 블록이 실행되고 그렇지 않은 경우에는 ‘else’ 블록을 수행하는데 ‘else’ 블록 내부에서 또 조건문을 이용하여 80점 이상인지를 판단하도록 구성한 것이다. 이렇게 여러 조건을 한꺼번에 판단하기 위해 사용하는 구문이 ‘elif’이다.
<br/>
<br/>
{% highlight python %}
if score >= 90:
    print("A")
elif score >= 80:
    print("B")
elif score >= 70:
    print("C")
else:
    print("D")
{% endhighlight %}

<br/>
<br/>
‘elif’를 사용하여 프로그램을 작성하면 훨씬 가독성이 높아진 것을 확인할 수 있다(기능상으로야 동일할지 모르겠으나, 나중에 프로그램을 수정하거나 잘못된 부분을 찾을 때 훨씬 쉽게 확인할 수 있다)
<br/>
<br/>
#### 3) 간단한 if문의 작성
<br/>
‘if’문 내부에서 단순하게 하나의 행만을 수행한다면 다음과 같이 줄여서 쓸 수 있다.
<br/>
<br/>
{% highlight python %}
>>> if score >= 70: print("합격")
else: print("불합격")

합격
{% endhighlight %}
<br/>
<br/>
편리하기는 하지만 가독성 부분에서 그리 권장할 사항은 아니다.
<br/>
<br/>

### 3.2 반복 수행: while
<br/>
<br/>
우리는 프로그래밍을 하다 보면 반복적으로 어떤 처리를 해야 하는 경우가 있다. 가령 예를 들어 리스트에 있는 모든 값을 출력해야 한다면 우리가 배운식으로 처리를 하면 다음과 같다.
<br/>
<br/>
{% highlight python %}
>>> a = ['A', 'B', 'C']
>>> a
['A', 'B', 'C']
>>> print (a[0])
A
>>> print (a[1])
B
>>> print (a[2])
C
{% endhighlight %}

<br/>
<br/>
위와 같이 프로그래밍을 한다면 리스트의 요소가 100개라고 하면 print문을 100번 써줘야 할 것이다. 이것은 무척이나 고달프고 고통스러운 것이다. 프로그래밍을 하다 보면 반복적으로 어떤 처리를 해야 하는 경우가 많이 발생하는데 이를 위해 미리 내장되어 있는 while문은 특정 블록을 반복하여 처리하는 기능을 제공한다. while문은 다음과 같은 구조를 가진다.
<br/>
<br/>
{% highlight python %}
while [조건식]:
    처리할 문장 1
    처리할 문장 2
    …
{% endhighlight %}

<br/>
<br/>
다음의 예를 살펴보자.
<br/>
<br/>
{% highlight python %}
>>> a = 1
>>> while a < 5:
	print(a)
	a = a + 1


1
2
3
4
>>>
{% endhighlight %}

<br/>
<br/>
먼저 ‘a’라는 변수를 만들고 정수값 1을 대입하였다. ‘while’문의 조건으로는 ‘a’의 값이 5보다 작은경우까지 하위 블록을 수행한다. 하위 블록은 먼저 ‘a’의 값을 출력하고 그 후에 ‘a’ 라는 변수에 ‘a’가 가지고 있는 값에 1을 더하여 다시 ‘a’에다 넣어준다. 즉, ‘a’의 값을 하나씩 증가시켜 주는 것이다. 다음의 그림을 확인하자.
<br/>


![](/asset/study/python_basic/2/11.png)
    [그림 1] a = a + 1의 동작 과정
{: .borderBox}

<br/>
<br/>
프로그램에서는 대입연산자(=)를 기준으로 오른쪽의 연산을 수행한 후 왼쪽의 저장소에다 결과값을 입력한다고 하였다. 즉 오른쪽의 ‘a’는 ‘a’가 가지고 있는 변수의 값을 의미하고, 왼쪽의 ‘a’는 저장할 장소를 의미한다. ‘a = a + 1은 ‘a += 1’로 줄여 쓸 수 있다. ‘while’문이 동작하는 일련의 과정은 다음과 같다.
<br/>
<br/>


![](/asset/study/python_basic/2/12.png)
    [그림 2] While 문의 수행 과정
{: .borderBox}

<br/>
<br/>
#### 1) while 문의 강제 종료: break
<br/>
‘while’문의 경우에는 조건식이 만족하는 동안 계속 블록을 반복하여 수행한다. 그러나 경우에 따라서는 조건문을 사용할 수 없는 경우가 있다. 파이썬 IDLE의 편집기를 수행하여 아래의 코드를 작성하고, 저장한 후 실행하여 보자.
<br/>
<br/>
{% highlight python %}
# while Test
goal = 55

while True:
    in_data = int(input("숫자를 입력하세요 : "))
    if in_data < goal:
        print("숫자가 작습니다")
    elif in_data > goal:
        print("숫자가 큽니다")
    else:
        print("일치합니다")
        break

print("종료합니다")
{% endhighlight %}

<br/>
<br/>
다음은 실행한 결과를 나타낸다.
<br/>
<br/>
{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
숫자를 입력하세요 : 1
숫자가 작습니다
숫자를 입력하세요 : 50
숫자가 작습니다
숫자를 입력하세요 : 70
숫자가 큽니다
숫자를 입력하세요 : 60
숫자가 큽니다
숫자를 입력하세요 : 56
숫자가 큽니다
숫자를 입력하세요 : 55
일치합니다
종료합니다
>>>
{% endhighlight %}

<br/>
<br/>
위의 예제는 계속 반복하면서 데이터를 입력 받아 특정한 값이 만족되면 프로그램을 종료하는것이다. ‘while’의 조건문을 보면 ‘True’라고 되어있는 것을 확인할 수 있다. 즉, while 블록은 항상 참(True)이기 때문에 무한이 블록을 반복하게 된다. 이런 경우 While을 종료시키는 방법은 ‘break’라는 명령어를 이용하여 가능하다. 즉, 데이터를 계속 입력 받으면서 ‘goal’의 숫자와 동일하게 되는 경우에는 ‘break’ 문을 이용하여 while 블록을 탈출할 수 있다.
<br/>
<br/>
예제에서 사용한 ‘in_data = int(input("숫자를 입력하세요 : "))’문은 키보드로부터 데이터를 입력받아 정수형(int)로 변환하여 in_data에 저장하는 것이다. 나중에 입출력 부분에서 다시 한번 설명할 예정이니 단순하게 이해하고 넘어가기를 바란다.
<br/>
<br/>
#### 2) while 문의 처음으로 이동: continue
<br/>
break는 while 문의 종료를 강제적으로 지정하는 경우에 사용한다고 했다. continue의 경우에는 하위 블록을 수행하지 않고 다시 while 문의 처음으로 이동하기 위하여 사용한다. 다음의 예를 작성하여 보자.
<br/>
<br/>
{% highlight python %}
while True:
    in_data = input("문자열을 입력하세요:")
    if len(in_data) < 3:
        print("문자열의 길이가 짧습니다")
        continue

    print("종료합니다")
    break
>>>
{% endhighlight %}

<br/>
<br/>
코드를 수행한 예는 다음과 같다
<br/>
<br/>
{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
문자열을 입력하세요:ab
문자열의 길이가 짧습니다
문자열을 입력하세요:abcd
종료합니다
>>>
{% endhighlight %}

<br/>
<br/>
위의 코드에서는 while문의 조건이 True로 되어 있어 무한반복을 한다. 문자열을 입력 받아서 그 길이가 3보다 작은 경우에는 “문자열의 길이가 짧습니다”를 출력하고 하위 코드를 수행하지 않고 While 문의 최 상단으로 이동한다. 만약 문자열의 길이가 3 이상이면 if 문은 수행이 되지 않고 “종료합니다”를 출력한 후 while 블록을 탈출한다. 다음은 이러한 프로그래밍의 과정을 순서도(flow chart)로 표시한 것이다.
<br/>
<br/>
![](/asset/study/python_basic/2/13.png)
    [그림 2] While … continue 순서도
{: .borderBox}

<br/>
<br/>
위의 예에서 만약 break 문이 없다면 while문은 무한 반복하게 될 것이다.
<br/>
<br/>

### 3.3 반복 수행: for
<br/>
<br/>
앞서 우리는 while을 이용하여 데이터를 반복적으로 처리하는 방법에 대하여 알아보았다. while 문 다음에 오는 조건식은 while 블록을 탈출하는 조건으로 사용되는데 이와 유사하게 반복적인 기능을 제공하는 for 구문이 있다. for의 구조는 다음과 같다.
<br/>
<br/>
```
for [변수] in 리스트(또는 튜플, 문자열):
    처리할 Block
```
<br/>
<br/>
{% highlight python %}
>>> a = ['A', 'B', 'C']
>>> for i in a:
	print(i)

A
B
C
>>>
{% endhighlight %}

<br/>
<br/>
위의 예에서는 ‘a’ 리스트의 값을 순차적으로 ‘I’ 변수에 가져 오고, 이를 출력하는 것이다. for 구문은 while문과 다르게 리스트나 튜플 또는 문자열등을 이용하여 변수에다 하나씩 값을 가지고 올 수 있는 기능을 제공하기 때문에 유용하게 사용할 수 있다. 다음은 튜플을 이용하는 예이다.
<br/>
<br/>
{% highlight python %}
>>> a = [('science', 100), ('math', 95), ('computer', 97)]
>>> for (subject, score) in a:
	print(subject + " : " + str(score))


science : 100
math : 95
computer : 97
>>>
{% endhighlight %}

<br/>
<br/>
튜플의 경우에는 각 요소(element)들이 2개의 값을 가지고 있으므로 in 을 이용하여 처음 아이템(item)을 subject변수에, 두번째 아이템을 score 변수에 가지고 와서 리스트 의 끝까지 반복하면서 출력하는 예이다.
<br/>
<br/>
#### 1) range() 함수의 사용
<br/>
for문과 함께 잘 사용하는 range() 함수는 시작과 끝의 값을 지정하여 주면, 연속된 숫자 객체를 만들어 준다.
<br/>
<br/>
{% highlight python %}
>>> a =range(10)
>>> a
range(0, 10)
>>> a = range(2, 10)
>>> a
range(2, 10)
>>> for i in range(1, 5):
	print(i)


1
2
3
4
{% endhighlight %}

<br/>
<br/>
위의 예에서 보듯이 range(처음시작값, 마지막값)으로 입력하는 경우에는 숫자열은 처음 시작값부터 마지막값 - 1까지(미만으로 인식) 숫자 객체로 만들어짐을 알 수 있다. 다음의 예를 편집기에서 작성하여 저장한 후 실행하여 보자.
<br/>
<br/>
{% highlight python %}
# test.py로 저장한 후 실행
score = [100, 55, 70, 35, 90]
for number in range(len(score)):
    if (score[number] >= 60):
        print("%d 학생 합격" % (number+1))
    else:
        print("%d 학생 불합격" % (number+1))
{% endhighlight %}

<br/>
<br/>
실행한 결과는 다음과 같다.
<br/>
<br/>
{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
1 학생 합격
2 학생 불합격
3 학생 합격
4 학생 불합격
5 학생 합격
{% endhighlight %}

<br/>
<br/>
위의 예는 range()함수를 이용하여 인덱스를 자동을 생성한 후 인덱스에 해당하는 요소값을 가지고 오는 예를 보여준다. range()함수의 인자가 하나인 경우 시작인덱스는 0부터 생성되고, len()함수를 이용하여 리스트의 개수를 얻어온다. 실제 얻어온 리스트의 개수는 5인데 range()를 이용하여 생성된 숫자 객체는 (0, 1, 2, 3, 4)가 되어 리스트 아이템의 인덱스만큼 반복할 수 있다.
<br/>
<br/>
#### 2) continue와 break의 사용
<br/>
for 문에서도 while과 마찬가지로 continue와 break를 사용할 수 있다.

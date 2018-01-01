---
layout: post_study
title:  4. 입력과 출력 (Input/Output)
date: 2017-12-28 05:01:00
---
## 4. 입력과 출력
<br/>
<br/>
앞서 프로그래밍은 “입력 >> 처리 >> 출력”의 과정을 거친다고 이야기하였다. 본 장에서는 데이터를 입력 받는 방법과 출력하는 방법에 대해 좀더 자세히 알아보도록 하겠다.
<br/>
<br/>

### 4.1 화면 입력과 출력
<br/>
<br/>
사용자가 키보드를 통하여 데이터를 입력하게 되는 경우 우리는 입력한 데이터를 받아 컴퓨터의 저장소(변수)에 저장하여야 한다. 파이썬에서는 키보드 입력을 위하여 input()함수를 제공한다.
<br/>
<br/>

{% highlight python %}
>>> a = input()
안녕하세요
>>> print(a)
안녕하세요
>>> a = input()
123
>>> print(a)
123
>>> a + 10
Traceback (most recent call last):
  File "<pyshell#45>", line 1, in <module>
    a + 10
TypeError: Can't convert 'int' object to str implicitly
>>> a = int(input())
123
>>> a + 10
133
>>>
{% endhighlight %}

<br/>
<br/>
위의 예에서 “123”이라는 값을 입력하여 a 라는 변수에 저장하고 출력하는 경우에는 정상적으로 처리되지만 10을 더하는 경우에는 오류를 발생한다. 기본적으로 파이썬에서는 데이터 입력을 문자열로 취급하며, 만약 정수 또는 실수의 값을 변수에 저장하려면 int()라는 함수 또는 float()함수를 이용하여 형 변환(type casting)을 해 주어야 한다.
<br/>
<br/>
{% highlight python %}
>>> a = input("이름을 입력하세요:")
이름을 입력하세요:홍길동
>>> print(a)
홍길동
>>>
{% endhighlight %}

<br/>
<br/>
만약 데이터를 입력받으면서 특정한 문구를 출력하고자 한다면 input()의 인자로 출력할 문자열을 함께 전달하면 출력 문자열뒤에 데이터를 입력할 수 있다.
<br/>
<br/>
데이터를 화면에 출력하기 위해 우리는 print()함수를 사용하였다. 화면에 출력할 시 포맷팅을 이용하는 방법에 대해서는 앞서 설명하였으므로 기타 편리한 기능을 몇 가지 알아보도록 하겠다.
<br/>
<br/>

{% highlight python %}
>>> print("안녕하세요" + "홍길동입니다")
안녕하세요홍길동입니다
>>> print("안녕하세요" "홍길동입니다")
안녕하세요홍길동입니다
>>>
{% endhighlight %}

문자열과 문자열을 합치기 위하여 ‘+’ 연산자를 사용하지만 생략하는 경우에도 자동으로 인식한다.

{% highlight python %}
>>> print("안녕하세요" + " " + "홍길동입니다")
안녕하세요 홍길동입니다
>>> print("안녕하세요", "홍길동입니다")
안녕하세요 홍길동입니다
>>>
{% endhighlight %}

<br/>
<br/>
띄어쓰기를 하기 위하여 두개의 문장에 “ “(공백) 문자를 추가하였으나 ‘,’를 이용하면 두개의 문자열 사이에 자동으로 띄어 쓰기를 지원한다.
<br/>
<br/>

{% highlight python %}
>>> for i in range(4):
	print(i)

0
1
2
3
>>> for i in range(4):
	print(i, end=" ")


0 1 2 3
{% endhighlight %}

<br/>
<br/>
print()문을 수행하면 자동으로 줄바꿈(new line: \n)이 출력된다. 그러나 때로는 한줄에 데이터를 계속해서 출력해야 하는 경우가 있다. 이 경우 ‘end=’ 이후에 출력할 값 이후에 끝에 붙을 문자열을 넣어주면 자동 줄바꿈이 실행되지 않는다. 즉, print(i)는 print(i, end=”\n”)와 동일하다.
<br/>
<br/>

### 4.2 파일 입력과 출력
<br/>
<br/>
우리가 프로그래밍을 하게 되면 데이터를 저장된 데이터를 읽어와 어떤 처리를 하고 결과값을 파일로 저장해야 하는 경우가 많이 생긴다. 본 절에서는 파일 입출력(File Input/Output: File I/O)에 대해 알아보도록 하자.
<br/>
<br/>
#### 1) 파일 생성
<br/>
<br/>
{% highlight python %}
#파일 객체 = open(파일명, 파일 속성)
>>> f = open(“test.txt”, “w”)
>>> f.close()
{% endhighlight %}

<br/>
<br/>
파일을 생성하기 위해서는 파일명과 파일 속성을 인자로 하여 open()함수를 이용한다. 파일 속성은 다음과 같다.
<br/>
<br/>

| 파일 속성 | 설명 |
| -------- | -------- |
|r|	파일명에 해당하는 파일을 읽기 모드로 연다|
|w|	파일명에 해당하는 파일을 생성한다. 만약 기존에 파일이 있다면 기존파일을 삭제하고 새로 만든다(기존 내용이 전부 사라진다)|
|a|	기존에 있는 파일의 마지막부터 데이터를 추가한다|
{: .table table-striped}

<br/>
<br/>
파일명은 경로를 포함할 수 있으며, 경로명을 주지 않고 파일명만 입력한 경우에는 실행 파일이 동작하는 디렉터리에 생성된다. open()함수는 파일 객체를 반환한다. 일정한 처리가 완료되면 close()함수를 이용하여 파일을 닫아 주어야 한다.
<br/>
<br/>
위의 예제에서는 ‘test.txt’파일을 작성하는 파일이 있는 디렉터리에 생성하고 파일객체를 닫았으므로 아무것도 기록되지 않은 ‘test.txt’ 파일이 생성될 것이다.
<br/>
<br/>
#### 2) 파일 읽기
<br/>
<br/>
먼저 메모장을 열어 아주 간단한 파일을 생성하고 ‘C’ 디렉터리 하위에 ‘python_sample’이라는 디렉터리를 만들고 test.txt라고 저장을 한다.
<br/>
<br/>

![](/asset/study/python_basic/2/14.png)
    [그림 1] 메모장에 샘플 파일 생성
{: .borderBox}

<br/>

![](/asset/study/python_basic/2/15.png)
    [그림 2] c:/python/test.txt로 저장
{: .borderBox}

<br/>
<br/>
파일이 저장되었으면 다음과 같이 에디터에서 작성한 후 저장하고, 실행한다.
<br/>
<br/>

{% highlight python %}
f = open("c:/python_sample/test.txt", "r")
print(f.readline())
f.close()
{% endhighlight %}

<br/>
<br/>
F5키를 눌러 실행하면 다음과 같이 출력될 것이다.
<br/>
<br/>

```
======== RESTART: c:/python_sample/test.py ========
Python File I/O Test

>>>
```

<br/>
<br/>
readline()은 파일의 한줄을 읽어오는 함수이다. 우리가 test.txt 파일에 2줄의 문서를 작성했지만 한줄밖에 가지고 오지 못한다. 전체를 읽어오기 위해서 다음과 같이 프로그램을 수정해 보자.
<br/>
<br/>

{% highlight python %}
f = open("c:/python_sample/test.txt", "r")
while True:
    line = f.readline()
    if not line:
        break
    print(line)
f.close()
{% endhighlight %}

<br/>
<br/>
실행한 결과는 다음과 같다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
Python File I/O Test

파이썬 파일 입출력 시험
>>>
{% endhighlight %}

<br/>
<br/>
위의 프로그램은 test.txt 파일을 읽기 모드로 연 후 while 문을 이용하여 한 라인씩 읽어 오면서 처리를 하다가 데이터가 더 이상 없는 경우에 break를 이용하여 블록을 빠져 나온 후 파일 객체를 반환한다.
<br/>
<br/>
readline()은 파일의 한줄을 읽는 함수이지만 파일의 모든 줄을 읽어 오기 위하여 파이썬은 readlines() 함수를 제공한다. 다음과 같이 프로그램을 작성한 후 실행한다.
<br/>
<br/>

{% highlight python %}
f = open("c:/python_sample/test.txt", "r")
lines = f.readlines()
for line in lines:
    print(line)
f.close()
{% endhighlight %}
<br/>
<br/>
실행한 결과는 다음과 같다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
Python File I/O Test

파이썬 파일 입출력 시험
>>>
{% endhighlight %}

<br/>
<br/>
readlines()는 읽어온 모든 행을 리스트처럼 저장한다. read() 함수는 아예 파일을 하나의 객체로 가지고 오므로 전체 파일을 출력하는 경우에는 편리하다.
<br/>
<br/>
{% highlight python %}
f = open("c:/python_sample/test.txt", "r")
lines = f.read()
print(lines)
f.close()
{% endhighlight %}
<br/>
<br/>
실행한 결과는 다음과 같다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
Python File I/O Test
파이썬 파일 입출력 시험
>>>
{% endhighlight %}

<br/>
<br/>
readlines()와 read()의 출력 결과를 보면 한줄이 줄어 있는 것을 확인할 수 있다. 이는 print()함수가 라인별로 동작하면서 행바꿈이 일어나기 때문이다.
<br/>
<br/>
#### 3) 파일 쓰기
<br/>
<br/>
다음과 같이 프로그램을 작성한 후 저장한다.
<br/>
<br/>

{% highlight python %}
f = open("c:/python_sample/test.txt", 'w')
f.write("첫번째 줄입니다")
f.write("두번째 줄입니다")
f.close()
{% endhighlight %}

<br/>
<br/>
프로그램을 실행하면 다음과 같이 프롬프트만 나타날 것이다.
<br/>
<br/>

{% highlight python %}
======== RESTART: c:/python_sample/test.py ========
>>>
{% endhighlight %}

<br/>
<br/>
탐색기를 이용하여 test.txt 파일을 열면 기존의 내용이 삭제되고 새로운 내용으로 만들어 진진 것 확인할 수 있을 것이다.
<br/>
<br/>

![](/asset/study/python_basic/2/16.png)
    [그림 1] text.txt 파일
{: .borderBox}

<br/>
<br/>
여기서 주의할 점은 두 개의 행이 붙어서 나왔다는 것이다. 파일을 쓸 때 줄바꿈을 하기 위해서는 “\n”을 입력하여야 한다.
<br/>
<br/>

{% highlight python %}
f = open("c:/python_sample/test.txt", 'w')
f.write("첫번째 줄입니다")
f.write("\n")
f.write("두번째 줄입니다")
f.write("\n")
f.close()
{% endhighlight %}

<br/>
<br/>
실행한 후 다시 파일을 확인해 보면 정상적으로 줄바꿈이 된 것을 확인할 수 있다.
<br/>
<br/>

![](/asset/study/python_basic/2/17.png)
    [그림 2] 이스케이프 문자인 “\n”을 이용한 줄바꿈 결과
{: .borderBox}

<br/>
<br/>
#### 4) 파일에 내용 추가
<br/>
<br/>
파일에 내용을 추가 하기 위하여 다음과 같이 코드를 작성한 후 저장한다.
<br/>
<br/>

{% highlight python %}
f = open("c:/python_sample/test.txt", 'a')
f.write("추가한 줄입니다")
f.close()
{% endhighlight %}

<br/>
<br/>
실행한 후 저장된 test.txt 파일을 확인하면 이전 파일의 맨 마지막에 내용이 추가된 것을 확인할 수 있다.
<br/>
<br/>

![](/asset/study/python_basic/2/18.png)
    [그림 3] 파일 열기 – 추가 모드
{: .borderBox}

<br/>
<br/>
#### 5) with 문과 함께 사용
<br/>
<br/>
지금까지 파일 객체를 생성하면 반듯이 파일 객체를 반환하는 close()함수를 사용하여야 했다. 이는 매우 번거로운 일이다. 이를 편하게 사용하기 위하여 파이썬에서는 with문과 함께 사용할 수 있다.
<br/>
<br/>

{% highlight python %}
with open("c:/python_sample/test.txt", "r") as f:
    lines = f.read()
    print(lines)
{% endhighlight %}

<br/>
<br/>
with문과 함께 사용하는 파일 객체는 하위 블록에서 유효하다. 만약 블록을 벗어나게 되면 오류가 발생한다.


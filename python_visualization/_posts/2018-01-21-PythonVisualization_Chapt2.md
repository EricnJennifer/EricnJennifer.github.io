---
layout: post_study
title:  2. 그래프를 그리자 - matplotlib
date: 2018-01-21 11:01:00
categories: python_visualization
---
## 2. 그래프를 그리자 : matplotlib
<br/><br/><br/>
우리는 [공공 정보 데이터 수집하기](https://ericnjennifer.github.io/python_crawling/2018/01/21/PythonCrawling_Chapt11.html)에서 ‘출입국 관광객 정보 서비스’ 부분에서 ‘matplotlib’를 활용하여 그래프를 그리는 방법에 대해 알아보았다. ‘matplotlib’은 파이썬을 이용한 데이터 분석에 있어서 가장 일반적으로 사용되는 라이브러리로 수치해석에 많이 사용되는 ‘MATLAB’과 유사한 형태의 차트를 그릴 수 있는 다양한 함수를 제공해 준다.
<br/><br/><br/>
### 2.1 기본 그래프 그리기
<br/><br/>
이중 먼저 pyplot을 이용하는 방법에 대해 알아보도록 하자. 단위 코딩을 하기 위하여 python 에뮬레이터를 실행시키거나 python IDLE를 실행시킨후 다음과 같이 작성한다.

{% highlight python %}
>>> import matplotlib.pyplot as plt
>>> plt.plot([1,2,3,4])
>>> plt.xlabel('X-axis label’)
>>> plt.ylabel('Y-axis label')
>>> plt.show()
{% endhighlight %}

<br/>
plt.show()를 실행하면 [그림 1]과 같이 그래프 윈도우가 나타날 것이다.
<br/><br/>

![](/asset/study/python_visualization/1.png)
[그림 1] pyplot 예제 1
{: .borderBox}

<br/>
plot()함수는 다양현 형태의 인자값을 가진다. 먼저, [그림 1]을 보면 y축의 값은 1부터 시작되는데 x축의 값은 0부터 시작함을 알 수 있다. 이는 우리가 plot()함수에 y값만을 할당하고 x값은 함수 내부에서 지정하는 값(인덱스 값이므로 0부터 시작)으로 매칭되었기 때문이다. 만약 인덱스를 주고 싶다면 다음과 같이 plot()함수를 수정한 후 수행해 보자.

{% highlight python %}
>>> plt.plot([1,2,3,4],[1,2,3,4])
>>> plt.show()
{% endhighlight %}

<br/>
코드를 수행하면 [그림 2]와 같이 x축의 인덱스가 1부터 시작하는 것을 확인할 수 있다.
<br/><br/>

![](/asset/study/python_visualization/2.png)
[그림 2] x축에 인덱스를 준 경우
{: .borderBox}

<br/>
그런데 이상한 것은 이 그래프가 직선을 유지하고 있다는 것이다. 원래 원하는 것은 x=1일 때 y=1 이고 x=2일 때 y=2 인 그래프를 그리는 것이 목적일 수 있다. plot()함수는 기본적으로 꺽은선 그래프를 기본값을 하고 있다. 이 인자값을 바꾸기 위하여 다음과 같이 해보자.

{% highlight python %}
>>> plt.plot([1,2,3,4], [1,2,3,4], 'ro')
>>> plt.show()
{% endhighlight %}

<br/>
코드를 수행하면 [그림 3]과 같이 화면에 나타나는 것을 확인할 수 있다
<br/><br/>

![](/asset/study/python_visualization/3.png)
[그림 3] ‘ro’ 옵션으로 값 표시하기
{: .borderBox}

<br/>
기본적으로 ‘plot()’은 ‘b-‘ 옵션을 기본값으로 가지고 있는데, 파란색(b)라인(-) 이라는 뜻이고, ‘ro’ 옵션을 주면 ‘적색(r)’ 으로 ‘o’ 표시를 의미한다. 만약 ‘파란색’을 이용하여 ‘v’ 마크를 표시하고 싶다면 인자값으로 ‘bv’라고 지정하면 된다. 두개의 리스트가 같은 인덱스에 해당하는 값으로 표시하는 것을 알 수 있다. ‘plot()’의 옵션으로 사용되는 값들은 그래프 형식 이외에 여러가지가 있는데 ‘matplotlib’ 공식 사이트(http://matplotlib.org)에서 확인하기 바란다.
<br/><br/><br/>
### 2.2 복수개의 시리즈 그리기
<br/><br/>
이제는 한 개의 그래프에 여러 개의 데이터를 표현하는 방법에 대해 알아보자.

{% highlight python %}
>>> plt.plot([1,2,3,4],[1,2,3,4],'r-', [1,2,3,4],[3,4,5,6],'v-')
>>> plt.show()
{% endhighlight %}

<br/>
코드를 수행하면 [그림 4]와 같이 두개의 그래프를 하나의 그림에 나타낼 수 있다.
<br/><br/>

![](/asset/study/python_visualization/4.png)
[그림 4] 하나의 화면에 여러 개의 그래프 그리기
{: .borderBox}

<br/><br/><br/>
### 2.3 한글 처리
<br/><br/>
[그림 1]에서 우리는 X축과 Y축에 ‘.xlabel()’과 ‘.ylabel()’을 이용하여 ‘라벨’을 넣을 수 있었다. 그런데 아래와 같이 입력을 한 후 수행을 하면 [그림 5]처럼 글자가 깨져 나오는 것을 확인할 수 있다.

{% highlight python %}
>>> plt.plot([1,2,3,4])
>>> plt.xlabel('x축 한글표시')
>>> plt.show()
{% endhighlight %}

<br/><br/>

![](/asset/study/python_visualization/5.png)
[그림 5] pyplot에서의 한글 깨짐
{: .borderBox}

<br/>
‘matplotlib’에서 한글을 사용하기 위해서는 약간의 작업이 필요하다. 먼저 탐색기를 이용하여 윈도우 폰트 디렉터리로 [그림 6]처럼 이동해 보자.
<br/><br/>

![](/asset/study/python_visualization/6.png)
[그림 6] 윈도우 설치 디렉터리 아래 Fonts 폴더로 이동
{: .borderBox}

<br/>
상당한 수의 폰트가 설치되어 있는 것을 확인할 수 있는데, 아쉽게도 여기서는 폰트의 파일명을 확인할 수 없고 적용된 폰트 리스트만 확인이 된다. 이 이유는 실제 폰트 파일은 일반폰트, 두꺼운(bold) 폰트등 여러 개의 파일로 구성되어 있는 패키지이기 때문이다. 해당 파일(여기서는 맑은 고딕)을 선택한 후 오른쪽 마우스를 눌러 [열기]를 선택하면 [그림 7]과 같이 복수개의 폰트가 존재함을 확인할 수 있다.
<br/><br/>

![](/asset/study/python_visualization/7.png)
[그림 7] 폰트 파일명 확인
{: .borderBox}

<br/>
이제 폰트 패키지의 개별 파일들이 보이게 되므로 사용할 파일을 선택한 후 오른쪽 마우스를 눌러 속성을 확인하면 상단에 파일명이 나타나는 것을 확인할 수 있다. ‘matplotlib’에서는 직접 설치된 폰트 파일명을 경로와 함께 지정해 주면 해당 폰트를 사용할 수 있도록 해준다. 이제 다음과 같이 코드를 작성해 보자.

{% highlight python %}
>>> from matplotlib import font_manager, rc
>>> import matplotlib
>>> font_location = "c:/Windows/fonts/malgun.ttf"
>>> font_name = font_manager.FontProperties(fname=font_location).get_name()
>>> matplotlib.rc('font', family=font_name)
>>> plt.plot([1,2,3,4])
>>> plt.xlabel('x축 한글표시')
>>> plt.show()
{% endhighlight %}

<br/>
코드를 수행하면 [그림 8]과 같이 정상적으로 한글이 표현됨을 확인할 수 있다.
<br/><br/>

![](/asset/study/python_visualization/8.png)
[그림 8] pyplot에서 한글 사용하기
{: .borderBox}

<br/>
‘matplotlib’는 폰트 관리를 위한 ‘fontmanager’와 리소스 관리를 위한 ‘rc’를 제공한다. ‘fontmanager’를 이용하여 [그림 7]에서 확인한 내 컴퓨터상의 한글 폰트 파일명을 가지고 온 후 이를 리소스에 할당하면 해당 폰트를 사용할 준비가 된다. 이 후 ‘matplotlib’내에서 한글을 사용하면 [그림 8]처럼 정상적으로 나타난다.
<br/><br/><br/>
### 2.4 한 화면에 여러개 그래프 그리기
<br/><br/>
앞서 우리는 [그림 4]와 같이 하나의 그래프에 여러 개의 데이터를 그리는 방법을 알아보았다. 이와 함께 하나의 화면에 여러 개의 그래프를 개별적으로 그려야 하는 경우가 있다. 이를 위하여 다음과 같이 작성해 보자.

{% highlight python %}
>>> plt.figure()
>>> plt.subplot(2, 1, 1)
>>> plt.plot([1,2,3,4], [1,2,3,4])
>>> plt.subplot(2,1,2)
>>> plt.plot([5,6,7,8],[5,6,7,8])
>>> plt.show()
{% endhighlight %}

<br/><br/>

![](/asset/study/python_visualization/9.png)
[그림 9] 한 개의 창에 여러 개의 그래프 그리기(세로형)
{: .borderBox}

<br/>
코드를 실행하면 [그림 9]와 같이 하나의 창에 두개의 그래프가 그려지는 것을 확인할 수 있다. 기본적으로 pyplot은 figure()라는 함수를 이용하여 하나의 캔버스(그림창)을 생성한다. 그리고 이 창에 여러 개의 그래프를 넣기 위해서는 ‘subplot(m , n, idx)’ 함수를 사용한다.
<br/><br/>
‘m’과 ‘n’은 매트릭스(matrix)형태로써 ‘2,1’을 지정하면 행(row)가 2개이고 열(column)이 1개인 창을 의미한다. 그리고 ‘idx’는 ‘m x n’ 형태의 창중에 ‘idx’번째 위치를 의미하는 것이다. 우리가 앞에서 plt.subplot(2, 1, 1)을 이용하여 그린 것이 [그림 9]의 상단의 그래프가 되는 것이고 plt.subplot(2, 1, 2)가 하단의 그래프가 되는 것이다(총 2 x 1의 그래프를 생성하였고 1은 첫번째 열, 2는 두번쨰 열(컬럼이 한 개밖에 없으므로 다음번으로 넘어가게 된다).
<br/><br/>
만약 하나의 행에 여러 개의 열(column)을 표시하는 형태라면 다음과 같이 작성해 본다.

{% highlight python %}
>>> plt.figure()
>>> plt.subplot(1, 2, 1)
>>> plt.plot([1,2,3,4], [1,2,3,4])
>>> plt.subplot(1, 2, 2)
>>> plt.plot([5,6,7,8],[5,6,7,8])
>>> plt.show()
{% endhighlight %}

<br/><br/>

![](/asset/study/python_visualization/10.png)
[그림 10] 한 개의 창에 여러 개의 그래프 그리기(가로형)
{: .borderBox}

<br/>
[그림 10]에서는 ‘1 x 2’ 형태의 캔버스를 생성하였고, (1, 2, 1)은 1행의 첫번째 컬럼, (1, 2, 2)는 1행의 두번째 컬럼을 의미한다.
<br/><br/><br/>
### 2.5 그래프에 문자 삽입
<br/><br/>
그래프 표현을 하면서 특정 값을 그래프 안에 삽입하여 표시하여야 하는 경우가 많이 생긴다. 문자열 처리를 위하여 다음과 같이 코드를 작성한다.

{% highlight python %}
>>> plt.plot([1,2,3,4], [1,2,3,4])
>>> plt.xlabel('x축')
>>> plt.ylabel('y축')
>>> plt.title('matplotlib 활용')
>>> plt.text(3.5, 3.0, '평균:2.5')
>>> plt.grid(True)
>>> plt.show()
{% endhighlight %}

<br/>
코드를 수행하면 [그림 11]과 같이 나타난다.
<br/><br/>

![](/asset/study/python_visualization/11.png)
[그림 11] 문자열 할당
{: .borderBox}

<br/>
‘.xlabel()’은 x축의 라벨을, ‘.ylabel()’은 y축의 라벨에 문자열을 지정한다. ‘.title()’은 그래프의 제목을상단에 표시한다. ‘.text(x, y, 문자열)’은 그래프의 지정 위치에 문자열을 표시하는데, x와 y의 값은 그래프상의 x축과 y축의 값을 의미한다. ‘.grid()’는 각 인덱스마다 격자를 표시하는데 사용한다. 모든 함수들은 위치값과 문자 색상등을 지정할 수 있으며, 자세한 내용은 공식 홈페이지에서 확인하기 바란다.
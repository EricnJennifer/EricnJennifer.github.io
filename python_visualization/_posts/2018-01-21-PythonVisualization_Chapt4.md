---
layout: post_study
title:  4. 데이터 기반 추천서비스 - 상관관계분석, pandas, numpy
date: 2018-01-21 12:01:00
categories: python_visualization
---
## 4. 데이터 기반 추천: 데이터 상관 관계 분석
<br/><br/><br/>
요즘 빅데이터 열풍이 불면서 ‘추천서비스’에 대한 이야기가 많이 들린다. 데이터를 분석해서 사용자에게 필요한 정보를 ‘맞춤형’으로 제공하고자 하는 시도는 여러곳에서 수행되고 있으며 그중에서 가장 많이 사용하는 분석 방법중 한가지가 ‘상관관계 분석’이다. 통계학도가 아니여도 ‘상관관계 분석’이라는 말은 데이터 분석에 관심이 있다면 한번쯤은 들어 보았을 것이다. 간단하게 ‘상관관계 분석’이 어떤 것인지 알아보고 이를 데이터 분석에 활용하는 방법에 대해 알아보자.
<br/><br/><br/>
### 4.1 상관분석과 상관계수
<br/><br/>
우리는 일상생활에서 “키가 크면 발이 크다”, “교육수준이 높을수록 자녀의 대학 진학률이 높다”등의 이야기를 많이 한다. 이러한 두 개의 변수 ‘키’와 ‘발’, ‘교육수준’과 ‘자녀 대학 진학률’간의 관계가 어떠한 관계를 가지고 있는지 성향을 분석하는 것이 “상관분석”이다. 이를 ‘산포도’ 또는 ‘산점도’라는 그래프로 그리면 직관적으로 두 변수간의 관계를 파악할 수 있다.
<br/><br/>
만약 어느 집단의 키와 발크기를 조사하였더니 다음의 표와 같이 정리가 되었다고 하자(극단적인 예를 만든것임).
<br/><br/>

|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|
| 키(Cm) | 100 | 120 | 130 | 140 | 150 | 160 | 170 | 180 | 190 |
| 발크기(mm) | 200 | 205 | 210 | 220 | 230 | 250 | 270 | 280 | 285 |
{: .table table-striped}
<br/>
직관적으로 표만 보아도 키가 커지면 발크기가 커지는 것을 확인할 수 있다. 이를 산포도로 그려보기 위하여 다음과 같이 코드를 작성해 보자.

{% highlight python %}
>>> import matplotlib.pyplot as plt
>>> from matplotlib import font_manager, rc
>>> import matplotlib
>>> font_location = "c:/Windows/fonts/malgun.ttf"
>>> font_name = font_manager.FontProperties(fname=font_location).get_name()
>>> matplotlib.rc('font', family=font_name)
>>> height = [100, 120, 130, 140, 150, 160, 170, 180, 190]
>>> foot_size = [200, 205, 210, 220, 230, 250, 270, 280, 285]
>>> plt.scatter(height, foot_size)
>>> plt.xlabel('키(Cm)')
>>> plt.ylabel('발크기(mm)')
>>> plt.show()
{% endhighlight %}

<br/>
코드를 수행하면 [그림 1]과 같이 그려진다. 그래프로 그려보니 더욱더 확실하게 증가하는 형태가 보인다.
<br/><br/>

![](/asset/study/python_visualization/18.png)
[그림 1] x가 증가함에 따라 y가 증가하는 경우
{: .borderBox}

<br/>
다음으로 산의 높이에 따른 온도의 변화를 표로 나타내보자(일반적으로 산의 높이가 100m 올라갈때마다 평균 0.65℃ ~ 0.75℃ 정도 낮아진다고 한다)
<br/><br/>

|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|
| 산의 높이(m) | 100 | 200 | 300 | 400 | 500 | 600 | 700 | 800 | 900 |
| 온도(℃) | 18.0 | 17.5 | 17 | 16.5 | 15 | 14.5 | 13 | 12 | 11 |
{: .table table-striped}
<br/>
위의 표를 이용하여 산포도를 그려보자.

{% highlight python %}
>>> height = [100, 200, 300, 400, 500, 600, 700, 800, 900]
>>> temperature = [18.0, 17.5, 17, 16.5, 15, 14.5, 13, 12, 11]
>>> plt.scatter(height, temperature)
>>> plt.xlabel('산의높이(m)')
>>> plt.ylabel('온도(℃)')
>>> plt.show()
{% endhighlight %}

<br/><br/>

![](/asset/study/python_visualization/19.png)
[그림 2] 산의 높이에 따른 온도의 변화
{: .borderBox}

<br/>
[그림 2]에서 보듯이 산의 높이가 높아질수록 온도가 낮아짐을 확인할 수 있다. 다음의 코드를 작성해 보자.

{% highlight python %}
>>> import numpy as np
>>> random_x = np.random.random_integers(0, 100, 50)
>>> random_y = np.random.random_integers(0, 100, 50)
>>> plt.scatter(random_x, random_y)
>>> plt.show()
{% endhighlight %}

<br/>
위의 코드를 생성하면 [그림 3]과 같이 어떤 관계가 보이지 않는 데이터가 나타난다.
<br/><br/>

![](/asset/study/python_visualization/20.png)
[그림 3] 데이터간의 관계가 보이지 않는 경우
{: .borderBox}

<br/>
이 예제에서는 데이터 연관성을 없애기 위하여 numpy모듈의 random_interger()함수를 사용하였다. ‘random_interger(arg1, arg2, number)’는 ‘arg1’과 ‘arg2’사이의 임의의 난수를 ‘number’ 개수만큼 만들어준다.
<br/><br/>
상관분석에서는 x의 값이 증가함에 따라 y의 값이 증가하는 경우 우리는 “양의 상관관계”를 가지고 있다고 이야기하며(키 vs. 발크기), 반대로 x가 증가함에 따라 y의 값이 감소(산의 높이 vs. 온도)하는 경우 “음의 상관관계”를 가지고 있다고 이야기한다.
<br/><br/>
상관 계수는 서로간의 데이터가 어느 정도의 근접도를 가지고 있는지 표현하는 방법이다. [그림 4]는 둘 다 양의 상관관계를 가지고 있으나 두 점들이 모여있는 근접도(밀도)가 차이를 가진다.
<br/><br/>

![](/asset/study/python_visualization/21.png)
[그림 4] 양의 상관관계를 가지고 있으나 근접도가 다른 경우
{: .borderBox}

<br/>
이러한 근접도를 함께 포함하도록 표현한 방법이 “상관계수(기호로는 r)”이다. 상관계수는 -1≤r≤1 의 값을 가지며, 수치가 0에 근접할 수록 두 변수간에 상관관계가 없음을 의미하고, -1의 경우에는 음(-)의 상관 관계가 강하며, +1에 근접할수록 양(+)의 상관관계가 크다고 본다.
<br/><br/>
일반적으로 각 상관계수 값은 다음과 같은 의미로 해석된다(음의 상관계수는 절대값을 붙여 생각하면 된다). 이 해석은 분석하는 사람에 따라 편차가 있다.
<br/><br/>

|--------|--------|
| 0.0 ~ 0.2 | 상관 관계가 거의 없다 |
| 0.2 ~ 0.4 | 약한 상관 관계 |
| 0.4 ~ 0.6 | 상관 관계가 있다 |
| 0.6 ~ 0.8 | 강한 상관 관계 |
| 0.8 ~ 1.0 | 매우 강한 상관 관계 |
{: .table table-striped}
<br/>
상관관계 분석식은 다음과 같이 표현된다.
<br/><br/>

![](/asset/study/python_visualization/21_1.png)
{: .borderBox}

<br/><br/><br/>
### 4.2 데이터 테이블 생성: pandas 패키지
<br/><br/>
‘pandas’ 패키지는 데이터를 엑셀 스프레드시트(excel spreadsheet)나 SQL 테이블과 같이 서로 다른 속성을 가지는 컬럼들로 구성된 데이터 테이블 형태(DataFrame)이나 시계열 데이터와 같이 연속된 데이터 셋(Series)으로 구성하고, 이를 분석하기 위한 도구로 사용된다.
<br/><br/>
‘pandas’ 패키지는 데이터 구조를 위하여 일차원 데이터인 ‘Series’와 이차원 데이터인 ‘DataFrame’을 사용한다. 먼저 파이썬 에뮬레이터를 실행하고 ‘pandas’와 ‘numpy’를 ‘import’한다. ‘numpy’는 파이썬을 이용하여 과학 계산과 같은 고성능 수치계산을 위해 만들어진 패키지로써 다차원 배열을 사용하기 위하여 주로 사용한다(‘numpy’에 대한 활용은 필요한 부분에서만 잠시 하겠다).

{% highlight python %}
>>> import numpy as np
>>> import pandas as pd
{% endhighlight %}

<br/><br/><br/>
#### 4.2.1 Series
<br/>
‘Series’는 이차원의 인덱스를 가지는 배열(labeled array)로써 integer, string, float, 파이썬 객체(object)등 다양한 형식의 데이터 타입을 가질 수 있으며 함수의 사용법은 다음과 같다.

{% highlight python %}
pd.Series(data, index=index)
{% endhighlight %}

<br/><br/>
1) 일차원(array) 데이터의 활용
<br/>

{% highlight python %}
>>> s = pd.Series(np.random.randn(5))
>>> s
0    0.636579
1    1.429441
2   -0.056851
3   -0.330899
4    0.690789
dtype: float64
>>> s = s = pd.Series(np.random.randn(5), index=['A','B','C','D','E'])
>>> s
A    0.449761
B   -0.288997
C   -0.263346
D    1.673738
E   -0.437203
dtype: float64
{% endhighlight %}

<br/>
두 예제에서 보듯이, 인덱스의 값을 부여하지 않으면 ‘pandas’는 자동적으로 0부터 len(data)-1까지의 값을 부여하며, 인덱스 값을 부여하는 경우에는 해당 인덱스로 매칭된다. 인덱스를 부여하는 경우에는 인덱스의 개수와 데이터의 개수가 반듯이 일치하여야 한다.
<br/><br/>
‘numpy’에서 가장 많이 사용하는 ‘ndarray’는 ‘N’차원의 배열 객체로써 기존의 파이썬은 어떠한 형식이라도 어레이에 담을 수 있으나, ‘ndarray’는 동일한 형태의 데이터만 배열에 담을 수 있는 차이점을 가지고 있다. 우리가 자료를 임의로 생성하여는 경우가 많으므로 난수(random) 데이터를 얻는 함수에 대해 살펴보자
<br/><br/>

|--------|--------|
| rand(d0, d1, ..., dn) | N차원 배열의 난수 발생 |
| randn(d0, d1, ..., dn) | 표준 정규분포에 따른 N차원 난수 발생 |
| randint(low[, high, size]) | low 이상 high 미만의 정수형 난수 발생 |
| random_integers(low[, high, size]) | low이상 high이하의 정수형 난수 발생 |
| random_sample([size])<br/>random([size])<br/>ranf([size])<br/>sample([size]) | 0.0이상 1.0미만의 실수형 난수 발생 |
| choice(a[, size, replace, p]) | 주어진 1차원 배열을 기반으로 무작위 샘플 추출 |
| bytes(length) | 바이트형 난수 발생 |
{: .table table-striped}
<br/>
2) 딕셔너리(Dictionary)
<br/>

{% highlight python %}
>>> d = {'a' : 0., 'b' : 1., 'c' : 2.}
>>> pd.Series(d)
a    0.0
b    1.0
c    2.0
dtype: float64
>>> pd.Series(d, index=['a', 'b', 'B', 'c'])
a    0.0
b    1.0
B    NaN
c    2.0
dtype: float64
{% endhighlight %}

<br/>
딕셔너리 형태의 데이터는 인덱스를 부여하지 않으면 딕셔너리 키값이 인덱스로 들어가며, 만약 ‘index값을 지정하는 경우에는 데이터 값에서 해당 인덱스의 값만을 가지고 들어온다. 위의 예에서 ‘d’인덱스에는 ‘B’가 없으므로 생성된 시리즈에는 ‘B’의 값이 ‘NaN(Not a Number)‘으로 들어가 있게 된다.
<br/><br/>
3) 스칼라(Scalar) 값
<br/>

{% highlight python %}
>>> pd.Series(7, index=['a', 'b', 'c', 'd', 'e'])
a    7
b    7
c    7
d    7
e    7
dtype: int64
{% endhighlight %}

<br/>
스칼라 값을 이용하여 초기화를 하는 경우에는 반듯이 인덱스가 들어가야 한다.
<br/><br/><br/>
생성된 데이터의 값을 가지고 오는 방법은 배열에서 데이터를 가지고 오는 방법과 동일한 형식을 가지고 있으며 데이터 연산이 다음과 같이 가능하다.
<br/><br/>

{% highlight python %}
>>> s = pd.Series([1,2,3,4,5], index=['a', 'b', 'c', 'd', 'e'])
>>> s[0]
1
>>> s[:3]
a    1
b    2
c    3
dtype: int64
>>> s[[4,1]]
e    5
b    2
dtype: int64
>>> np.power(s, 2)
a     1
b     4
c     9
d    16
e    25
dtype: int64
{% endhighlight %}

<br/><br/><br/>
#### 4.2.2 DataFrame
<br/>
‘DataFrame’은 가장 많이 사용하는 ‘pandas’의 객체로 엑셀과 같이 표의 형식으로 데이터를 표현할수 있어 다양한 형태로 데이터 분석에 사용된다. ‘DataFrame’은 각개의 열은 데이터 형식이 동일하지만 서로 다른 열끼리는 데이터의 형식이 달라도 되기 때문에 하나의 테이블 형태로 데이터를 표현하기에 적합한 형태이다.
<br/><br/>
1) Series/Dict 데이터의 활용
<br/>

{% highlight python %}
>>> d = {'one' : pd.Series([1., 2., 3.], index=['a', 'b', 'c']),
         'two' : pd.Series([1., 2., 3., 4.], index=['a', 'b', 'c', 'd'])}
>>> d
{'two': 
a    1.0
b    2.0
c    3.0
d    4.0
dtype: float64, 
'one': 
a    1.0
b    2.0
c    3.0
dtype: float64}
>>> df = pd.DataFrame(d)
>>> df
   one  two
a  1.0  1.0
b  2.0  2.0
c  3.0  3.0
d  NaN  4.0
>>> d = {'one' : pd.Series([1., 2., 3.]),
         'two' : pd.Series([1., 2., 3., 4.])}
>>> df = pd.DataFrame(d)
>>> df
   one  two
0  1.0  1.0
1  2.0  2.0
2  3.0  3.0
3  NaN  4.0

>>> d = {'one' : [1., 2., 3., 4.],
         'two' : [4., 3., 2., 1.]}
>>> df = pd.DataFrame(d)
>>> df
   one  two
0  1.0  4.0
1  2.0  3.0
2  3.0  2.0
3  4.0  1.0
{% endhighlight %}

<br/>



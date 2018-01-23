---
layout: post_study
title:  5. 히트맵(Heatmap) – seaborn, numpy
date: 2018-01-21 13:01:00
categories: python_visualization
---
## 5. 히트맵(Heatmap) – seaborn, numpy
<br/><br/><br/>
‘seaborn’은 ‘matplotlib’와 함께 효과적인 시각화 라이브러리를 제공해준다. ‘seaborn’을 사용하기위해서는 ‘numpy’와 ‘scipy’가 설치되어야 하는데, 윈도우에서는 pip install을 사용하는 경우 설치오류가 발생하는 경우가 많다.
<br/><br/>
먼저 numpy-mkl 버전을 인스톨 한다. numpy mkl 버전은 ‘numpy’와 인텔의 수학 커널 라이브러리를 지원하는 dll 파일들이 함께 포함되어 있는 패키지이다. 인터넷을 검색하여 numpy mkl 버전을 다운로드 받는다(http://www.lfd.uci.edu/~gohlke/pythonlibs/#numpy). 
<br/><br/>
‘.whl’ 파일의 경우에는 사용하고 있는 파이썬 버전(cpXX로 표시)과 윈도우 32/64 비트를 개발 환경에 맞게 다운로드 받는다. 동일한 형식으로 ‘scipy’ 및 ‘seborn’ 파일을 받고 인스톨 한다(필자의 경우에는 파이썬 3.5 = CP25-의 64비트 버전을 다운로드받아 D:\Temp 폴더에 저장하였고, Python 설치 경로는 D:\Program Files\Python 이다). 패키지 인스톨에 대하여 잘 모르는 경우에는 [파이썬 기초 >> 7. 패키지(Package) 설치](https://ericnjennifer.github.io/python_basic/2017/12/28/PythonBasic_Chapt8.html) 부분 참조 ^^

{% highlight python %}
D:\Program Files\Python\pip install d:\temp\numpy-1.12.1+mkl-cp35-cp35m-win_amd64.whl
Processing d:\program files\python\scripts\numpy-1.12.1+mkl-cp35-cp35m-win_amd64.whl
Installing collected packages: numpy
Successfully installed numpy-1.12.1+mkl

D:\Program Files\Python>pip install d:\temp\scipy-0.19.0-cp35-cp35m-win_amd64.whl
Processing d:\temp\scipy-0.19.0-cp35-cp35m-win_amd64.whl
Requirement already satisfied: numpy>=1.8.2 in d:\program files\python\lib\site-
packages (from scipy==0.19.0)
Installing collected packages: scipy
Successfully installed scipy-0.19.0

D:\Program Files\Python>pip install d:\temp\seaborn-0.7.1-py2.py3-none-any.whl
Processing d:\temp\seaborn-0.7.1-py2.py3-none-any.whl
Installing collected packages: seaborn
Successfully installed seaborn-0.7.1

D:\Program Files\Python>
{% endhighlight %}

<br/>
설치가 완료되면 파이썬 쉘을 수행한 후 중국 입국자수(JSON 파일 : [중국(112)_해외방문객정보_2011_2016.json](https://ericnjennifer.github.io/asset/study/sample_data/중국(112)_해외방문객정보_2011_2016.json))를 가지고 히트맵을 작성하여 보자. 먼저 저장된 중국 입국자수 json 파일을 불러들여 데이터 프레임으로 저장한다.

{% highlight python %}
>>> import pandas as pd
>>> import json
>>> fv_CFileName = 'd:/Temp/public_data/중국(112)_해외방문객정보_2011_2016.json'
>>> jsonFV = json.loads(open(fv_CFileName, 'r', encoding='utf-8').read())
>>> china_table = pd.DataFrame(jsonFV, columns=('yyyymm', 'visit_cnt'))
>>> china_table.head()
   yyyymm  visit_cnt
0  201101      91252
1  201102     140571
2  201103     141457
3  201104     147680
4  201105     154066
{% endhighlight %}

<br/>
데이터프레임에 저장되어 있는 데이터는 연도와 월이 문자열로 결합된 ‘yyyymm’필드와 방문객수인 ‘visit_cnt’ 열로 구성되어 있다. 해당연도와 월을 가로와 세로로 가지는 표의 형태로 구성하기 위하여 ‘yyyymm’ 필드를 먼저 ‘datetime’형식으로 변환한 후 ‘year’ 컬럼과 ‘month’ 컬럼을 생성한다.

{% highlight python %}
>>> china_table.yyyymm = pd.to_datetime(china_table.yyyymm, format='%Y%m')
>>> china_table['year'] = china_table.yyyymm.dt.year
>>> china_table['month'] = china_table.yyyymm.dt.month
>>> china_table.head()
      yyyymm  visit_cnt  year  month
0 2011-01-01      91252  2011      1
1 2011-02-01     140571  2011      2
2 2011-03-01     141457  2011      3
3 2011-04-01     147680  2011      4
4 2011-05-01     154066  2011      5
{% endhighlight %}

<br/>
‘year’ 컬럼과 ‘month’ 컬럼이 생성된 것이 확인되면 이제 표 형식으로 데이터 프레임을 변경한 후 ‘saborn’을 import 한 후 ‘heatmap’ 객체를 생성하고 ‘show()’를 이용하여 그래프를 출력한다.

{% highlight python %}
>>> china_table = china_table.set_index(['month','year'])['visit_cnt'].unstack(fill_value=0)
>>> print (china_table)
year     2011    2012    2013    2014    2015    2016
month                                                
1       91252  113927  167022  196371  296708  394345
2      140571  157019  173790  249698  326295  516787
3      141457  163261  183691  276479  423768  515130
4      147680  153875  222114  335069  531947  641610
5      154066  152347  210439  278723  517031  618083
6      150119  179508  234482  399031  573852  315095
7      195188  244573  322917  569787  692053  255632
8      241987  276299  359065  642258  757683  513275
9      176196  231462  283402  483518  564078  591242
10     181428  214681  279440  343273  562278  650174
11     136152  174164  204533  276428  460671  507579
12     119061  159080  195997  276234  420501  465218
>>> import matplotlib.pyplot as plt
>>> import seaborn as sns
>>> sns.heatmap(china_table)
>>> plt.show()
{% endhighlight %}

<br/>
[그림 1]은 생성된 Heatmap을 나타낸다.
<br/><br/>

![](/asset/study/python_visualization/24.png)
[그림 1] 국내 중궁인 입국수 시각화(Heatmap)
{: .borderBox}

<br/>
---
layout: post_study
title:  1. 빈도 분석 : 문장 형태소 분석 - KoNLPy 설치하기
date: 2018-01-21 10:01:00
categories: python_visualization
---
## 1. 빈도 분석: 문장 형태소 분석 - KoNLPy
<br/><br/><br/>
자연어 처리는 우리가 사용하는 문장을 기계적으로 분석하여 컴퓨터가 이해할 수 있는 형태로 만드는 기술이라고 할 수 있다. 자연어 분석은 요즘 경쟁적으로 개발하는 인공지능(AI) 기술에서 고전적으로 요구되는 기술로써 수집한 데이터를 효율적으로 가공하기 위해서는 필수적이라 할 수 있다.
<br/><br/>
형태소 분석이란 어떤 대상 어절의 모든 가능한 분석 결과를 출력하는 것을 의미한다. 정보를 효율적으로 검색하기 위해서는 문장중 주요 단어(색인어)를 추출할 수 있어야 하며, 이를 위해서 문장중에 ‘명사’에 해당하는 부분만을 추출하여야 하는데 한글은 조사와 복합명사(명사 두개가 합쳐져 만들어진 명사)등을 분리하기가 상대적으로 영어권 문장에 비하여 힘들다.
<br/><br/>

|--------|--------|
| 복합 명사의 예 | “논밭” : ‘논’ + ‘밭’ 또는 “논밭”<br/>“눈물” : ‘눈’ + ‘물’ 또는 “눈물” |
| 조사의 예 | “나는” : ‘나’(대명사) + ‘는’(조사) |
| 어미의 예 | “나는” : ‘나’(동사: 날다) + ‘는’(관형형 어미) |
{: .table table-striped}
<br/>
위의 예에서 보듯이 다양한 형태의 활용과 의미 분석이 필요하므로 자연어 처리와 형태소 분석은 단순한 문제가 아니다. 국내에서는 이러한 형태소 분석을 효율적으로 하고 명사를 추출하기 위한 ‘코엔엘파이’, ‘은전한닢’ 등 여러 오픈소스 프로젝트가 진행되고 있으며, 본 과정에서는 “코엔엘파이(KoNLPy)”를 이용하여 형태소 분석을 진행하도록 하겠다.
<br/><br/>
<br/><br/>
### 1.1 코엔엘파이(KoNLPy)의 설치 및 활용?
<br/><br/>
본 절에서는 코엔엘파이를 설치하고 간단한 프로그램을 작성하여 정상적으로 동작하는지 확인하도록 한다. 본 과정은 윈도우를 기반으로 하기 때문에 기타 다른 OS의 경우에는 참고 문헌( http://konlpy-ko.readthedocs.io/ko/v0.4.3/install )을 확인하여 진행하기 바란다.
<br/><br/>
코엔엘 파이를 설치하기 위해서는 다음과 같은 절차가 필요하다. 만약 자바가 설치되어 있는 경우에는 1)과 2)의 과정은 수행하지 않아도 된다.
<br/><br/>
1)	JAVA 1.7 이상의 설치
<br/>
오라클(Oracle) 사이트(http://www.oracle.com/technetwork/java/javase/downloads/index.html)로 이동하여 해당 OS에 맞는 JDK를 설치한다.
<br/><br/>
2)	JAVA_HOME Path 설정
<br/>
• JDK가 정상적으로 동작하기 위하여 JAVA_HOME 경로(Path)를 설정한다. 탐색기의 “내 컴퓨터”를 선택한 후 오른쪽 마우스를 눌러 “속성”을 선택한다.
<br/>
• [고급 시스템 설정]을 선택한 후 [고급] 탭을 누르고 [환경변수]를 선택한다.
<br/>
• [시스템 변수]영역의 [새로 만들기]를 선택한 후 변수 이름이 “JAVA_HOME”을 기입하고, 변수값 부분에 JDK가 설치되어있는 경로를 입력한다.
<br/><br/>
3)	JPype1 (>=0.5.7) 설치
<br/>
‘코엔엘파이’의 경우 JAVA로 작성된 모듈을 로드하여야 하기 때문에 JPype1 0.5.7 이상이 설치되어야 한다.

{% highlight python %}
[파이썬이 설치된 경로]\\pip install jpype1
{% endhighlight %}

<br/>
만약 정상적으로 설치가 안되는 경우에는 (http://www.lfd.uci.edu/~gohlke/pythonlibs/#jpype)로 이동하여 시스템에 맞는 .whl(윈도우 wheel 파일)을 다운로드 받아 설치하기 바란다.
<br/><br/>
4)	KoNLPy 설치
파이썬이 설치된 디렉터리로 이동하여 [표 1] 과 같이 KoNLPy를 설치하고, 테스트해 보자(필자는 파이썬이 설치된 경로가 ‘D:\Program Files\Python’이다).

{% highlight python %}
D:\Program Files\Python>pip install JPype1
Collecting JPype1
  Using cached JPype1-0.6.2.tar.gz
Installing collected packages: JPype1
  Running setup.py install for JPype1 ... done
Successfully installed JPype1-0.6.2

D:\Program Files\Python>pip install KoNLPy
Collecting KoNLPy
  Using cached konlpy-0.4.4-py2.py3-none-any.whl
Installing collected packages: KoNLPy
Successfully installed KoNLPy-0.4.4

D:\Program Files\Python>python
Python 3.5.2 (v3.5.2:4def2a2901a5, Jun 25 2016, 22:18:55) [MSC v.1900 64 bit (AM
D64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> from konlpy.tag import Kkma
>>> from konlpy.utils import pprint
>>> kkma = Kkma()
>>> pprint(kkma.nouns(u'명사만을 추출하여 워드클라우드를 그려봅니다'))
['명사', '추출', '워드', '워드클라우드', '클라우드']
>>>
{% endhighlight %}

<br/>
정상적으로 설치가 되었다면 파이썬을 실행하여 Kkma 클래스의 nouns() 함수를 이용하여 문장중 명사만을 추출고 정상적으로 동작하는지 확인한다.
<br/><br/>
5)	설치가 안될 경우
<br/>
설치중 오류가 발생하거나 정상적으로 동작을 하지 않는 경우에는 다음을 살펴 보도록 한다
<br/><br/>
• JAVA_HOME의 경로 확인: 자신이 설치한 JAVA 경로와 일치하는지 확인한다.

{% highlight python %}
D:\Program Files\Python>set java
JAVA_HOME=c:\Program Files\Java\Jre1.8.0_111
D:\Program Files\Python>
{% endhighlight %}

<br/>
• “numpy” 설치: 우리는 [PYTHON 크롤링 >> 10. 공공 정보 데이터 수집하기](https://ericnjennifer.github.io/python_crawling/2018/01/21/PythonCrawling_Chapt11.html)에서 “matplotlib”를 설치하면서 “numpy” 패키지를 설치하였다. 만약 설치가 되지 않았다면 “numpy” 패키지를 설치한다.

{% highlight python %}
D:\Program Files\Python>pip list
JPype1 (0.6.2)
konlpy (0.4.4)
matplotlib (2.0.0)
numpy (1.12.0)
pip (9.0.1)
…
D:\Program Files\Python>
{% endhighlight %}

<br/>
• “dll load failed 지정된 모듈을 찾을 수 없습니다” 오류: “matplotlib” 패키지 설치시 언급하였던 Visual Studio 재배포 패키지를 설치하여 주면 해결된다.




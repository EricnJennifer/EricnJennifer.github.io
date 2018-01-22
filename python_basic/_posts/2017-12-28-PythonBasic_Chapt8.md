---
layout: post_study
title:  7. 패키지(Package) 설치
date: 2017-12-28 08:01:00
---
## 7. 패키지(Package) 설치
<br/><br/><br/>
PIP란 파이썬으로 작성된 패키지 패키지(package: 특정 기능을 수행하기 위하여 만들어 놓은 모듈-라이브러리-) 소프트웨어를 설치, 관리하는 시스템이다. 여기서 패키지는 특정 기능을 수행하기 위한 함수들을 모아놓은 라이브러리(모듈)을 의미한다. 파이썬은 2.7.9와 3.4 버전 이상에서는 기본적으로 포함되어 있다.
<br/><br/>
만약 프로그램을 설치하는 도중에 pip를 설치하는 것에 동의하지 않았다면 수작업으로 pip 설치하여야 한다 (easy_install.exe 라는 프로그램이 Python 설치 디렉터리 밑에 /Scrips에 있을 것이다. easy_isntall pip 라고 하면 pip가 설치된다)
<br/><br/><br/>
### 7.1 설치된 패키지 확인하기
<br/><br/>
'pip'를 활용하기 위해서는 명령 프롬프트(도스창)를 사용한다. [그림 1]처럼 ‘cmd’ 명령을 이용하여 도스창을 띄운다.
<br/><br/>

![](/asset/study/python_basic/3/1.png)
[그림 1] 명령 프롬프트(도스창) 실행
{: .borderBox}

<br/>
도스창에서 ‘pip list’ 명령을 입력하면 [그림 2]와 같이 현재 설치되어 있는 패키지 리스트가 나타난다.
<br/><br/>

![](/asset/study/python_basic/3/2.png)
[그림 2] 설치된 패키지 확인: pip list
{: .borderBox}

<br/>
파이썬을 설치한 초기 상태에서는 [그림 2]와 같이 pip(7.1.2)와 setuptools(18.2)만이 설치되어 있으며 현재 설치되어 있는 PIP 모듈은 버전이 7.1.2이며, 9.0.1 버전으로 업그레이드가 가능하다고 친절하게 나타난다. PIP 버전이 낮은 경우 패키기 설치시 오류가 발생하는 경우가 많이 발생하므로 항상 최신 버전으로 업데이트 하는 것이 필요하다.
<br/><br/><br/>
### 7.2 설치된 패키지 업데이트
<br/><br/>
[그림 2]에서 나타난 것처럼 PIP 버전을 업데이트 하기 위하여 ‘pip install --upgrade pip’ 명령을 실행한다.
<br/><br/>

![](/asset/study/python_basic/3/3.png)
[그림 3] PIP 버전 업데이트: pip install --upgrade pip
{: .borderBox}

<br/>
[그림 3]에서 설치가 완료되면 다시 PIP 버전을 확인하기 위하여 ‘pip list’를 실행하면 [그림 4]와 같이 업데이트 된 사항을 확인할 수 있다. PIP 버전이 업데이트 되면서 목록을 확인하는 방법이 변경되었다는 메시지가 출력된다. 기존형식으로 보기 위해서는 --format=legacy 형식을 인자로 전달하고, 변경된 형식은 --format=columns을 인자로 전달한다.
<br/><br/>

![](/asset/study/python_basic/3/4.png)
[그림 4] 업데이트 된 패키지의 확인
{: .borderBox}

<br/><br/><br/>
### 7.3 패키지 설치
<br/><br/>
새로운 패키지를 설치하기 위해서는 ‘pip install [패키지명]’을 이용한다. 예제로 데이터를 수신하기위해 많이 사용하는 ‘request’ 패키지를 설치하여 보자. 도스창에서 ‘pip install request’라고 입력한 후 실행하면 [그림 5]와 같이 설치가 된다. 설치된 버전을 확인하기 위하여 ‘pip list --format=columns’를 실행한다.
<br/><br/>

![](/asset/study/python_basic/3/5.png)
[그림 5] 패키지 설치의 예
{: .borderBox}

<br/><br/><br/>
### 7.4 .whl 파일의 설치
<br/><br/>
설치하여야 할 패키지가 PIP INSTALL을 이용하여 설치가 되지 않는 경우가 가끔 발생한다. 이 겨웅에는 해당 패키지 웹사이트에서 파이썬 버전에 맞는 .whl 파일을 다운로드 받은 후 다음과 같이 실행을 한다.
<br/><br/>
> 참고 : scipy-1.0.0-cp35-none-win32.whl 라는 .whl 파일이 있으면 cp35가 파이썬 3.5.X 버전이라는 의미이다

<br/><br/>

{% highlight python %}
C:\>pip install [.whl 파일 경로] 또는
C:\>python –m pip install [.whl 파일 경로]
{% endhighlight %}

<br/>
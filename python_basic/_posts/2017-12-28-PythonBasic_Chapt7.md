---
layout: post_study
title:  6. 모듈(module)
date: 2017-12-28 07:01:00
---
## 6. 모듈(module)
<br/>
<br/>
앞장에서 배운 함수는 하나의 프로그램에서 사용하였지만 모듈은 미리 함수, 변수 등을 파일로 작성해 해 놓고 필요할 때 마다 포함시켜 사용하는 방식을 이야기한다. 실제로 우리는 파이썬 프로그래밍을 하면서 다른 사람들이 미리 만들어 놓은 모듈들을 많이 가져다 사용하게 되는데 사용하면서 미리 수고를 해 준 많은 개발자들에게 고마움을 조금이라도 느꼈으면 좋겠다. 다음과 같이 모듈을 작성하고 파일명을 my_module.py로 특정 위치에 저장하자(필자의 경우 c:\python_sample에다 저장하였다).
<br/>
<br/>

{% highlight python %}
def sum(*args):
    result = 0
    for i in args:
        result = result + i
    return result
{% endhighlight %}

<br />
<br />
이제 명령 프롬프트를 실행해서 파일을 저장한 위치로 이동한 후 다음과 같이 테스트한다.
<br />
<br />

![](/asset/study/python_basic/2/20.png)
    [그림 1] 모듈의 작성 및 활용
{: .borderBox}

<br />
<br />
위의 예에서 작성한 모듈을 불러오기 위하여 ‘import’라는 명령어를 사용한다. 이때 불러올 모듈의 파일명 뒤 ‘.py’는 기입하지 않는다. 모듈이 불려지면 ‘모듈명.함수명’의 형태로 불러다 언제든지 사용이 가능하다. 모듈명이 긴 경우 별명을 붙여 사용할 수 있는데, 그 경우에는 ‘as’를 이용한다.
<br />
<br />
만약 특정 모듈의 특정한 함수만 사용하게 되는 경우에는 ‘from [모듈명] import [함수명]’의 형태로 사용하면 ‘[모듈명].’을 표기하지 않아도 된다.
<br />
<br />

### 6.1 모듈 __name__
<br/>
<br/>
모듈 파일은 ‘.py’ 확장자를 가지고 있으며, python 실행시 바로 호출될 수 있다. 다음과 같이 코드를 수정하고 my_module.py 이름으로 저장한다.
<br/>
<br/>

{% highlight python %}
def sum(*args):
    result = 0
    for i in args:
        result = result + i
    return result

print (sum(1,2,3))
{% endhighlight %}

<br/>
<br/>
이제 명령 프롬프트를 실행해서 파일을 저장한 위치로 이동한 후 다음과 같이 테스트한다.
<br/>
<br/>

![](/asset/study/python_basic/2/21.png)
    [그림 2] import 와 직접 실행의 차이
{: .borderBox}

<br/>
<br/>
파이썬에서 특정 파일을 불러오는 경우에는 명령 프롬프트에서 ‘python [파일명]’과 같이 실행할 수 있다. 앞서 작성한 예의 경우에는 모듈 내부에 print()문으로 수행하는 것이 있다. 이 파일을 만약 모듈로 사용한다면 import시에 print()문이 실행되고 만다. 이러한 것을 막기 위하여 다음과 같이 코드를 수정한다.
<br/>
<br/>

{% highlight python %}
def sum(*args):
    result = 0
    for i in args:
        result = result + i
    return result

if __name__ == "__main__":
    print (sum(1,2,3))
{% endhighlight %}

<br/>
<br/>
코드를 저장한 후 실행하여 보면 다음과 같다.
<br/>
<br/>

![](/asset/study/python_basic/2/22.png)
    [그림 3] __name__ == __main__의 활용
{: .borderBox}

<br />
<br />
호출되는 모듈이 직접 실행이 되는 경우에는 ‘__name__’ 이 ‘__main__’되어 실행되지만 모듈로서 기능을 하게 되는 경우에는 다른 프로그램이 __main__이 되므로 모듈내의 print()문이 동작하지 않게 되는 것이다.




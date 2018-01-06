---
layout: post_study
title:  5. Facebook 데이터 수집
date: 2018-01-05 06:01:00
categories: python_crawling
---
## 5. Facebook 데이터 수집
<br/><br/>

우리는 [1. Facebook API 사용등록](https://ericnjennifer.github.io/python_crawling/2018/01/05/PythonCrawling_Chapt2.html)에서 페이스북 App을 설정하였다. 이제 해당 앱 ID와 비밀키를 가지고 파이썬을 이용하여 데이터를 가지고 오는 방법에 대하여 설명하고자 한다.
<br/><br/>
일반적으로 근래 서비스되는 모든 웹 서비스는 OAuth라는 인증 방식을 통하여 사용자 계정에 대한 접근을 하게 된다. 그러나 페이스북은 OAuth2 방식을 채용하면서 기본적으로 ‘https’ 프로토콜을 적용하고 편리한 방식의 그래프 API를 이용해 페이스북에 접근하는 방식을 제공한다. 그래프 API는 “소셜 그래프”라는 명칭에서 유래된 것으로 다음 항목을 가지고 있다.
<br/>

| 항목 | 설명 |
|--------|--------|
| 노드(Node) | 기본적으로 사용자, 사진, 페이지, 댓글과 같은 항목(item)을 포함한다 |
| 에지(Edge)| 페이지의 사진, 사진의 댓글 등 각 항목간의 연결 링크 |
| 필드(Field) | 페이지 정보, 해당 페이지 프로필(Profile)등 항목에 대한 정보 |
{: .table table-striped}

<br/>
그래프 API는 HTTP 기반으로 설계되어 쉽게 이해할 수 있으며 웹 브라우저에서도 동작할 수 있는 장점을 가지고 있으나 보안성이 취약한 단점을 가지고 있다. 보안이 필요한 경우에는 OAuth 인증 방식을 사용하는 것을 권장하나 본 책의 범위를 넘어서는 관계로 여기서는 그래프 API의 엑세스 토큰을 전달하는 방식을 사용한다.
<br/><br/>
<br/><br/>

### 5.1 페이스북 그래프(Graph) API
<br/><br/>

그래프(Graph) API는 페이스북의 소셜 그래프에서 데이터를 가지고 오거나 게시하는 기본적인 방법을 제공하는 HHTP 기반의 API이다. 그래프 API는 모든 노드(Node)와 에지(Edge)에 관하여 HTTP GET 요청을 보내기만 하면 해당 데이터를 조회할 수 있다.
<br/><br/>
<br/><br/>
#### 5.1.1 읽기
<br/><br/>
```
GET /[version info]/[Node|Edge Name]
Host: graph.facebook.com
```
<br/><br/>
일반적인 API 호출은 액세스 토큰(access token)을 사용하여 접근할 수 있으며, 노드나 에지의 특성에 따라 요청 토큰에 따른 권한이 필요하다. 예를 들어 “/me” 노드를 요청하는 경우에는 사용자에 대한 아이디(user_id) 또는 페이스북의 page_id를 반환하며,  “/me/photos”를 요청하게 되는 경우 해당 사용자의 사진을 모두 가지고 올 수 있다. 반환의 형식은 아래와 같이 fieldname에 해당하는 키값과 그에 해당하는 field 값의 쌍으로 JSON 형식으로 반환한다.
<br/><br/>

```
{
	“fieldname” : {field-value},
	…
}
```
<br/><br/>

#### 5.1.2 페이지 조회
<br/><br/>
페이스북은 시간의 흐름(timline)을 기준으로 작성된다. 일반적으로 사용자는 단순하게 단일 데이터를 요청하지 않고 여러 개의 데이터를 조회할 수 있으며, 이를 위하여 다음과 같은 페이징 기법을 제공한다.
<br/><br/>

1) 커서(cursor) 기반 페이지 조회
<br/>
커서 기반 페이지는 일반적인 페이징 기법으로 이전페이지에 접근할 수 있는 커서값(before)과 다음 페이지(after)에 접근할 수 있는 커서값을 제공해준다. 커서값을 인자로 전달하는 경우 해당 페이지로 이동한다. 커서 페이지 메김을 하는 경우 다음과 같은 데이터가 JSON 형식으로 반환된다. 페이스북은 친절하게 이전 페이지와 다음페이지를 요청할 수 있는 키 값을 “previous”와 “next”로 제공해 준다.
<br/><br/>
커서의 값은 중간에 뉴스 피드가 삭제되거나 추가되는 경우 페이지 값이 변경될 수 있으므로 저장된 값으로 사용해서는 안된다. 응답되는 JSON의 형식은 다음과 같다.
<br/><br/>

```
{
  "data": [
     ... 획득한 데이터
  ],
  "paging": {
    "cursors": {
      "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
      "before": "NDMyNzQyODI3OTQw"
    },
    "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw"
    "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
  }
}
```
<br/><br/>
전달되는 인자는 다음과 같은 특성을 가진다.
<br/><br/>

| 인자 | 설명 |
|--------|--------|
| before | 반환된 데이터 페이지의 처음을 가리키는 커서 |
| after| 반환된 데이터의 끝을 가리키는 커서 |
| limit| 반환하는 데이터의 최대 개수. 최대 개수보다 데이터가 작게 수신된 경우 더 이상의 데이터가 없다고 판단할 수 있으나, 중간 뉴스 피드가 비공개 처리 된 경우 등이 있어 데이터의 반환이 적을 수 있으므로 반드시 “next”의 값이 없는 경우에 더 이상의 데이터가 없다고 판단하여야 한다 |
| next | 데이터의 다음 페이지를 반환하는 그래프 API URL. 해당 값이 없는 경우에는 더 이상 조회할 데이터가 없음을 의미한다 |
| previous | 데이터의 이전 페이지를 반환하는 그래프 API URL |
{: .table table-striped}

<br/><br/>

2) 시간(time period) 기준 페이지 조회
<br/>
시간 기준은 조회 시작 시간과 조회 종료 시간을 지정함으로써 데이터를 획득한다. 응답되는 데이터의 JSON 형식은 다음과 같다.
<br/><br/>

```
{
  "data": [
     ... 획득한 데이터
  ],
  "paging": {
    "previous": "https://graph.facebook.com/me/feed?limit=25&since=1364849754",
    "next": "https://graph.facebook.com/me/feed?limit=25&until=1364587774"
  }
}
```
<br/><br/>
전달되는 인자는 다음과 같은 특성을 가진다. 단 최대 since와 until의 값의 범위는 6개월 이내이어야 한다.
<br/><br/>

| 인자 | 설명 |
|--------|--------|
| until | 조회 종료 시간을 의미하는 Unix 타임스탬프 값 |
| since| 조회 시작 시간을 의미하는 Unix 타임스탬프 값 |
| limit| 반환하는 데이터의 최대 개수 |
| next | 데이터의 다음 페이지를 반환하는 그래프 API URL. 해당 값이 없는 경우에는 더 이상 조회할 데이터가 없음을 의미한다 |
| previous | 데이터의 이전 페이지를 반환하는 그래프 API URL |
{: .table table-striped}

<br/><br/>

3) 오프셋(offset) 기반 페이지 조회
<br/>
시간 순서등에 상관없이 특정 개수의 개체를 반환하고자 할 때 사용할 수 있다. 매뉴얼 상에는 에지(Edge)가 커서 또는 시간 기준 페이지 조회가 되지 않는 경우에만 사용 가능하다고 되어 있다.
<br/><br/>
<br/><br/>

### 5.2 페이스북 ID 가지고 오기
<br/><br/>
페이스북은 각자 계정을 만들게 되면 고유 URL을 부여한다. [그림 1]의 예에서 처럼 JTBC 뉴스의 접근 URL은 http://www.facebook.com/jtbcnews로 지정되어 있는 것을 확인할 수 있다. 여기에서 페이지 고유 URL은 “jtbcnews” 임이 확인 가능하다.
<br/><br/>

![](/asset/study/python_crawling/3/1.jpg)
[그림 1] 페이스북 접근 URL
{: .borderBox}

<br/><br/>
그러나 아쉽게도 페이스북 API는 고유 이름을 이용하여 접근할 수 있는 방법을 제공하지 않는다. 페이지의 접근을 위해서 페이스북에서는 고유의 숫자형식 ID(Numeric ID)를 사용하게 되므로 이를 위해 그래프 API를 이용하여 해당 페이지의 숫자 형식 ID를 가지고 와야 한다. 아래와 같이 [코드 1]을 작성한다

{% highlight python %}
import sys
import urllib.request
import json

if __name__ == '__main__':
    # [CODE 1]
    page_name = "jtbcnews"
    app_id = "[App ID]"
    app_secret = "[App Secret Code]"
    access_token = app_id + "|" + app_secret

    # [CODE 2]
    # https://graph.facebook.com/v2.8/[page_id]/?access+token=[App_ID]|[Secret_Key]
    # 형식의 문자열을 만들어 낸다

    base = "https://graph.facebook.com/v2.8"
    node = "/" + page_name
    parameters = "/?access_token=%s" % access_token
    url = base + node + parameters

    # [CODE 3]
    req = urllib.request.Request(url)

    # [CODE 4]
    try:
        response = urllib.request.urlopen(req)
        if response.getcode() == 200:
            data = json.loads(response.read().decode('utf-8'))
            page_id = data['id']
            print ("%s Facebook Numeric ID : %s" % (page_name, page_id))
    except Exception as e:
        print (e)
{% endhighlight %}

<br/><br/>
해당 코드를 수행하면 [그림 2]와 같이 Numeric ID를 수신한 것을 확인할 수 있다.
<br/><br/>

![](/asset/study/python_crawling/3/2jpg)
[그림 2] Numeric ID 획득 결과
{: .borderBox}

<br/><br/>
우리는 위와 같은 코드를 이용하여 “jtbcnews”에 접근할 수 있는 ID값을 얻게 되었다. 코드에 대해 자세히 살펴보면 다음과 같다.

{% highlight python %}
# [CODE 1]
    page_name = "jtbcnews"
    app_id = "[App ID]"
    app_secret = "[App Secret Code]"
    access_token = app_id + "|" + app_secret
{% endhighlight %}

<br/><br/>
먼저 접근할 page의 ID와 할당받은 App ID, App Secret을 변수로 지정한 후 접근 인증을 얻기위한 Token을 생성한다. 페이스북에서는 다양한 형식의 인증방식을 제공해 준다. 그래프 API에서는 단순하게 앱 ID와 앱 시크릿을 구분자인 “|”를 이용하여 연속적인 파라미터로 전송함으로써 인증을 완료하는 편의성을 제공한다. 단, 보안성이 요구되는 프로그램에서는 이렇게 쿼리 파라미터로 데이터를 전송하는데 제약을 가질 수 있다.
<br/><br/>
[CODE 1]에서 [App ID]와 [App Secret_Code]는 페이스북 App을 생성하면서 부여 받은 앱 ID와 비밀키를 의미한다.

{% highlight python %}
# [CODE 2]
    # https://graph.facebook.com/v2.8/[page_id]/?access_token=[App_ID]|[Secret_Key]
    # 형식의 문자열을 만들어 낸다

    base = "https://graph.facebook.com/v2.8"
    node = "/" + page_name
    parameters = "/?access_token=%s" % access_token
    url = base + node + parameters
{% endhighlight %}

<br/><br/>
본 책이 쓰여지는 시점에서 사용한 그래프 API의 버전은 2.8(2017년 2월 21일 현재)이며, 해당 베이스 URL 뒤에 Node와 파라미터를 부여하여 쿼리 문자열을 작성한다.
```
https://graph.facebook.com/v2.8/[page_name]/?access+token=[App_ID]|[App_Secret_Code]
```
<br/><br/>
기본적으로 페이스북을 통하여 해당 Numeric ID를 얻어가지고 오는 방법은 그래프 API URL 뒤에 질의하고자 하는 페이지 id와 억세스 토큰(access token)을 쿼리 파라미터(query partameters)로 전달하면 JSON 형태의 데이터로 응답 받게 된다.

{% highlight python %}
# [CODE 3]
    req = urllib.request.Request(url)
{% endhighlight %}

<br/><br/>
“urllib.request” 모듈은 URL을 이용하여 고수준(high level)의 HTTP 클라이언트 인터페이스를 위한함수(function)와 클래스(class)제공하며 “Request” 클래스는 URL 요청을 하기 위한 방법을 제공한다. “Urllib.request.Request()” 생성자는 다음과 같은 방식으로 설정한다.

```
urllib.request.Request(url, data=None, headers={}, origin_req_host=None, unverifiable=False, method=None)
```
<br/>

| 인자 | 설명 |
|--------|--------|
| url | 접근할 페이지 URL |
| data| 서버로 전송할 부가적인 데이터를 지정한다. 전송할 데이터는 바이트열(bytes)이나, 전송할 파일, POST 데이터 등이 될 수 있으며 Header에서 지정하는 Content-Length와 Transfer-Encoding 속성에 의하여 전송할 데이터 양을 지정한다. POST 데이터의 경우에는 urllib.parse.urlencode() 함수를 이용하여 데이터 값을 인코딩한 후 전송하여야 한다 |
| headers| 헤더는 딕셔너리(dictionary) 형식을 가지며, add_header() 함수를 이용하여 키(key)값과 그에 해당하는 값(value)의 쌍을 가진다. 예를 들어 POST 형식의 데이터를 전송하게 되는 경우에는 Content-Type: application/x-www-form-relencoded가 지정되어야 한다(실제 이 값은 기본값이다) |
| origin_req_host | RFC 2965 기반의 요청한 호스트의 호스트 명 또는 IP 어드레스를 지정한다. 기본값은 http.cookies.requst_host(self) 값이다 |
| unverifiable | 해당 요청에 대하여 재 확인(검증)이 요청되는 경우에 지정한다. 기본값은 ”False”이다. 쿠키의 값 인증 등을 위해 사용한다 |
| method | HTTP 요청의 방식을 지정한다. 기본값은 ‘GET’ 방식이다 |
{: .table table-striped}

<br/>










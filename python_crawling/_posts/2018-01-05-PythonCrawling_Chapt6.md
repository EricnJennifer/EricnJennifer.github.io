---
layout: post_study
title:  5. Facebook 데이터 수집
date: 2018-01-05 06:01:00
categories: python_crawling
---
## 5. Facebook 데이터 수집
<br/><br/><br/>
우리는 [1. Facebook API 사용등록](https://ericnjennifer.github.io/python_crawling/2018/01/05/PythonCrawling_Chapt2.html)에서 페이스북 App을 설정하였다. 이제 해당 앱 ID와 비밀키를 가지고 파이썬을 이용하여 데이터를 가지고 오는 방법에 대하여 설명하고자 한다.
<br/><br/>
일반적으로 근래 서비스되는 모든 웹 서비스는 OAuth라는 인증 방식을 통하여 사용자 계정에 대한 접근을 하게 된다. 그러나 페이스북은 OAuth2 방식을 채용하면서 기본적으로 ‘https’ 프로토콜을 적용하고 편리한 방식의 그래프 API를 이용해 페이스북에 접근하는 방식을 제공한다. 그래프 API는 “소셜 그래프”라는 명칭에서 유래된 것으로 다음 항목을 가지고 있다.
<br/><br/>

| 항목 | 설명 |
|--------|--------|
| 노드(Node) | 기본적으로 사용자, 사진, 페이지, 댓글과 같은 항목(item)을 포함한다 |
| 에지(Edge)| 페이지의 사진, 사진의 댓글 등 각 항목간의 연결 링크 |
| 필드(Field) | 페이지 정보, 해당 페이지 프로필(Profile)등 항목에 대한 정보 |
{: .table table-striped}

<br/>
그래프 API는 HTTP 기반으로 설계되어 쉽게 이해할 수 있으며 웹 브라우저에서도 동작할 수 있는 장점을 가지고 있으나 보안성이 취약한 단점을 가지고 있다. 보안이 필요한 경우에는 OAuth 인증 방식을 사용하는 것을 권장하나 본 책의 범위를 넘어서는 관계로 여기서는 그래프 API의 엑세스 토큰을 전달하는 방식을 사용한다.
<br/><br/><br/><br/>
### 5.1 페이스북 그래프(Graph) API
<br/><br/>
그래프(Graph) API는 페이스북의 소셜 그래프에서 데이터를 가지고 오거나 게시하는 기본적인 방법을 제공하는 HHTP 기반의 API이다. 그래프 API는 모든 노드(Node)와 에지(Edge)에 관하여 HTTP GET 요청을 보내기만 하면 해당 데이터를 조회할 수 있다.
<br/><br/><br/>
#### 5.1.1 읽기
<br/>

```
GET /[version info]/[Node|Edge Name]
Host: graph.facebook.com
```
<br/>
일반적인 API 호출은 액세스 토큰(access token)을 사용하여 접근할 수 있으며, 노드나 에지의 특성에 따라 요청 토큰에 따른 권한이 필요하다. 예를 들어 “/me” 노드를 요청하는 경우에는 사용자에 대한 아이디(user_id) 또는 페이스북의 page_id를 반환하며,  “/me/photos”를 요청하게 되는 경우 해당 사용자의 사진을 모두 가지고 올 수 있다. 반환의 형식은 아래와 같이 fieldname에 해당하는 키값과 그에 해당하는 field 값의 쌍으로 JSON 형식으로 반환한다.
<br/><br/>

```
{
	“fieldname” : {field-value},
	…
}
```
<br/><br/><br/>
#### 5.1.2 페이지 조회
<br/>
페이스북은 시간의 흐름(timline)을 기준으로 작성된다. 일반적으로 사용자는 단순하게 단일 데이터를 요청하지 않고 여러 개의 데이터를 조회할 수 있으며, 이를 위하여 다음과 같은 페이징 기법을 제공한다.
<br/><br/>
1) 커서(cursor) 기반 페이지 조회
<br/><br/>
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
<br/>
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
<br/>
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
<br/><br/><br/><br/>
### 5.2 페이스북 ID 가지고 오기
<br/><br/>
페이스북은 각자 계정을 만들게 되면 고유 URL을 부여한다. [그림 1]의 예에서 처럼 JTBC 뉴스의 접근 URL은 http://www.facebook.com/jtbcnews로 지정되어 있는 것을 확인할 수 있다. 여기에서 페이지 고유 URL은 “jtbcnews” 임이 확인 가능하다.
<br/><br/>

![](/asset/study/python_crawling/3/1.jpg)
[그림 1] 페이스북 접근 URL
{: .borderBox}

<br/>
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
<br/>
해당 코드를 수행하면 [그림 2]와 같이 Numeric ID를 수신한 것을 확인할 수 있다.
<br/><br/>

![](/asset/study/python_crawling/3/2.jpg)
[그림 2] Numeric ID 획득 결과
{: .borderBox}

<br/>
우리는 위와 같은 코드를 이용하여 “jtbcnews”에 접근할 수 있는 ID값을 얻게 되었다. 코드에 대해 자세히 살펴보면 다음과 같다.

{% highlight python %}
# [CODE 1]
    page_name = "jtbcnews"
    app_id = "[App ID]"
    app_secret = "[App Secret Code]"
    access_token = app_id + "|" + app_secret
{% endhighlight %}

<br/>
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

<br/>
본 책이 쓰여지는 시점에서 사용한 그래프 API의 버전은 2.8(2017년 2월 21일 현재)이며, 해당 베이스 URL 뒤에 Node와 파라미터를 부여하여 쿼리 문자열을 작성한다.

```
https://graph.facebook.com/v2.8/[page_name]/?access+token=[App_ID]|[App_Secret_Code]
```
<br/>
기본적으로 페이스북을 통하여 해당 Numeric ID를 얻어가지고 오는 방법은 그래프 API URL 뒤에 질의하고자 하는 페이지 id와 억세스 토큰(access token)을 쿼리 파라미터(query partameters)로 전달하면 JSON 형태의 데이터로 응답 받게 된다.

{% highlight python %}
# [CODE 3]
    req = urllib.request.Request(url)
{% endhighlight %}

<br/>
“urllib.request” 모듈은 URL을 이용하여 고수준(high level)의 HTTP 클라이언트 인터페이스를 위한함수(function)와 클래스(class)제공하며 “Request” 클래스는 URL 요청을 하기 위한 방법을 제공한다. “Urllib.request.Request()” 생성자는 다음과 같은 방식으로 설정한다.

```
urllib.request.Request(url, data=None, headers={}, origin_req_host=None, unverifiable=False, method=None)
```
<br/><br/>

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

{% highlight python %}
# [CODE 4]
    try:
        response = urllib.request.urlopen(req)
        if response.getcode() == 200:
            data = json.loads(response.read().decode('utf-8'))
            print ("%s Facebook Numeric ID : %s" % (page_id, data['id']))
    except Exception as e:
        print (e)
{% endhighlight %}

<br/>
“try… except” 구문은 일반적으로 어떠한 동작을 수행하면서 발생하는 오류를 처리하기 위한 블록이다. “try” 블록내의 처리중 시스템 오류가 발생하면 “except” 이하의 블록이 처리되며, “Exception”을 이용하여 해당 오류가 무엇인지 확인할 수 있다(본 책은 파이썬 프로그래밍 과정이 아니므로 ‘try…except’에 대하여 자세히 설명하지 아니한다. 좀더 자세한 사항은 파이썬 도움말을 참조하기 바란다)
<br/><br/>
urllib.request.Request()를 이용하여 생성한 req 객체를 urllib.request.urlopen()함수에 전달하여 지정한 URL에 해당하는 객체를 가지고 온다. 반환된 객체는 다음과 같은 메소드를 제공한다.
<br/><br/>

|--------|--------|
| geturl() | 응답한 서버의 URL을 포함하며, Redirect된 경우에는 Redirect 서버의 URL을 반환한다 |
| info()| 페이지의 헤더값과 같은 meta 정보를 가지고 온다 |
| getcode()| 서버의 HTTP 응답 코드를 반환한다 |
| origin_req_host | RFC 2965 기반의 요청한 호스트의 호스트 명 또는 IP 어드레스를 지정한다. 기본값은 http.cookies.requst_host(self) 값이다 |
| unverifiable | 해당 요청에 대하여 재 확인(검증)이 요청되는 경우에 지정한다. 기본값은 ”False”이다. 쿠키의 값 인증 등을 위해 사용한다 |
| method | HTTP 요청의 방식을 지정한다. 기본값은 ‘GET’ 방식이다 |
{: .table table-striped}

<br/>
반환받은 response 객체의 서버 응답코드가 정상적인 경우(200 반환)에는 수신한 데이터에서 원하는 정보를 찾기 위한 작업을 수행한다.
<br/><br/>
반환되는 값은 바이트열(Bytes)로 구성되어 있으며, 이를 해석하기에 적절한 형태로 변환하여야 한다. 앞서 이야기 했듯이 수신된 데이터는 JSON 형태를 가지고 있기 때문에 일단 수신한 데이터를 “json” 모듈에 전달하기 위하여 ‘utf-8’ 형식으로 디코딩한 후 전달한다.
<br/><br/>
정상적으로 데이터를 수신하게 되면 data는 다음과 같은 JSON 속성을 가진다.

> {“id”: “240263402699918”, “name”: “JTBC 뉴스”}

여기서 “Id”는 “jtbcnews”의 Nemeric ID를 의미하며, “name”은 해당페이지명을 의미한다. 우리는 코드를 통하여 “jtbcnews”의 Numeric ID를 얻었으며, 이를 페이지 아이디(Page ID)라고 부를 것이다. 이제 다음절에서는 페이지 아이디를 이용하여 페이지를 검색하고 데이터를 가지고 오는 방법에 대해 알아보도록 하겠다.
<br/><br/><br/><br/>
### 5.3 페이스북 포스트(/{post-id})) 가져오기
<br/><br/>
페이스북의 게시물들은 포스트(Post)를 이용하여 관리된다. 포스트는 공개된 페이지에 대하여 액세스 토큰(access token)을 이용하여 접근할 수 있으며, 페이지의 공개 범위 설정에 의하여 조회되는 데이터의 접근 정도가 결정된다.

> GET /[version info]/{post-id}
> Host: graph.facebook.com

포스트를 통하여 요청하는 주요 데이터는 다음과 같다.
<br/><br/>

| 필드명 | 설명 | 반환 형식 |
|--------|--------|--------|
| id | 포스트 ID | String |
| comments | 댓글 정보 | Object |
| created_time | 포스트 초기 생성일자 | Datetime |
| from | 포스트한 사용자에 대한 프로필 정보 | Profile |
| link | 포스트에 삽입되어 있는 링크 | String |
| message | 포스트 메시지 | String |
| name | 링크의 이름 | String |
| object_id | 업로드한 사진 또는 동영상 ID | String |
| parent_id | 해당 포스트의 부모 포스트 | String |
| picture | 포스트에 포함되어 있는 사진들의 링크 | String |
| place | 포스트를 작성한 위치 정보 | Place |
| reactions | 좋아요, 화나요 등에 대한 리엑션 정보 | Obejct |
| shares | 포스트를 공유한 숫자 | Object |
| type | 포스트의 객체 형식 | enum{link, status, photo, video, offer} |
| updated_time | 포스트가 최종 업데이트된 시간 | Datetime |
{: .table table-striped}

<br/>
게시글을 가지고 오기 위하여 아래와 같이 코드를 작성한다.

{% highlight python %}
# -*- coding: utf-8 -*-
# version: 3.5

import sys
import urllib.request
import json

if __name__ == '__main__':

    page_name = "jtbcnews"
    page_id = "240263402699918"
    app_id = "[App ID]"
    app_secret = "[App Secret Code]"

    # [CODE 1]
    from_date = "2017-01-01"
    to_date = "2017-01-31"
    num_statuses = "10"
    access_token = app_id + "|" + app_secret

    # [CODE 2]
    base = "https://graph.facebook.com/v2.8"
    node = "/%s/posts" % page_id
    fields = "/?fields=id,message,link,name,type,shares,reactions," + \
             "created_time,comments.limit(0).summary(true)" + \
             ".limit(0).summary(true)"
    duration = "&since=%s&until=%s" % (from_date, to_date)
    parameters = "&limit=%s&access_token=%s" % (num_statuses, access_token)
    url = base + node + fields + duration + parameters

    req = urllib.request.Request(url)

    try:
        response = urllib.request.urlopen(req)
        if response.getcode() == 200:
            data = json.loads(response.read().decode('utf-8'))
            print (data)

    except Exception as e:
        print (e)
{% endhighlight %}

<br/>
코드를 수행하면 해당 다음과 같이 JSON 형태의 페이스북 포스트가 수신된다.

```
{
    "data":[
        {
            "comments":{
                "data":[
                ],
                "summary":{
                    "order":"ranked",
                    "total_count":12,
                    "can_comment":"False"
                }
            },
            "message":"즉 청와대가 최 씨의 국정개입 사건을 파 악하고도 \n은폐했다는 사실이 안 전 수석 입에서 나온 겁니다.",
            "type":"link",
            "shares":{
                "count":46
            },
            "reactions":{
                "data":[
                ],
                "summary":{
                    "viewer_reaction":"NONE",
                    "total_count":443
                }
            },
            "created_time":"2017-02-23T00:00:00+0000",
            "name":"안종범 \"재단 임원 인사에 최순실 개입, 알고도 숨겼다\"",
            "id":"240263402699918 _1328805163845731",
            "link":"http://news.jtbc.joins.com/article/article.aspx?new s_id=NB11427906&pDate=20170222"
        }
    ],
    "paging":{
        "next":"https://graph.facebook.co m/v2.8/240263402699918/posts?fields=...",
        "previous":"https://graph.facebook.com/v2.8/240263402699918/posts?fields=..."
    }
}
```

<br/>
수신된 JSON 형식의 포스트의 실제 페이스북 화면은 [그림 3]과 같다.
<br/><br/>

![](/asset/study/python_crawling/3/3.jpg)
[그림 3] JTBCNEWS(ID = 240263402699918) 포스트
{: .borderBox}

<br/>
코드에 대해 자세히 살펴보면 다음과 같다.

{% highlight python %}
# [CODE 1] 기본 인자의 설정
    from_date = "2017-01-01"
    to_date = "2017-01-31"
    num_statuses = "10"
    access_token = app_id + "|" + app_secret

{% endhighlight %}

<br/>
먼저 접근할 포스트의 기간 설정을 위하여 검색의 시작일과 종료일, 그리고 1회 조회 시 가져올 포스트의 개수를 지정한다. 정확하게 페이스북에서는 최대 한 페이지의 포스트 개수에 대하여 정의한 문서는 찾지 못하였지만 동시에 가지고 올 포스트의 개수가 커지면 서버 응답시간이 길어지게 되고, 성능 저하의 원인이 될 수 있으므로 적당한 수로 조정하는 것이 좋다.

{% highlight python %}
# [CODE 2] 쿼리 파라미터의 작성
    base = "https://graph.facebook.com/v2.8"
    node = "/%s/posts" % page_id
    fields = "/?fields=id,message,link,name,type,shares,reactions," + \
             "created_time,comments.limit(0).summary(true)" + \
             ".limit(0).summary(true)"
    duration = "&since=%s&until=%s" % (from_date, to_date)
    parameters = "&limit=%s&access_token=%s" % (num_statuses, access_token)
    url = base + node + fields + duration + parameters
{% endhighlight %}

<br/>
기본적으로 가지고 올 노드는 “jtbcnews’의 페이지 아이디로 지정한다(페이지 아이디를 얻는 방법은 5.2절에서 설명하였다). 가지고올 필드는 message, link, created_time, type, name, id, comments, shares, reaction으로 지정하고 검색 기간은 기본 인자 설정에서지정한 기간으로 설정한다.
<br/><br/>
comments와 reaction은 summary값을 요청하는 경우 FQL(Facebook Query Language)을 이용하는 방법을 공식문서에서 확인할 수 있는데 직접 쿼리 파라미터로 접근할 수 있는 방법을 스택오버플로우(Stack Overflow : http://stackoverflow.com/questions/17755753/how-to-get-likes-count-when-searching-facebook-graph-api-with-search-xxx)에서 답변으로 확인할 수 있다.
<br/><br/>
검색 기간은 기본적으로 페이스북에서는 유닉스 타임스탬프 또는 일반적인 “YYYY-MM-DD” 형식을 지원한다.
<br/><br/>
우리는 기본적인 코드를 이용하여 원하는 페이지로부터 POST를 가지고 오는 방법에 대하여 알아보았다. 이제 이를 더 확장하여 좋아요 등의 개수와 댓글의 개수를 확인하는 방법에 대하여 알아보고, 이를 JSON 형식으로 저장하고, 나중에 활용할 수 있도록 하자.
<br/><br/><br/><br/>
### 5.4	페이스북 포스트(/{post-id})) 저장
<br/><br/>
페이스북의 포스트들을 검색하여 JSON 파일로 저장하기 위하여 다음 코드를 작성한다.

{% highlight python %}
# -*- coding: utf8 -*-
# version: 3.5

import sys
import urllib.request
import json
import datetime
import csv
import time

#[CODE 1]
def get_request_url(url):

    req = urllib.request.Request(url)

    try:
        response = urllib.request.urlopen(req)
        if response.getcode() == 200:
            print ("[%s] Url Request Success" % datetime.datetime.now())
            return response.read().decode('utf-8')
    except Exception as e:
        print(e)
        print("[%s] Error for URL : %s" % (datetime.datetime.now(), url))
        return None

#[CODE 2]
def getFacebookNumericID(page_id, access_token):

    base = "https://graph.facebook.com/v2.8"
    node = "/" + page_id
    parameters = "/?access_token=%s" % access_token
    url = base + node + parameters

    retData = get_request_url(url)

    if (retData == None):
        return None
    else:
        jsonData = json.loads(retData)
        return jsonData['id']

#[CODE 3]
def getFacebookPost(page_id, access_token, from_date, to_date, num_statuses):

    base = "https://graph.facebook.com/v2.8"
    node = "/%s/posts" % page_id
    fields = "/?fields=id,message,link,name,type,shares,reactions," + \
             "created_time,comments.limit(0).summary(true)" + \
             ".limit(0).summary(true)"
    duration = "&since=%s&until=%s" % (from_date, to_date)
    parameters = "&limit=%s&access_token=%s" % (num_statuses, access_token)
    url = base + node + fields + duration + parameters

    retData = get_request_url(url)

    if (retData == None):
        return None
    else:
        return json.loads(retData)


def getPostItem(post, key):
    try:
        if key in post.keys():
            return post[key]
        else:
            return ''
    except:
        return ''

def getPostTotalCount(post, key):
    try:
        if key in post.keys():
            return post[key]['summary']['total_count']
        else:
            return 0
    except:
        return 0

#[CODE 4]
def getPostData(post, access_token, jsonResult):

    #[CODE 4-1]
    post_id = getPostItem(post, 'id')
    post_message = getPostItem(post, 'message')
    post_name = getPostItem(post, 'name')
    post_link = getPostItem(post, 'link')
    post_type = getPostItem(post, 'type')

    post_num_reactions = getPostTotalCount(post, 'reactions')
    post_num_comment = getPostTotalCount(post, 'comments')
    post_num_shares = 0 if 'shares' not in post.keys() else post['shares']['count']

    #[CODE 4-2]
    post_created_time = getPostItem(post, 'created_time')
    post_created_time = datetime.datetime.strptime(post_created_time, '%Y-%m-%dT%H:%M:%S+0000')
    post_created_time = post_created_time + datetime.timedelta(hours=+9)
    post_created_time = post_created_time.strftime('%Y-%m-%d %H:%M:%S')

    #[CODE 4-3]
    reaction = getFacebookReaction(post_id, access_token) if post_created_time > '2016-02-24 00:00:00' else {}
    post_num_likes = getPostTotalCount(reaction, 'like')
    post_num_likes = post_num_reactions if post_created_time < '2016-02-24 00:00:00' else post_num_likes

    #[CODE 4-4]
    post_num_loves = getPostTotalCount(reaction, 'love')
    post_num_wows = getPostTotalCount(reaction, 'wow')
    post_num_hahas = getPostTotalCount(reaction, 'haha')
    post_num_sads = getPostTotalCount(reaction, 'sad')
    post_num_angrys = getPostTotalCount(reaction, 'angry')

    jsonResult.append({'post_id':post_id, 'message':post_message,
                    'name':post_name, 'link':post_link,
                    'created_time':post_created_time, 'num_reactions':post_num_reactions,
                    'num_comments':post_num_comment, 'num_shares':post_num_shares,
                    'num_likes':post_num_likes, 'num_loves':post_num_loves,
                    'num_wows':post_num_wows, 'num_hahas':post_num_hahas,
                    'num_sads':post_num_sads, 'num_angrys':post_num_angrys})


#[CODE 5]
def getFacebookReaction(post_id, access_token):

    base = "https://graph.facebook.com/v2.8"
    node = "/%s" % post_id
    reactions = "/?fields=" \
                "reactions.type(LIKE).limit(0).summary(total_count).as(like)" \
                ",reactions.type(LOVE).limit(0).summary(total_count).as(love)" \
                ",reactions.type(WOW).limit(0).summary(total_count).as(wow)" \
                ",reactions.type(HAHA).limit(0).summary(total_count).as(haha)" \
                ",reactions.type(SAD).limit(0).summary(total_count).as(sad)" \
                ",reactions.type(ANGRY).limit(0).summary(total_count).as(angry)"
    parameters = "&access_token=%s" % access_token
    url = base + node + reactions + parameters

    retData = get_request_url(url)

    if (retData == None):
        return None
    else:
        return json.loads(retData)

#[CODE 6]
def main():
    page_name = "jtbcnews"
    app_id = "App ID"
    app_secret = "App Secret Code"
    access_token = app_id + "|" + app_secret

    from_date = '2017-02-01'
    to_date = '2017-02-03'

    num_statuses = 10
    go_next = True
    jsonResult = []

    page_id = getFacebookNumericID(page_name, access_token)

    if (page_id == None):
        print ("[%s] %s is Invalid Page Name" % (datetime.datetime.now(), page_name))
        exit()

    print ("[%s] %s page id is %s" % (datetime.datetime.now(), page_name, page_id))

    #[CODE 6-1]
    jsonPost = getFacebookPost(page_id, access_token, from_date, to_date, num_statuses)

    if (jsonPost == None):
        print ("No DATA")
        exit()

    #[CODE 6-2]
    while (go_next):
        for post in jsonPost['data']:
            getPostData(post, access_token, jsonResult)

        if 'paging' in jsonPost.keys():
            jsonPost =  json.loads(get_request_url(jsonPost['paging']['next']))
        else:
            go_next = False

    #[CODE 6-3]
    with open('%s_facebook_%s_%s.json' % (page_name, from_date, to_date), 'w', encoding='utf8') as outfile:
        str_ = json.dumps(jsonResult,
                      indent=4, sort_keys=True,
                      ensure_ascii=False)
        outfile.write(str_)

    print ('%s_facebook_%s_%s.json SAVED' % (page_name, from_date, to_date))

if __name__ == '__main__':
    main()

{% endhighlight %}

<br/>



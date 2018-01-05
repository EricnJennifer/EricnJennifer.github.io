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
<br/><br/>

| 항목 | 설명 |
|--------|--------|
| 노드(Node) | 기본적으로 사용자, 사진, 페이지, 댓글과 같은 항목(item)을 포함한다 |
| 에지(Edge)| 페이지의 사진, 사진의 댓글 등 각 항목간의 연결 링크 |
| 필드(Field) | 페이지 정보, 해당 페이지 프로필(Profile)등 항목에 대한 정보 |
{: .table table-striped}

<br/><br/>
그래프 API는 HTTP 기반으로 설계되어 쉽게 이해할 수 있으며 웹 브라우저에서도 동작할 수 있는 장점을 가지고 있으나 보안성이 취약한 단점을 가지고 있다. 보안이 필요한 경우에는 OAuth 인증 방식을 사용하는 것을 권장하나 본 책의 범위를 넘어서는 관계로 여기서는 그래프 API의 엑세스 토큰을 전달하는 방식을 사용한다.
<br/><br/>
<br/><br/>

### 5.1 페이스북 그래프(Graph) API
<br/><br/>

그래프(Graph) API는 페이스북의 소셜 그래프에서 데이터를 가지고 오거나 게시하는 기본적인 방법을 제공하는 HHTP 기반의 API이다. 그래프 API는 모든 노드(Node)와 에지(Edge)에 관하여 HTTP GET 요청을 보내기만 하면 해당 데이터를 조회할 수 있다.
<br/><br/>
<br/><br/>
#### 1.1 읽기
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

![](/asset/study/python_crawling/2/31.jpg)
[그림 1] 공공 데이터 포털 초기 페이지
{: .borderBox}
<br/><br/>
























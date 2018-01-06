---
layout: post_study
title:  6. 트위터 데이터 수집하기
date: 2018-01-05 06:01:00
categories: python_crawling
---
## 6. Twitter 데이터 수집
<br/><br/><br/>
우리는 [2. Twitter API 사용하기](https://ericnjennifer.github.io/python_crawling/2018/01/05/PythonCrawling_Chapt3.html)에서에서 트위터 App을 설정하였다. 이제 해당 앱 ID와 비밀키를 가지고 파이썬을 이용하여 데이터를 가지고 오는 방법에 대하여 설명하고자 한다. 페이스북의 경우에는 앱 아이디와 토큰을 요청 URL에 파라미터로 전송하여 간단하게 데이터를 가지고 올 수 있는 기능을 제공한다. 그러나 트위터의 경우에는 OAuth1.0a의 기본에 충실하게 억세스 토큰을 가지고 와야 실제 원하는 데이터를 조회 할 수 있다.
<br/><br/><br/><br/>
### 6.1 OAuth란?
<br/><br/>
사용자는 특정 서비스를 사용하기 위하여 아이디와 비밀번호라는 고전적인 방법을 이용하였다. 이를 위하여 서비스 제공자는 사용자의 아이와 비밀번호를 관리하여야 하는데 서버의 공격등으로 인해 아이디와 비밀번호가 노출되는 사고가 빈번히 발생하고 이를 위하여 데이터베이스에 비밀번호를 암호화 하는 등 다양한 형태의 보안 기술이 발생하였다.
<br/><br/>
OAuth의 시작은 2006년에 트위터와 소셜 북마크 서비스인 Gnolia사의 개발자들은 접속한 사용자들이 서비스를 사용하기 위하여 사용자를 인증(Authentication)하고 특정한 서비스만 사용하기 위한 권한(Authorization)을 주기 위한 적당한 인증 알고리즘이 없다고 판단하여 개발에 착수, 2007년 10월에 OAuth1.0을 발표하였다. 그 이후 세션 고정 공격(Session Fixation Attack: 세션 하이재킹(Hijacking) 기법중의 하나로 유요한 유저 세션을 탈취하여 인증을 회피하는 공격 방법)에 취약함이 발견되어 이를 개선하고 “The OAuth 1.0 Protocol”이라는 이름으로 2010년에 표준안이 RFC5849로 IETF에 의해 체택 되었다. 기존의 1.0에서 보안된 개념으로 실제 1.0이라고 제정되었지만 많은 사람들은 OAuth 1.0a라고 부른다.
현재 OAuth는 처음 설계를 한 트위터를 필두로, 구글, 페이스북, 마이크로 소프트 등의 외국 거대 서비스 회사 및 국내의 네이버, 다음 카카오 등에서 사용하고 있다.
<br/><br/>
OAuth의 장점은 사용자의 아이디와 비밀번호를 이용하여 인증을 하는 것이 아니고, 특정 서비스를 사용하고자 할 때 임시 사용 티켓을 발급하여, 서비스가 만료되거나 특정 기간이 종료하면 티켓을 사용할 수 없는 구조로 되어있다.
<br/><br/><br/>
#### 6.1.1 OAuth 1.0a 인증 과정
<br/>
OAuth를 이용하여 사용자를 인증하는 과정을 OAuth Dance – ‘춤’이라고 표현할 수 있는 개발자들의 자유스러움이 부럽다 - 라고 한다. 먼저 OAuth 인증을 위하여 사용하는 주체를 표현하는 용어는 다음과 같다.
<br/><br/>

|--------|--------|
| 소비자<br/>Consumer | OAuth를 사용해 서비스 제공자의 기능을 사용하려는 프로그램<br/> : 우리가 제작하는 프로그램의 입장|
| 서비스 제공자<br/>Service Provider | OAuth를 사용하는 Open API 제공자<br/> : 트위터, 페이스 북 등|
| 요청 토큰<br/>Request Token | OA소비자(Consumer)가 서비스제공자(Service Provider)에게 접근 권한을 받기 위해 사용하는 값, 인증이 완료되면 접근 토큰(Access Token)으로 변경된다 |
| 접근 토큰<br/>Access Token | 인증 후 소비자(Consumer)가 서비스제공자(Service Provider)의 자원에 접근하기 위한 키를 포함한 값|
| 사용자<br/>User | 서비스제공자(Service Provider)에 계정을 가지고 있으면서, 소비자(Consumer)를 이용하려는 사용자|
{: .table table-striped}
<br/>
[그림 1]은 OAuth 1.0a의 인증 과정을 나타낸다.
<br/><br/>

![](/asset/study/python_crawling/3/6.jpg)
[그림 1] OAuth 1.0a 인증 과정 (http://oauth.net/core/diagram.png)
{: .borderBox}

<br/>
1) Request Token 요청 및 발급
<br/>
* Consumer Key와 요청 시간(timestamp), 요청자료를 암호화 한 방식(HMAC-SHA1 등), 버전(1.0) 정보, 악의적인 정보 요청을 방지하기 위한 임의 문자열과 서명값을 만든후 해당 서버로 요청한다.
<br/>
* 서명값(oauth_signature) 앞에서 설명한 각 변수값과 HTTP 요청 방식(POST 또는 GET)을 지정한 암호화 방식으로 암호화하여 만들어 낸다.
<br/>
* 서비스 제공자에게 Request Token을 요청하면, 서비스 제공자는 ‘oauth_token’과 ‘oauth_token_secret’값을 회신한다.
<br/><br/>

2) 사용자 인증 페이지 호출 및 수락
<br/>
* 수신한 ‘oauth_token’을 가지고 서비스 제공자가 지정한 인증페이지로 인증 수락을 요청한다. 인증 페이지는 각 서비스제공자가 제공한다.
<br/><br/>

3) Access Token 요청 및 발급
<br/>
* ‘consumer_key’와 ‘oauth_token’ 및 기타 변수들을 이용하여 ‘oauth_signature’ 를 생성한 후 요청하면 서비스 제공자는 ‘oauth_token’과 ‘oauth_token_secret’ 및 부가 정보를 회신한다.
<br/><br/>

4) 해당 API 접근
<br/>
* 수신한 ‘oauth_token’을 이용하여 서비스제공자의 API를 사용한다.
<br/><br/><br/>
#### 6.1.2 OAuth 2.0의 탄생
<br/>
아쉽게도 OAuth 2.0은 OAuth 1.0a와 호환성을 가지고 개발되지 않았다. OAuth 2.0은 시그네처(signature)의 생성이 복잡하고 CPU 부하를 많이 가지고 있어서 모바일 환경에 적합하지 않아 시작된 표준이다. 그러나 하나의 표준이 존재하지 않고 프레임워크(Framework)라고 부르며, 여러 개의 표준 작업이 기업의 목적에 따라 이루어지고 있다. 간단하고 더 많은 인증 방법을 제공하고 있으나 각 기업이나 단체가 자신들의 서비스를 목적으로 표준 작업을 진행하다 보니 복잡하고 방대해져서 OAuth 1.0a를 대체하는데 많은 시간이 걸릴 것이라 관측하는 사람들도 적지 않다.
<br/><br/><br/><br/>





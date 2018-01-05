---
layout: post_study
title:  3. NAVER API 사용하기
date: 2018-01-05 04:01:00
categories: python_crawling
---
## 3. 네이버(Naver) API 사용하기
<br/><br/>

이 블로그를 읽는 독자중에 네이버 아이디가 없는 사람은 아마 없을 것이므로 네이버 API를 활용하기 위한 과정에 대하여 설명하도록 하겠다. 먼저 개발자 계정을 얻기 위하여 네이버 개발자 페이지(http://developer.naver.com)로 이동한다.
<br/><br/>
[그림 1]은 네이버 개발자 페이지이다.

![](/asset/study/python_crawling/2/25.jpg)
[그림 1] 네이버 개발자 페이지(http://developer.naver.com)
{: .borderBox}
<br/><br/>

상단 메뉴의 [API 소개] -> [검색]을 선택하면 [그림 2]와 같이 네이버 검색 API 소개 페이지로 이동한다.
<br/><br/>

![](/asset/study/python_crawling/2/26.jpg)
[그림 2] API 사용을 위한 애플리케이션 등록
{: .borderBox}
<br/><br/>

하단으로 이동하여 [오픈 API 이용신청]을 선택하면 이용약관에 동의한 후 [확인] 버튼을 누르면 [그림 3]과 같이 API를 사용하기 위한 약관에 동의를 요청하는 화면이 나온다.
<br/><br/>

![](/asset/study/python_crawling/2/27.jpg)
[그림 3] API 이용 약관 동의
{: .borderBox}
<br/><br/>

하단으로 이동하여 이용약관에 동의하고 [확인]버튼을 누르면 [그림 4]와 같이 작성할 애플리케이션의 이름을 요청하는 화면이 나타난다.
<br/><br/>

![](/asset/study/python_crawling/2/28.jpg)
[그림 4] 애플리케이션 등록(API 이용신청)
{: .borderBox}
<br/><br/>

애플리케이션의 이름과 사용 API를 선택한다. 우리는 네이버 뉴스나 블로그의 기사를 검색할 목적으로 활용할 것이므로, [검색]을 선택한다(추후 관리페이지에서 다른 API를 추가할 수 있다). 사용 API를 선택하면 [그림 5]와 같이 해당 서비스를 제공할 도메인을 설정하도록 요구한다.
<br/><br/>

![](/asset/study/python_crawling/2/29.jpg)
[그림 5] 비로그인 오픈 API 서비스 신청
{: .borderBox}
<br/><br/>

본 사이트에서는 크롤러를 만들고, 수집한 데이터를 분석하여 서비스한다는 전제하에 [WEB 설정]을 선택하고, 웹 서비스 URL을 입력한다. 아직 서비스할 도메인이 지정되어 있지 않기 때문에 http://localhost를 지정한다. 입력을 완료한 후 [등록하기] 버튼을 누르면 [그림 6]과 같이 신규 애플리케이션이 등록된 것을 확인할 수 있다.
<br/><br/>

![](/asset/study/python_crawling/2/30.jpg)
[그림 6] 신규 애플리케이션의 등록 완료
{: .borderBox}
<br/><br/>

신규 애플리케이션을 위하여 ‘Client ID’와 ‘Client Secret’이 발급되었으며, ‘Client Secret’ 정보는 [보기] 버튼을 누르면 문자열로 변환되어 나타난다.













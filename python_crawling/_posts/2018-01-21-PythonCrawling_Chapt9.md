---
layout: post_study
title:  9. 네이버 데이터 수집하기
date: 2018-01-21 07:01:00
categories: python_crawling
---
## 8. 네이버(NAVER) 데이터 수집하기
<br/><br/><br/>
네이버의 서비스는 ‘네이버 아이디로 로그인’ 이라는 서버 인증을 통해 Access Token을 가지고온후 API를 사용할 수 있는 서비스와, HTTP 헤더에 클라이언트 아이디와 시크릿(secret) 값만을 얹어 전송하면 되는 비로그인 서비스로 나뉜다. 네이버의 뉴스, 블로그 및 까페의 글들은 로그인을 하지 않고 비로그인 방식으로 데이터를 조회하여 가지고 올 수 있도록 설계되어 있어 복잡한 인증 과정을 거치지 않아도 되고 어플리케이션을 만들 경우 Access Token을 얻기 위한 웹 페이지 처리를 하지 않아도 되는 장점이 있다. 그러나 2가지 제약 사항을 가진다.
<br/><br/>
> 1) 하루 검색 기사수: API 호출이 25,000회/일로 제한되어 있다. 최대 한번 호출에 100개의 검색을 가지고 올 수 있으므로 250,000건의 데이터를 가지고 올 수 있다.
<br/><br/>
2) 검색 포인터 문제: 본 책을 쓰고 있는 시점에 가장 검색 이슈가 되는 부분은 대통령 탄핵(언제 책이 출간될지 모르겠지만..) 부분이였다. 실제 네이버에서 “탄핵”이라는 데이터를 검색하면 뉴스에서만 약 43만건의 데이터가 검색되는데 최대 1,000개밖에 API를 이용하여 가지고 올 수 없다(코드에서 자세히 설명하겠다)

<br/><br/>
물론 상업적으로 네이버의 기사를 크롤링한다면, 계약을 통하여 제약 사항을 해결할 수 있으리라 생각이 들지만 2)번 사항에 대해서는 조금 가혹하다는 생각이 든다.
<br/><br/>
### 8.1 검색 API의 활용
<br/><br/>
뉴스, 블로그 및 까페의 글들을 수집하기 위하여 다음의 코드를 작성한다.(코드를 작성하기 전에 먼저 네이버에 계발자 계정을 가지고 있어야 한다. 개발자 계정을 만드는 방법은 [3. 네이버(Naver) API 사용하기](https://ericnjennifer.github.io/python_crawling/2018/01/05/PythonCrawling_Chapt4.html) 를 참조하기 바란다)

{% highlight python %}
import os
import sys
import urllib.request
import datetime
import time
import json
from config import *

#[CODE 1]
def get_request_url(url):
    
    req = urllib.request.Request(url)
    req.add_header("X-Naver-Client-Id", app_id)
    req.add_header("X-Naver-Client-Secret", "scxKeSJYib")
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
def getNaverSearchResult(sNode, search_text, page_start, display):
    
    base = "https://openapi.naver.com/v1/search"
    node = "/%s.json" % sNode
    parameters = "?query=%s&start=%s&display=%s" % (urllib.parse.quote(search_text), page_start, display)
    url = base + node + parameters
    
    retData = get_request_url(url)
    
    if (retData == None):
        return None
    else:
        return json.loads(retData)

#[CODE 3]
def getPostData(post, jsonResult):
    
    title = post['title']
    description = post['description']
    org_link = post['originallink']
    link = post['link']

    #Tue, 14 Feb 2017 18:46:00 +0900
    pDate = datetime.datetime.strptime(post['pubDate'],  '%a, %d %b %Y %H:%M:%S +0900')
    pDate = pDate.strftime('%Y-%m-%d %H:%M:%S')
    
    jsonResult.append({'title':title, 'description': description,
                    'org_link':org_link, 'link': org_link, 
                    'pDate':pDate})
    return    

def main():

    jsonResult = []

    # 'news', 'blog', 'cafearticle'
    sNode = 'news'
    search_text = '탄핵'
    display_count = 100
    
    jsonSearch = getNaverSearchResult(sNode, search_text, 1, display_count)
    
    while ((jsonSearch != None) and (jsonSearch['display'] != 0)):
        for post in jsonSearch['items']:
            getPostData(post, jsonResult)
        
        nStart = jsonSearch['start'] + jsonSearch['display']
        jsonSearch = getNaverSearchResult(sNode, search_text, nStart, display_count)
    
    with open('%s_naver_%s.json' % (search_text, sNode), 'w', encoding='utf8') as outfile:
        retJson = json.dumps(jsonResult,
                        indent=4, sort_keys=True,
                        ensure_ascii=False)
        outfile.write(retJson)
        
    print ('%s_naver_%s.json SAVED' % (search_text, sNode))

    
if __name__ == '__main__':
    main()
{% endhighlight %}

<br/>
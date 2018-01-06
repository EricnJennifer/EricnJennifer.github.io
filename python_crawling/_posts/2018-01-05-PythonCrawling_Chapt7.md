---
layout: post_study
title:  6. TEST
date: 2018-01-05 07:01:00
categories: python_crawling
---


 항목 | 설명
--------|--------
 노드(Node) | 기본적으로 사용자, 사진, 페이지, 댓글과 같은 항목(item)을 포함한다
 에지(Edge)ㅆㄸㅆㄴ ㅆㄸ| 페이지의 사진, 사진의 댓글 등 각 항목간의 연결 링크
 필드(Field) | 페이지 정보, 해당 페이지 프로필(Profile)등 항목에 대한 정보


{% highlight python %}
import sys
import urllib.request
import json

if \_\_name\_\_ == '\_\_main\_\_':
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







---
layout: post_study
title:  6. TEST
date: 2018-01-05 07:01:00
categories: python_crawling
---



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







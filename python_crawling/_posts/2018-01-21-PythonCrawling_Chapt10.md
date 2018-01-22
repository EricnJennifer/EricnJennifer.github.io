---
layout: post_study
title:  9. 네이버 지도 API(주소, 좌표변환) 활용
date: 2018-01-21 08:01:00
categories: python_crawling
---
## 9. 네이버 지도 API(주소, 좌표변환) 활용
<br/><br/><br/>
지도 API는 네이버에서 활용도를 극대화 하기 위하여 오픈 소스로 비중 있게 공개하고 있는 부분이다. 자바 스크립트등을 이용하면 네이버 지도 서비스를 그대로 사용할 수 있고, 위성 지도, 파노라마, 교통 상화, 레이어 지도 등 다양한 서비스를 활용할 수 있어 지도와 관련된 어플리케이션 구성에 편의성을 제공해준다. 이번 절에서는 우리가 수신한 데이터를 지오(Geo)데이터(위경도 데이터)와 활용하기 위하여 주소, 좌표변환 서비스를 활용해 보도록 하겠다.
<br/><br/>
먼저 네이버 개발자 페이지로 이동한 후에 상단 메뉴의 [Application] >> [내 어플리케이션]으로 이동한 후 개발자 ID를 받은 애플리케이션으로 이동하면 [그림 1]과 같이 상세 정보가 나타난다.
<br/><br/>

![](/asset/study/python_crawling/3/15.jpg)
[그림 1] 사용 API의 추가
{: .borderBox}

<br/>
[그림 1]의 적색 상자로 표시한 부분의 “선택하세요”를 누른 후 [지도]를 선택한 후 하단의 [수정]버튼을 누르면 지도 API가 추가된다. 이제 아래와 같이 코드를 작성한다.

{% highlight python %}
import os
import sys
import urllib.request
import datetime
import time
import json
from config import *

def get_request_url(url):
    
    req = urllib.request.Request(url)
    req.add_header("X-Naver-Client-Id", client_id)
    req.add_header("X-Naver-Client-Secret", client_secret)
    try: 
        response = urllib.request.urlopen(req)
        if response.getcode() == 200:
            print ("[%s] Url Request Success" % datetime.datetime.now())
            return response.read().decode('utf-8')
    except Exception as e:
        print(e)
        print("[%s] Error for URL : %s" % (datetime.datetime.now(), url))
        return None

#[CODE 1]
def getGeoData(address):
    
    base = "https://openapi.naver.com/v1/map/geocode"
    node = ""
    parameters = "?query=%s" % urllib.parse.quote(address)
    url = base + node + parameters
    
    retData = get_request_url(url)
    
    if (retData == None):
        return None
    else:
        return json.loads(retData)

def main():

    jsonResult = getGeoData('서울특별시 종로구 사직로 161 경복궁') 

    if 'result' in jsonResult.keys():
        print('총 검색 결과: ', jsonResult['result']['total'])
        print('검색어: ', jsonResult['result']['userquery'])
        
        for item in jsonResult['result']['items']:
            print('=======================')
            print('주소: ', item['address'])
            print('위도: ', str(item['point']['y']))
            print('경도: ', str(item['point']['x']))

if __name__ == '__main__':
    main()
{% endhighlight %}

<br/>
코드를 실행하면 [그림 2]와 같이 주소를 위도와 경도로 변환한 값을 가지고 올 수 있다.
<br/><br/>

![](/asset/study/python_crawling/3/16.jpg)
[그림 2] 주소 >> 위경도 좌표 변환 결과
{: .borderBox}

<br/>
지오 데이터를 얻기 위하여 네이버는 https://openapi.naver.com/v1/map/geocode (json 형식)과 https://openapi.naver.com/v1/map/geocode.xml (xml 형식)의 End Point를 제공한다.

{% highlight python %}
#[CODE 1]
def getGeoData(address):
    
    base = "https://openapi.naver.com/v1/map/geocode"
    node = ""
    parameters = "?query=%s" % urllib.parse.quote(address)
    url = base + node + parameters
    
    #이하 생략
{% endhighlight %}

<br/>
전달하는 매개 변수의 형식은 다음과 같다.
<br/><br/>

|--------|--------|
| query | 검색어 파라미터(UTF-8 인코딩 형식) |
| encoding | 출력 결과 인코딩값 (UTF-8 기본값 / EUC-KR) |
| coordType | 출력 좌표 체계 (latlng(위경도) 기본값 / tm 128(카텍)) |
{: .table table-striped}
<br/>
전달한 매개변수를 통해 수신하는 JSON 데이터의 형식은 [그림 3]과 같다.
<br/><br/>

![](/asset/study/python_crawling/3/17.jpg)
[그림 3] 저장된 JSON 형식의 주소 검색 결과
{: .borderBox}

<br/>
이번 절에서 우리는 네이버 비로그인 API를 이용하여 뉴스 나 블로그등을 검색하는 방법과 주어진 주소를 가지고 위경도 좌표계로 변경하는 방법을 알아보았다. 주소 >> 위경도 변경은 추후 알아보게 될 공공정보 데이터와 연계를 위하여 필요한 부분이므로 미리 숙지하고 가는 것이 좋을 것이다.
---
layout: post_study
title:  10. 공공 정보 데이터 수집하기
date: 2018-01-21 09:01:00
categories: python_crawling
---
## 10. 공공 정보 데이터 수집하기
<br/><br/><br/>
우리는 [정부3.0 공공 데이터 포털 API 사용하기](https://ericnjennifer.github.io/python_crawling/2018/01/05/PythonCrawling_Chapt5.html)에서 공공 데이터 포털 API를 사용하기 위한 가입 및 서비스 요청 방법을 알아보았다. 공공 데이터 포털 개발 계정 신청을 통해 발급되는 일반 인증키는 API 요청 신청을 한 모든 API에 대하여 동일한 UTF-8 형식의 키값을 사용한다. 개발자의 입장에서는 편리할 수 있지만, 보안 측면에서는 개별 서비스마다 별도의 API 키 값을 부여하는 것이 어떨까 생각만(!) 해본다.
<br/><br/>
<br/><br/>
### 10.1 전국 유료 관광지 입장객 정보
<br/><br/>
관광 자원 통계 서비스 중 유료관광지방문객수조회 기능은 기간별 외국인방문객수와 내국인방문객수, 관광지의 주소 정보, 지역코드 정보를 제공한다. 지역별 관광지 입장객 수를 분석하기 위하여 다음의 코드를 작성한다.

{% highlight python %}
import os
import sys
import urllib.request
import datetime
import time
import json
from config import *
import math

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

#[CODE 1]
def getTourPointVisitor(yyyymm, sido, gungu, nPagenum, nItems):
    
    end_point = "http://openapi.tour.go.kr/openapi/service/TourismResourceStatsService/getPchrgTrrsrtVisitorList"
    
    parameters = "?_type=json&serviceKey=" + access_key
    parameters += "&YM=" + yyyymm
    parameters += "&SIDO=" + urllib.parse.quote(sido)
    parameters += "&GUNGU=" + urllib.parse.quote(gungu)
    parameters += "&RES_NM=&pageNo=" + str(nPagenum)
    parameters += "&numOfRows=" + str(nItems)

    url = end_point + parameters
    
    retData = get_request_url(url)
    
    if (retData == None):
        return None
    else:
        return json.loads(retData)

#[CODE 2]
def getTourPointData(item, yyyymm, jsonResult):
    
    addrCd = 0 if 'addrCd' not in item.keys() else item['addrCd']
    gungu = '' if 'gungu' not in item.keys() else item['gungu']
    sido = '' if 'sido' not in item.keys() else item['sido']
    resNm = '' if 'resNm' not in item.keys() else item['resNm']
    rnum = 0 if 'rnum' not in item.keys() else item['rnum']
    ForNum = 0 if 'csForCnt' not in item.keys() else item['csForCnt']
    NatNum = 0 if 'csNatCnt' not in item.keys() else item['csNatCnt']
    
    jsonResult.append({'yyyymm': yyyymm, 'addrCd': addrCd,
                    'gungu': gungu, 'sido': sido, 'resNm': resNm, 
                    'rnum': rnum, 'ForNum': ForNum, 'NatNum': NatNum})
    return    

def main():

    jsonResult = []

    sido = '서울특별시'
    gungu = ''
    nPagenum = 1
    nTotal = 0
    nItems = 100
    
    nStartYear = 2011
    nEndYear = 2017

    for year in range(nStartYear, nEndYear):
        for month in range(1, 13):

            yyyymm = "{0}{1:0>2}".format(str(year), str(month))

            nPagenum = 1

            #[CODE 3]
            while True:
                jsonData = getTourPointVisitor(yyyymm, sido, gungu, nPagenum, nItems)
    
                if (jsonData['response']['header']['resultMsg'] == 'OK'):
                    nTotal = jsonData['response']['body']['totalCount']
            
                    if nTotal == 0:
                        break

                    for item in jsonData['response']['body']['items']['item']:
                        getTourPointData(item, yyyymm, jsonResult)
            
                    nPage = math.ceil(nTotal / 100)
                    if (nPagenum == nPage):
                        break

                    nPagenum += 1
                
                else:
                    break
    
    with open('%s_관광지입장정보_%d_%d.json' % (sido, nStartYear, nEndYear-1), 'w', encoding='utf8') as outfile:
        retJson = json.dumps(jsonResult,
                        indent=4, sort_keys=True,
                        ensure_ascii=False)
        outfile.write(retJson)
        
    print ('%s_관광지입장정보_%d_%d.json SAVED' % (sido, nStartYear, nEndYear-1))            
    
if __name__ == '__main__':
    main()
{% endhighlight %}

<br/>
코드를 수행하여 얻은 결과를 바탕으로 2011년 1월부터 2016년 9월까지 수집된 경복궁의 입장객 정보를 확인해보면 [그림 1]과 같다. [그림 1]은 수집된 데이터를 액셀을 이용하여 그래프 작업을 한 것이며 다음절에서는 수집된 데이터를 가지고 직접 파이썬을 이용하여 그래프를 그려 보도록 하겠다.
<br/><br/>

![](/asset/study/python_crawling/3/18.jpg)
[그림 1] 경복궁 내•외국인 입장객 현황
{: .borderBox}

<br/>
이제 코드를 살펴 보기로 하자. [코드 32]는 유료 관광지 정보 및 입장객수를 조회하기 위한 코드이다. 실제 데이터를 조회하면 분기별로 업데이트 되고 있는 것으로 파악되며, 공공정보가 제공하는 서울특별시 입장 정보 데이터의 수량이 많은 편은 아니여서 조금 아쉬운 상황이다.

{% highlight python %}
#[CODE 1]
def getTourPointVisitor(yyyymm, sido, gungu, nPagenum, nItems):
    
    end_point = "http://openapi.tour.go.kr/openapi/service/TourismResourceStatsService/getPchrgTrrsrtVisitorList"
    
    parameters = "?_type=json&serviceKey=" + access_key
    parameters += "&YM=" + yyyymm
    parameters += "&SIDO=" + urllib.parse.quote(sido)
    parameters += "&GUNGU=" + urllib.parse.quote(gungu)
    parameters += "&RES_NM=&pageNo=" + str(nPagenum)
    parameters += "&numOfRows=" + str(nItems)

    url = end_point + parameters
    
    retData = get_request_url(url)
    
    if (retData == None):
        return None
    else:
        return json.loads(retData)
{% endhighlight %}

<br/>
유료 관광지 방문객수 조회를 위한 End Point는 “http://openapi.tour.go.kr/openapi/service/TourismResourceStatsService/getPchrgTrrsrtVisitorList” 이며 전달하는 요청 변수는 다음과 같다.
<br/><br/>

| 항목 | 파라미터명 | 형식 | 설명 |
|--------|--------|--------|--------|
| 년월 | YM | YYYYMM(ex: 201703) | 검색 연월 |
| 시도 | SIDO | STRING (ex: 서울특별시) | 시도명 |
| 군구 | GUNGU | STRING (ex: 중구) | 시군구 |
| 관광지 | RES_NM | STRING (ex: 경복궁) | 관광지명 |
| 페이지 번호 | pageNo | INT (ex: 1) | 페이지 번호 |
| 조회 레코드 수 | numOfRow | INT(ex: 100) | 조회 레코드 최대수(Max: 100) |
{: .table table-striped}
<br/>
전달하는 변수는 UTF-8 형식으로 인코딩 되어 있어야 한다. 또한 해당 검색 조건에 부합하는 데이터가 ‘numOfRow’의 수보다 많은 경우에는 페이지로 분리된다. 이 경우 동일한 검색 조건에 pageNO 값을 변경하여 해당 페이지의 값을 가지고 올 수 있다. 페이징 처리와 수신하는 데이터의 형식을 알아보기 위해 다음 코드를 알아보자.

{% highlight python %}
#[CODE 3]
            while True:
                jsonData = getTourPointVisitor(yyyymm, sido, gungu, nPagenum, nItems)
    
                if (jsonData['response']['header']['resultMsg'] == 'OK'):
                    nTotal = jsonData['response']['body']['totalCount']
            
                    if nTotal == 0:
                        break

                    for item in jsonData['response']['body']['items']['item']:
                        getTourPointData(item, yyyymm, jsonResult)
            
                    nPage = math.ceil(nTotal / 100)
                    if (nPagenum == nPage):
                        break

                    nPagenum += 1
                
                else:
                    break
{% endhighlight %}

<br/>
[CODE 3]에서는 getTourPointVisitor() 함수를 이용하여 JSON 형식의 데이터를 먼저 가지고 온다. 다음은 수신한 JSON 데이터의 예이다.

{% highlight python %}
{
    "response": {
        "body": {
            "items": {
                "item": [
                    {
                        "addrCd": 1111,
                        "csForCnt": 40224,
                        "csNatCnt": 141183,
                        "gungu": "종로구",
                        "resNm": "경복궁",
                        "rnum": 3,
                        "sido": "서울특별시",
                        "ym": 201101
                    },
                   …
                ]
            },
            "numOfRows": 100,
            "pageNo": 1,
            "totalCount": 16
        },
        "header": {
            "resultCode": "0000",
            "resultMsg": "OK"
        }
    }
}
{% endhighlight %}

<br/>
정상적으로 데이터를 수신한 경우에는 [“response”][“header”][“resultMsg”]에 ‘OK’값이 회신되며, 요청한 전체 item의 수가 [“response”][“body”][‘totalCount’]에 저장되어 있다 데이터에서 보면 알 수 있듯이 현재 조회된 item의 수는 16개여서 조회 요청한 100개의 Row보다 작다. 그러나 “경기도”로 “SIDO”값을 지정하게 되는 경우에는 160여개의 데이터가 조회되므로 page 처리를 해 주어야 한다.
<br/><br/>
[CODE 3]에서는 nTotal의 값을 의 총수를 numOfRow(=100)으로 나눈 후 올림하여 총 페이지수를 구하고, 만약 페이지 수가 2 이상인 경우에는 While문 안에서 다시 데이터를 요청하게 처리한다.
<br/><br/>
[CODE 2]는 수신한 JSON 형식의 데이터 중 개별 item을 전달받아 해당하는 값을 jsonResult에 저장한다. 각 name별 데이터는 다음의 표와 같은 의미를 가진다. 자세한 부분은 공공 데이터 포털을 참조하기 바란다.
<br/><br/>

| 항목 | Name | 형식 | 설명 |
|--------|--------|--------|--------|
| 지역코드 | addrCd | INT | 지역코드(우편번호와 일치하지 않음) |
| 외국인 방문객수 | csForCnt | INT | |
| 내국인 방문객수 | csNatCnt | INT | |
| 관광지 코드| rnum | INT | 관광지에 고유 부여된 코드 값 |

{: .table table-striped}
<br/>

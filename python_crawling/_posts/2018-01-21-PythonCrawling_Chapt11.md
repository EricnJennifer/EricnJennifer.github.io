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
이제 코드를 살펴 보기로 하자. 위 코드는 유료 관광지 정보 및 입장객수를 조회하기 위한 코드이다. 실제 데이터를 조회하면 분기별로 업데이트 되고 있는 것으로 파악되며, 공공정보가 제공하는 서울특별시 입장 정보 데이터의 수량이 많은 편은 아니여서 조금 아쉬운 상황이다.

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
<br/><br/>
### 10.2 파이썬 그래프 모듈: matplotlib
<br/><br/>
파이썬의 장점을 들자면 파이썬은 여러 모듈이 그래프 기능을 지원하는데 가장 일반적으로 사용하는 모듈은 ‘matplotlib’ 이다. 일반적으로 “pip install matplotlib” 을 이용하여 설치가 가능한데 몇 가지 제약 사항이 있다.
<br/><br/>
‘matplotlib’는 파이썬 2.7 또는 3.4 이상을 사용한다면 정상적으로 설치가 안된다고 matplotlib의 설치 가이드(http://matplotlib.org/users/installing.html)에 다음과 같이 설명하고 있다.
<br/>
> 2.7 또는 3.4 이상의 경우에는 Microsoft Visual C++ 2008(64 bit or 32 bit: 파이썬 2.7) 또는 Microsoft Visual C++ 2010 (64 bit or 32 bit : 파이썬 3.4) 배포 패키지(redistributable package) 필요합니다

<br/>
먼저 [제어판]의 [프로그램 및 기능]으로 이동하여 “Microsoft Visual C++ Redistibutable…” 패키지가 설치되어 있는지 확인한다.
<br/><br/>

![](/asset/study/python_crawling/3/19.jpg)
[그림 1] Visual C++ 재 배포 파일 설치 여부 확인
{: .borderBox}

<br/>
필자의 경우에는 Visual Studio를 이용하여 프로그래밍을 하기 때문에 여러 버전의 재배포 패키지가 설치되어 있다. 만약 검색이 되지 않는 경우에는 [그림 2]와 같이 마이크로소프트 다운로드 사이트(https://www.microsoft.com/ko-KR/download/detail.aspx?id=53587)로 이동하여 다운로드 받은 후 설치한다(다운로드 경로의 경우 수시로 바뀌기 때문에 해당 URL이 접근이 불가한 경우 검색을 이용하여 찾기 바란다)
<br/><br/>

![](/asset/study/python_crawling/3/20.jpg)
[그림 2] Visual C++ 재배포 가능 패키지 다운로드
{: .borderBox}

<br/>
재배포 패키지가 설치되어 있는 경우 도스창(Command Prompot)을 뛰운 후 파이썬이 설치되어 있는 경로로 이동한 후

{% highlight python %}
c:\\[파이썬 설치 경로]\\pip install matplotlib
{% endhighlight %}

<br/>
를 실행하면 [그림 3]과 같이 설치 과정이 진행된다.
<br/><br/>

![](/asset/study/python_crawling/3/21.jpg)
[그림 3] “matplotlib” 설치
{: .borderBox}

<br/>
정상적으로 설치가 되면 ‘matplotlib’에서 사용하는 ‘numpy’, ‘pyparsing’ 및 ‘cycler’ 패키지가 함께 설치된다. 정상적으로 모듈이 설치되었는지 확인하기 위하여 python을 실행한 후 아래와 같이 코드를 작성한다.

{% highlight python %}
>>> from matplotlib import pyplot
>>> pyplot.plot([1,2,3,4],[10,30,20,40])
>>> pyplot.show()
{% endhighlight %}

<br/>
만약 모듈이 정상적으로 설치되었다면 ‘matplotlib’가 [그림 4]와 같이 그래프 폼을 화면에 나타낸다.
<br/><br/>

![](/asset/study/python_crawling/3/21.jpg)
[그림 4] “matplotlib”를 이용한 그래프 그리기
{: .borderBox}

<br/>
> 필자가 초기에 ‘matplotlib’를 설치했을 경우에는 ‘import’를 하면 프로그램 실행시 ‘font_manager.py’ 쪽에서 에러를 발생하면서 연관 모듈을 프로그램상에 올리지 못한다는 에러가 나오는 현상이 발견되었다.
> {% highlight python %}
File "C:\Anaconda3\lib\site-packages\matplotlib\font_manager.py", line 1412, in <module>
    fontManager = pickle_load(_fmcache)
{% endhighlight %}
> 국내 사이트에서 관련 오류에 대한 사항을 찾아도 해결책이 나오지 않다가 스택오버플로우(http://stackoverflow.com/questions/34004063/error-on-import-matplotlib-pyplot-on-anaconda3-for-windows-10-home-64-bit-pc)에서 관련 문제점 및 해결 방법을 발견하였다. 문제의 원인은 윈도우 폰트 디렉터리에서 폰트 정보를 가지고 오면서 발생하는 것이였고, 이를 해결하기 위해서는 “[파이썬 설치 경로]\Lib\site-packages\matplotlib” 아래에 존재하는 “font_manager.py” 파일의
> {% highlight python %}
def win32InstalledFonts(directory=None, fontext='ttf'):
{% endhighlight %}
> 함수의 일부분을 수정하면 해결된다.
> {% highlight python %}
key, direc, any = winreg.EnumValue( local, j)
if not is_string_like(direc):
    continue
if not os.path.dirname(direc):
    direc = os.path.join(directory, direc)
direc = direc.split(‘\0’, 1)[0] #파이썬 경로 문제 해결
direc = os.path.abspath(direc).lower()
{% endhighlight %}
> 한동안을 이 부분을 수정하여 사용하였는데 ‘matplotlib 2.0.0’버전으로 업데이트를 한 후 이 부분을 수정하지 않고 사용하여도 문제가 없이 동작하였다. 어느 부분에서 이 부분을 해결하였는지는 귀챠니즘이 발동하여 찾아보지 않았으니 독자분중에 문제가 발생하였다며, 상기 사항을 확인하여 보기 바란다.

<br/><br/>
### 10.3 출입국 관광 통계 서비스
<br/><br/>
출입국 관광 통계 서비스를 이용하면 기간, 방문객 국가의 검색 조건에 따라 출입국자수를 조회 서비스를 제공한다.  국가별 입국자수를 수집하기 위하여 다음의 코드를 작성한다.

{% highlight python %}
import os
import sys
import urllib.request
import datetime
import time
import json
from config import *

import matplotlib.pyplot as plt
import matplotlib
from matplotlib import font_manager, rc

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
def getNatVisitor(yyyymm, nat_cd, ed_cd):
    
    end_point = "http://openapi.tour.go.kr/openapi/service/EdrcntTourismStatsService/getEdrcntTourismStatsList"
    
    parameters = "?_type=json&serviceKey=" + access_key
    parameters += "&YM=" + yyyymm
    parameters += "&NAT_CD=" + nat_cd
    parameters += "&ED_CD=" + ed_cd
    
    url = end_point + parameters
    
    retData = get_request_url(url)
    
    if (retData == None):
        return None
    else:
        return json.loads(retData)

def main():

    jsonResult = []

    #중국: 112 / 일본: 130 / 미국: 275
    national_code = "112"
    ed_cd = "E"

    nStartYear = 2011
    nEndYear = 2017

    for year in range(nStartYear, nEndYear):
        for month in range(1, 13):

            yyyymm = "{0}{1:0>2}".format(str(year), str(month))

            jsonData = getNatVisitor(yyyymm, national_code, ed_cd)
    
            if (jsonData['response']['header']['resultMsg'] == 'OK'):
                krName = jsonData['response']['body']['items']['item']["natKorNm"]
                krName = krName.replace(' ', '')
                iTotalVisit = jsonData['response']['body']['items']['item']["num"]
                print('%s_%s : %s' %(krName, yyyymm, iTotalVisit))
                jsonResult.append({'nat_name': krName, 'nat_cd': national_code,
                                 'yyyymm': yyyymm, 'visit_cnt': iTotalVisit})

    cnVisit = []
    VisitYM = []
    index = []
    i = 0
    for item in jsonResult:
        index.append(i)
        cnVisit.append(item['visit_cnt'])
        VisitYM.append(item['yyyymm'])
        i = i + 1
            
    with open('%s(%s)_해외방문객정보_%d_%d.json' % (krName, national_code, nStartYear, nEndYear-1), 'w', encoding='utf8') as outfile:
        retJson = json.dumps(jsonResult,
                        indent=4, sort_keys=True,
                        ensure_ascii=False)
        outfile.write(retJson)
    

    #[CODE 2]
    font_location = "c:/Windows/fonts/malgun.ttf"
    font_name = font_manager.FontProperties(fname=font_location).get_name()
    matplotlib.rc('font', family=font_name)

    plt.xticks(index, VisitYM)
    plt.plot(index, cnVisit)
    plt.xlabel('방문월')
    plt.ylabel('방문객수')
    plt.grid(True)
    plt.show()           
    
if __name__ == '__main__':
    main()
{% endhighlight %}

<br/>
코드를 수행하면 특정 기간 동안 특정 국가의 입국 인원수를 가지고 온 후 그래프를 생성해 낸다. [그림 5]는 2011.01부터 2016.12월까지 중국인 입국자수를 그래프로 표시한 것이다.
<br/><br/>

![](/asset/study/python_crawling/3/23.jpg)
[그림 5] 국내 중국 입국자수 추이 (2011.01 ~ 2016.12)
{: .borderBox}

<br/>
이제 코드를 살펴 보도록 하자.

{% highlight python %}
#[CODE 1]
def getNatVisitor(yyyymm, nat_cd, ed_cd):
    
    end_point = "http://openapi.tour.go.kr/openapi/service/EdrcntTourismStatsService/getEdrcntTourismStatsList"
    
    parameters = "?_type=json&serviceKey=" + access_key
    parameters += "&YM=" + yyyymm
    parameters += "&NAT_CD=" + nat_cd
    parameters += "&ED_CD=" + ed_cd
    
    url = end_point + parameters
    
    retData = get_request_url(url)
    
    if (retData == None):
        return None
    else:
        return json.loads(retData)
{% endhighlight %}

<br/>
출입국 데이터 조회를 위한 End Point는 “http://openapi.tour.go.kr/openapi/service/EdrcntTourismStatsService/getEdrcntTourismStatsList” 이며 전달하는 요청 변수는 다음의 표와 같다.
<br/><br/>

| 항목 | 파라미터명 | 형식 | 설명 |
|--------|--------|--------|--------|
| 년월 | YM | YYYYMM(ex: 201703) | 검색 연월 |
| 국가코드 | NAT_CD | DDD (ex: 100) | 세자리 국가 코드 |
| 출/입국 | ED_CD | ‘D’ or ‘E’ | D: 국민 해외 관광객<br/>E: 방한 외래 관광객 |
{: .table table-striped}
<br/>
전달하는 변수는 UTF-8 형식으로 인코딩 되어 있어야 하며 다음 표는 국가 코드중 일부이다. 자세한 사항은 공공데이터 포털 출입국관광통계서비스 활용가이드를 참조하기 바란다.
<br/><br/>

| 국가명 | 국가 코드 |
|--------|--------|
| 한국 | 100 |
| 중국 | 112 |
| 일본 | 130 |
| 미국 | 275 |
| 영국 | 316 |
{: .table table-striped}
<br/>
정상적으로 데이터를 수신한 경우에는 [“response”][“header”][“resultMsg”]에 ‘OK’값이 회신되며, 수신한 JSON 형식의 데이터 중 각 name별 데이터는 다음 표와 같은 의미를 가지며 수신한 JSON 형식의 예는 다음과 같다.
<br/><br/>

| 항목 | Name | 형식 | 설명 |
|--------|--------|--------|--------|
| 국가명 | natKorNm | STRING | 국가 코드에 해당하는 한글 국가명 |
| 입출국수 | csForCnt | INT |  |
{: .table table-striped}
<br/>

{% highlight python %}
{
    "response": {
        "body": {
            "items": {
                "item": {
                    "ed": "방한외래관광객",
                    "edCd": "E",
                    "natCd": 112,
                    "natKorNm": "중  국",
                    "num": 91252,
                    "rnum": 1,
                    "ym": 201101
                }
            },
            "numOfRows": 10,
            "pageNo": 1,
            "totalCount": 1
        },
        "header": {
            "resultCode": "0000",
            "resultMsg": "OK"
        }
    }
}
{% endhighlight %}

<br/>

{% highlight python %}
#[CODE 2]
    font_location = "c:/Windows/fonts/malgun.ttf"
    font_name = font_manager.FontProperties(fname=font_location).get_name()
    matplotlib.rc('font', family=font_name)

    plt.xticks(index, VisitYM)
    plt.plot(index, cnVisit)
    plt.xlabel('방문월')
    plt.ylabel('방문객수')
    plt.grid(True)
    plt.show()
{% endhighlight %}

<br/>
[CODE 2]는 ‘matplotlib’를 이용하여 그래프를 그리는 코드를 포함하고 있다. 이 책을 읽는 독자들은 아마 한번 정도씩은 문자열 처리를 하면서 ‘UTF-8’과 ‘CP949’ 때문에 고생을 한적이 있을 것이다. ‘matplotlib’에서도 마찬가지로 그래프를 그리면서 한글을 처리하고자 하면 깨지는 현상이 발생한다. 이는 모듈이 기본으로 “sans-serif” 폰트를 사용하기 떄문이다. 이를 해결하기 위해서 ‘matplotlib’의 ‘font_manager’와 ‘resource’에 윈도우에서 사용하는 한글 폰트를 지정하여 주면 해결된다(만약 리눅스나 Mac을 사용하는 경우에는 시스템 해당 경로를 지정해 주면 된다).
<br/><br/>
‘matplotlib’에 관하여는 [PYTHON 시각화 >> 2. 그래프를 그리자 - matplotlib](https://ericnjennifer.github.io/python_visualization/2018/01/21/PythonVisualization_Chapt2.html)에서 좀더 자세히 다루도록 하겠다.

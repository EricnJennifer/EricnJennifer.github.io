---
layout: post_study
title:  6. 지도 시각화 – Folium
date: 2018-01-21 14:01:00
categories: python_visualization
---
## 6. 지도 시각화 - 포리움(Folium)의 설치 및 활용
<br/><br/><br/>
Folium(https://folium.readthedocs.io/en/latest) 은 ‘Open Street Map’과 같은 지도데이터에 ‘Leaflet.js’를 이용하여 위치정보를 시각화하기 위한 라이브러리다. 기본적으로 ‘GeoJSON(http://geojson.org/)’ 형식 또는 ‘topoJSON’ 형식으로 데이터를 지정하면, 오버레이를 통해 마커의 형태로 위치 정보를 지도상에 표현할 수 있다.
<br/><br/>
우리가 현실세계에서 지도상의 위치를 표시하기 위해 ‘위도(latitude)’와 ‘경도(longitude)’를 사용한다. ‘위도’는 적도를 기준으로 하여 남쪽으로 남극점까지 90°, 북쪽으로 북극점까지 90°로 나누어 표시한다(우리나라는 적도의 북쪽인 북위 34° ~ 38° 사이에 위치). ‘경도(longitude)는 런던 그리니치천문대를 지나는 본초자오선을 중심으로 동서로 나누어 동경 180°, 서경 180°로 분리한다(서울의 경우 동경 127°에 위치).
<br/><br/>
‘GeoJSON’은 다양한 지리 데이터 구조를 인코딩하기 위한 형식을 제공한다. 객체는 지오메트리, 지형지물을 표시할 수 있으며, Point, LineString, Polygon, MultiPoint, MultiLineString, MultiPolygon 및 GeometryCollection과 같은 속성들을 지정할 수 있다. 다음은 GeoJSON 형식을 나타낸다.

{% highlight python %}
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [125.6, 10.1]
  },
  "properties": {
    "name": "Dinagat Islands"
  }
}
{% endhighlight %}

<br/>
TopoJSON은 GeoJSON의 확장형식으로 각 영역을 아크(arcs)들의 영역으로 구분하여 표시하는 기능을 제공해주어 연산량을 적게 해주는 장점을 가지고 있다.
<br/><br/>
특정 지역의 위도와 경도값을 찾는 방법은 구글맵이나 네이버지도와 같은 지도 서비스 포털을 이용할 수 있다. 예제로 사용할 위치를 구하기 위하여 구글맵(https:www.google.com/maps)을 호출한 후 [그림 1]과 같이 ‘서울특별시청’을 검색하여 보자.
<br/><br/>

![](/asset/study/python_visualization/27.png)
[그림 1] 구글맵을 통한 지리정보 찾기
{: .borderBox}

<br/>
[그림 1]의 상단 URL 정보를 보면 인자값으로 위도와 경도 정보가 전달되는 것을 알 수 있다. 그러나 이 값은 현재 우리가 검색한 ‘서울특별시청’의 위치좌표가 아니라 지금 표시한 지도의 중간 위·경도 값을 의미한다. 정확한 좌표값을 얻기 마커를 선택한 후 오른쪽 마우스를 누르면 [이곳이 궁금한가요?]라는 메뉴가 나타난다. 클릭을 하면 [그림 2]와 같이 세부 정보가 하단에 나타난다.
<br/><br/>

![](/asset/study/python_visualization/28.png)
[그림 2] 구글맵을 통한 지리정보 찾기
{: .borderBox}

<br/>
‘이곳이 궁금한가요?’를 통하여 얻은 위도 값은 37고 56분 63.45초이고 경도의 값은 126분 97도 78.93초이다.
<br/><br/><br/>
### 6.1 Folium 설치 및 객체 생성
<br/><br/>
먼저 도스창을 띄워 파이썬이 설치된 경로로 이동하여 ‘folium’을 인스톨한다.

{% highlight python %}
[파이썬 설치 경로]>pip install folium
{% endhighlight %}

<br/>
인스톨이 정상적으로 되었으며 파이썬 쉘을 실행시킨 후 ‘folium’을 임포트(import)하는 것만으로준비는 끝난다.
<br/><br/>
초기 객체의 생성은 ‘.Map()’ 메소드에 중심 좌표값을 지정함으로 간단하게 생성할 수 있다.

{% highlight python %}
>>> import folium
>>> map_osm = folium.Map(location=[37.566345, 126.977893])
>>> map_osm.save('d:/temp/chicken_data/map1.html') #파일이 저장될 위치
{% endhighlight %}

<br/>
서울특별시청의 위도는 37도 56분 63.45초 이고, 경도는 126도 97분 78.93초이므로 포리움맵을 생성하면서 서울특별시청의 값을 중심값으로 하여 지도를 생성한다. 만약 주피터 노트북을 개발환경으로 사용하면 단순하게 생성된 객체를 호출(>>>map_osm)하면 지도를 보여주지만, 우리는 파이썬 쉘을 사용하므로 생성된 객체를 저장하여야 확인할 수 있다. 생성된 객체를 특정 위치에 저장한 후 탐색기를 이용하여 해당 파일을 브라우저로 열면 [그림 3]과 같이 서울 시청을 중심값으로 하는 지도파일이 호출되는 것을 확인할 수 있다.
<br/><br/>

![](/asset/study/python_visualization/29.png)
[그림 3] 서울특별시청을 중심으로 포리움 맵 생성
{: .borderBox}

<br/>
초기 화면의 크기를 지정하는 방법은 ‘zoom_start’ 속성을 주면 가능하다. [그림 4]는 초기 화면의 크기를 확대하여 보여주는 예이다.

{% highlight python %}
>>> map_osm = folium.Map(location=[37.566345, 126.977893], zoom_start=17)
>>> map_osm.save('d:/temp/chicken_data/map2.html')
{% endhighlight %}

<br/>

![](/asset/study/python_visualization/30.png)
[그림 4] zoom_start 속성값을 부여하여 초기 지도의 확대
{: .borderBox}

<br/>
### 6.2 다른 유형의 맵 호출
<br/><br/>
포리움은 기본적으로 ‘Open Street Map’을 기반으로 동작하지만 내부적으로는 ‘Stamen Terrain’,  ‘Stamen Toner’, ‘Mapbox Bright’, 와 ‘Mapbox Control room tiles’ 형식을 내장하고 있다.

{% highlight python %}
>>> map_osm = folium.Map(location=[37.566345, 126.977893], zoom_start=17, tiles='Stamen Terrain')
>>> map_osm.save('d:/temp/chicken_data/map3.html')
>>> map_osm = folium.Map(location=[37.566345, 126.977893], zoom_start=17, tiles='Stamen Toner')
>>> map_osm.save('d:/temp/chicken_data/map4.html')
{% endhighlight %}

<br/>
[그림 5]는 ‘Stamen Terrain’형식의 지도를 사용한 것이고 [그림 6]은 ‘Stamen Toner’형식을 사용한 것이다.
<br/><br/>

![](/asset/study/python_visualization/31.png)
[그림 5] Stamen Terrain 형식의 지도 생성
{: .borderBox}

<br/>

![](/asset/study/python_visualization/32.png)
[그림 6] Stamen toner 형식의 지도 생성
{: .borderBox}

<br/>
‘Cloudmade’나 ‘Mapbox’를 사용하는 경우에는 사이트에 등록시 발급받은 API 키 정보를 아래와 같이 ‘API_key’ 속성으로 지정하여 준다.

{% highlight python %}
>>> map_osm = folium.Map(location=[37.5660, 126.9757], tiles=’Mapbox', API_key=’API키값’)
{% endhighlight %}

<br/>
### 6.3 마커(Marker)와 팝업(Popup)의 설정
<br/><br/>
포리움은 다양한 형식의 마커(특정 위치를 표시하는 표식)과 마커를 클릭하였을 때 나타나는 정보(Popup)을 지정할 수 있다.

{% highlight python %}
>>> map_osm = folium.Map(location=[37.566345, 126.977893], zoom_start=17)
>>> folium.Marker([37.566345, 126.977893], popup='서울특별시청').add_to(map_osm)
>>> folium.Marker([37.5658859, 126.9754788], popup='덕수궁').add_to(map_osm)
>>> map_osm.save('d:/temp/chicken_data/map5.html')
{% endhighlight %}

<br/>
마커의 생성은 ‘.Marker()’ 메소드를 이용하여 생성한다. 마커의 인자값으로 위경도 값 리스트와 마커를 클릭할 시 보여줄 문자열을 전달하고, 생성한 포리움 객체에 추가(.add_to())하면 간단하게 마커를 생성할 수 있다. [그림 7]은 생성된 포리움 맵의 마커를 클릭하여 팝업을 생성한 예이다.
<br/><br/>

![](/asset/study/python_visualization/33.png)
[그림 7] 포리움 마커의 생성
{: .borderBox}

<br/>
포리움 마커는 부트스트랩(bootstrap)을 이용하여 아이콘 타입을 설정할 수 있으며, 범위를 설정하기 위하여 circle 속성을 줄 수 있다.

{% highlight python %}
>>> map_osm = folium.Map(location=[37.566345, 126.977893], zoom_start=17)
>>> folium.Marker([37.566345, 126.977893], popup='서울특별시청', icon=folium.Icon(color='red',icon='info-sign')).add_to(map_osm)
>>> folium.CircleMarker([37.5658859, 126.9754788], radius=100,color='#3186cc',fill_color='#3186cc', popup='덕수궁').add_to(map_osm)
>>> map_osm.save('d:/temp/chicken_data/map6.html')
{% endhighlight %}

<br/>
[그림 8]은 덕수궁의 위치를 좀더 크게 마커로 표시하고, 서울특별시청은 적색의 ‘info-sign’ 마커로 표시한 예이다.
<br/><br/>

![](/asset/study/python_visualization/34.png)
[그림 8] 마커의 변경
{: .borderBox}

<br/>
### 6.4 JSON 활용 데이터
<br/><br/>
geoJSON 형식과 topoJSON은 지도상의 경계 영역 등을 표시하기에 효율적이다. https://github.com/southkorea 를 방문하면 통계청 데이터를 기반으로 하여 작성한 JSON 형식 행정구역 데이터를 얻을 수 있다(https://github.com/southkorea/southkorea-maps). 해당 데이터를 다운로드 받아 특정 위치에 저장한 후 파일을 읽어 ‘GeoJson()’메소드에 링크 시킨다(직접 open 파라미터를 사용하여도 되는데 이 때 디코딩 에러가 나는 경우가 있으므로, 예제에서는 파일을 직접 읽은 후 데이터를 링크 시켰다).

{% highlight python %}
>>> import folium
>>> import json
>>> map_osm = folium.Map(location=[37.566345, 126.977893])
>>> rfile = open('d:/temp/chicken_data/skorea_provinces_geo_simple.json', 'r', encoding='utf-8').read()
>>> jsonData = json.loads(rfile)
>>> folium.GeoJson(jsonData, name='json_data').add_to(map_osm)
>>> map_osm.save('d:/temp/chicken_data/map7.html')
{% endhighlight %}

<br/>
[그림 9]는 행정구역이 “MultiPolygon“ 형식으로 입력되어 있는 JSON 데이터를 불러들여 지도에 표시한 예이다.
<br/><br/>

![](/asset/study/python_visualization/35.png)
[그림 9] GeoJSON 데이터 매핑
{: .borderBox}

<br/>
### 6.5 네이버 주소 API를 이용하여 지도에 표시
<br/><br/>
포리움을 사용하는 방법을 이해했으니 실제 데이터를 가지고 지도에 표시하는 방법에 대해 알아보도록 하겠다. 본 절에서 다룰 데이터는 서울열린데이터광장에서 제공하는 서울시 초등학교현황(http://data.seoul.go.kr/dataList/datasetView.do?infId=OA-12059&srvType=S&serviceKind=1&currentPageNo=1)을 다운로드 받아 사용하였다.
> CSV 파일 : [서울시 초등학교 현황.csv](https://ericnjennifer.github.io/asset/study/sample_data/서울시 초등학교 현황.csv)

먼저 다음의 코드를 작성하자.

{% highlight python %}

import folium
import pandas as pd
import urllib.request
import datetime
import time
import json
import webbrowser

#[CODE 1]
def get_request_url(url):
    req = urllib.request.Request(url)
    req.add_header("X-Naver-Client-Id", "NAVER API ID")
    req.add_header("X-Naver-Client-Secret", "NAVER API SECRET")
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
def getGeoData(address):
    base = "https://openapi.naver.com/v1/map/geocode"
    
    try:
        parameters = "?query=%s" % urllib.parse.quote(address)
    except:
        return None
    
    url = base + parameters
    
    retData = get_request_url(url)
    if retData == None:
        return None

    jsonAddress = json.loads(retData)

    if 'result' in jsonAddress.keys():
        latitude = jsonAddress['result']['items'][0]['point']['y']
        longitude = jsonAddress['result']['items'][0]['point']['x']
    else:
        return None
    
    return [latitude, longitude]

def main():
   
    #[CODE 3]
    map = folium.Map(location=[37.5103, 126.982], zoom_start=12)

    filename = 'd:/tmp/서울시 초등학교 현황.csv'
    df = pd.DataFrame.from_csv(filename, encoding='CP949', index_col=0, header=0)
    geoData = []
    
    #[CODE 4]
    for index, row in df.iterrows():
        geoData = getGeoData(row['주소'])
        if geoData != None:
            folium.Marker(geoData, popup=row['학교명'], icon=folium.Icon(color='red')).add_to(map)
    
    svFilename = 'd:/tmp/elementary_school.html'
    map.save(svFilename)
    webbrowser.open(svFilename)  
    
if __name__ == "__main__":
    main()
{% endhighlight %}

<br/>
해당 코드를 수행하면 [그림 1]과 같이 프렌차이즈 위치 정보를 포함하는 지오맵(GeoMap)이 생생성된 것 확인할 수 있다.
<br/><br/>

![](/asset/study/python_visualization/es_map.png)
[그림 1] 서울특별시 초등학교 현황
{: .borderBox}

<br/>
네이버로 부터 주소를 가지고 오는 방법은 [PYTHON 크롤링 >> 9. 네이버 지도 API(주소, 좌표변환) 활용](https://ericnjennifer.github.io/python_crawling/2018/01/21/PythonCrawling_Chapt10.html)을 참조하기 바란다.
<br/><br/>
[CODE 3]은 저장되어 있는 서울시 초등학교 현황 파일을 읽어와 DataFrame의 형식으로 저장한다.
<br/><br/>
[CODE 4]는 DataFrame내의 데이터를 한 행(row)씩 가져오면서 '주소' 컬럼의 데이터를 네이버 API를 호출하기 위한 함수로 전달하고 반환값을 받는다. 이후 반환값이 있는 경우에는 .Marker() 메소드를 이용하여 해당 위,경도값에 학교명을 팝업으로 지정하여 추가한 후 반복이 종료되면  webbrowser.open()을 이용하여 화면에 나타낸다.
---
layout: post_study
title:  7. 트위터 스트립(Stream) 수집하기
date: 2018-01-21 06:01:00
categories: python_crawling
---
## 7. 트윗(Tweet) 스트림 가지고 오기
<br/><br/><br/>
페이스북에서는 현재까지 전체 타임라인을 검색하여 업데이트 된 정보를 제공해 주는 API를 제공해 주고 있지 않다. 트위터의 경우 생산되는 모든 데이터는 공유자산이라는 개념으로 시작하였으나 페이스북은 자신의 친구와 사생활을 공유하는 목적으로 개발되었기에 그들의 개인 보호 정책상에 맞지 않는 이유라 할 수 있겠다. 또한 페이스북의 데이터는 트위터에 비해 상대적으로 큰 사이즈이기 때문에 스트리밍 데이터를 처리하기에는 부적절 하다고 볼 수 있다. 최근 들어 페이스북은 “Webhooks”라는 API를 제공하고 있다. 그러나 웹 훅(과거에는 실시간 업데이트라고 함)의 경우에는 실시간으로 자신이 등록해 놓은 받아보기 리스트를 통해서 데이터의 업데이트 여부를 확인할 수 있다.
<br/><br/>
이러한 페이스북의 정책은 실제로 소셜 데이터 분석에 불편함을 제공한다. 트위터의 경우에는 필터를 통해 주된 관심사의 트윗이 얼마나 발생하고 있는지 확인할 수 있고, 이 데이터를 바탕으로 추가 분석을 할 수 있는 장점을 제공한다.
<br/><br/>
특정 검색어에 대한 스트림 데이터를 가지고 오기 위하여 다음의 코드를 작성한다


{% highlight python %}
import oauth2
import urllib
import json
import urllib.request
import datetime
import time
from config import *

#[CODE 1]
class TWoauth():

    def __init__(self, consumer_key, consumer_secret, access_key, access_secret):
        self.oauth_consumer = oauth2.Consumer(key=consumer_key, secret=consumer_secret)
        self.oauth_token = oauth2.Token(key=access_key, secret=access_secret)
        self.signature_method_hmac_sha1 = oauth2.SignatureMethod_HMAC_SHA1()
        self.http_method = "GET"
        self.http_handler = urllib.request.HTTPHandler(debuglevel=0)
        self.https_handler = urllib.request.HTTPSHandler(debuglevel=0)

    def getTWRequest(self, url, method, parameters):
        
        req = oauth2.Request.from_consumer_and_token(self.oauth_consumer,
                                                token=self.oauth_token,
                                                http_method=self.http_method,
                                                http_url=url,
                                                parameters=parameters)
        req.sign_request(self.signature_method_hmac_sha1, self.oauth_consumer, self.oauth_token)

        if method == "POST":
            encoded_post_data = req.to_postdata()
        else:
            encoded_post_data = None

        to_url = req.to_url()

        opener = urllib.request.OpenerDirector()
        opener.add_handler(self.http_handler)
        opener.add_handler(self.https_handler)

        response = opener.open(to_url, encoded_post_data)
        
        return response

def getTwitterTwit(tweet, jsonResult):

    tweet_id = tweet['id_str']
    tweet_message = '' if 'text' not in tweet.keys() else tweet['text']
    
    screen_name = '' if 'user' not in tweet.keys() else tweet['user']['screen_name']

    tweet_link = ''
    if tweet['entities']['urls']: #list
        for i, val in enumerate(tweet['entities']['urls']):
            tweet_link = tweet_link + tweet['entities']['urls'][i]['url'] + ' '
    else:
        tweet_link = ''
        
    hashtags = ''
    if tweet['entities']['hashtags']: #list
        for i, val in enumerate(tweet['entities']['hashtags']):
            hashtags = hashtags + tweet['entities']['hashtags'][i]['text'] + ' '
    else:
        hashtags = ''

    if 'created_at' in tweet.keys():
        # Twitter used UTC Format. EST = UTC + 9(Korean Time) Format ex: Fri Feb 10 03:57:27 +0000 2017
        tweet_published = datetime.datetime.strptime(tweet['created_at'],'%a %b %d %H:%M:%S +0000 %Y')
        tweet_published = tweet_published + datetime.timedelta(hours=+9)
        tweet_published = tweet_published.strftime('%Y-%m-%d %H:%M:%S')   
    else:
        tweet_published = ''

    num_favorite_count = 0 if 'favorite_count' not in tweet.keys() else tweet['favorite_count']
    num_comments = 0    
    num_shares = 0 if 'retweet_count' not in tweet.keys() else tweet['retweet_count']
    num_likes = num_favorite_count
    num_loves = num_wows = num_hahas = num_sads = num_angrys = 0
    
    jsonResult.append({'post_id':tweet_id, 'message':tweet_message,
                    'name':screen_name, 'link':tweet_link, 
                    'created_time':tweet_published, 'num_reactions':num_favorite_count,
                    'num_comments':num_comments, 'num_shares':num_shares,
                    'num_likes':num_likes, 'num_loves':num_loves,
                    'num_wows':num_wows, 'num_hahas':num_hahas,
                    'num_sads':num_sads, 'num_angrys':num_angrys, 'hashtags': hashtags})

#[CODE 2]
def fetch(filter, jsonResult):

    twoauth = TWoauth(CONSUMER_KEY, CONSUMER_SECRET, ACCESS_TOKEN, ACCESS_SECRET)

    url = "https://stream.twitter.com/1.1/statuses/filter.json" 
    parameters = []
    parameters.append({'track', filter})

    try:
        f = twoauth.getTWRequest(url, "GET", parameters)
        while True:
            line = f.readline()
            if line:
                try:
                    tweet = json.loads(line.decode('utf-8'))
                    print('#####[Scrapped Time : %s]' % datetime.datetime.now())
                    print(tweet['text'])
                    getTwitterTwit(tweet, jsonResult)
                except ValueError as ve:
                    print (ve)
                except KeyError as e:
                    print(e)
            else:
                time.sleep(0.1)
    except KeyboardInterrupt:
        # Ctrl-C Detected
        f.close()
        with open('%s_twitter.json' % (filter), 'w', encoding='utf8') as outfile:
            retJson = json.dumps(jsonResult,
                            indent=4, sort_keys=True,
                            ensure_ascii=False)
            outfile.write(retJson)
        
        print ('%s_twitter.json SAVED' % (filter))
    
if __name__ == '__main__':
    jsonResult = []
    filter_name = '탄핵,박근혜,광화문'
    fetch (filter_name, jsonResult)
{% endhighlight %}

<br/>
코드를 수행하면 스트리밍되는 관심어에 대한 내용이 화면에 나타나고 Control-C를 누르면 해당 .py 폴더에 filter_name_twitter.json 파일이 생성되고 프로그램이 종료된다. [그림 1]은 코드가 동작되는 화면을 보여준다.
<br/><br/>

![](/asset/study/python_crawling/3/10.jpg)
[그림 1] 실시간으로 수집되는 트윗
{: .borderBox}

<br/>
[그림 1]의 스크랩되는 시간을 보면 실시간으로 증가하면서 나타나는 것을 확인할 수 있다. 이제 코드를 살펴보도록 하자.

{% highlight python %}
#[CODE 1]
class TWoauth():

    def __init__(self, consumer_key, consumer_secret, access_key, access_secret):
        self.oauth_consumer = oauth2.Consumer(key=consumer_key, secret=consumer_secret)
        self.oauth_token = oauth2.Token(key=access_key, secret=access_secret)
        self.signature_method_hmac_sha1 = oauth2.SignatureMethod_HMAC_SHA1()
        self.http_method = "GET"
        self.http_handler = urllib.request.HTTPHandler(debuglevel=0)
        self.https_handler = urllib.request.HTTPSHandler(debuglevel=0)

    def getTWRequest(self, url, method, parameters):
        
        req = oauth2.Request.from_consumer_and_token(self.oauth_consumer,
                                                token=self.oauth_token,
                                                http_method=self.http_method,
                                                http_url=url,
                                                parameters=parameters)
        req.sign_request(self.signature_method_hmac_sha1, self.oauth_consumer, self.oauth_token)

        if method == "POST":
            encoded_post_data = req.to_postdata()
        else:
            encoded_post_data = None

        to_url = req.to_url()

        opener = urllib.request.OpenerDirector()
        opener.add_handler(self.http_handler)
        opener.add_handler(self.https_handler)

        response = opener.open(to_url, encoded_post_data)
        
        return response
{% endhighlight %}

<br/>
[CODE 1]은 oauth2 모듈을 이용하여 트위터에 인증을 수행하기 위하여 TWoauth 클래스를 생성하였다. 초기 클래스를 생성하면서 우리는 사용할 ‘consumer_key/secret’과 ‘access_token/secret’을 저장하고 기본적으로 Token을 요구하기 위하여 필요한 암호화 방식과 서버에 접근 방식을 저장하였다. 또한 인증이 완료되고 서비스 API를 사용하기 위해서는 인증 정보를 유지하여야 하므로 Opener를 생성하기 위하여 기본적으로 ‘http_handler’와 ‘https_handler’를 설정하였다.
<br/><br/>
‘getTWRequest()’ 함수는 실질적으로 인증 과정을 수행한다. ‘oauth2.Request.from_consumer_and_token()’ 함수를 이용하여 ‘access_token’을 요청하는 ‘request’를 생성한다. [그림 2]는 ‘access_token’을 요청하기 위하여 기본적인 값들을 생성한 데이터이다.
<br/><br/>

![](/asset/study/python_crawling/3/11.jpg)
[그림 2] Request를 요청할 파라미터를 위한 기본 데이터를 생성
{: .borderBox}

<br/>
‘sign_request()’를 이용하여 request를 지정한 암호화 방식으로 암호화한 시그네처(signature)를 생성하고, 추가한다.
<br/><br/>

![](/asset/study/python_crawling/3/12.jpg)
[그림 3] oauth_signature의 추가
{: .borderBox}

<br/>
만약 “POST” 방식을 이용하는 경우에는 ‘to_postdata()’를 이용하여 파라미터를 인코딩한다. 우리는 “GET”방식을 이용하기 때문에 사용하지 않는다. 이제 트위터 API를 사용하기 위한 URL이 완성되었다.
<br/><br/>
다음은 "/statuses/filter.json"에 접근하기 위한 URL의 예를 나타낸다.
<br/>
> https://stream.twitter.com/1.1/statuses/filter.json?oauth_consumer_key=MlQHYIaVcsmjr2h4defzRi88H&oauth_nonce=98691135&oauth_signature=6bIsjX0caJt7vQbKYuCJdPU1u%2Fk%3D&oauth_signature_method=HMAC-SHA1&oauth_token=836816176235888640-6lFKqe8OkkUA8NxYz6cWRdv7cJTEALb&%ED%83%84%ED%95%B5%2C%EB%B0%95%EA%B7%BC%ED%98%9C%2C%EA%B4%91%ED%99%94%EB%AC%B8=track&oauth_timestamp=1488649098&oauth_version=1.0&oauth_body_hash=2jmj7l5rSw0yVb%2FvlWAYkK%2FYBwk%3D

<br/>
해당 URL로 데이터를 요청하게 되는 경우 ‘oauth_token’을 계속 유지하여야 하기 때문에 ‘urllib.request.OpenerDirector()’를 이용하여 핸들러를 등록하고 유지한다.
<br/><br/>
#[CODE 2]는 TWoauth클래스를 생성하고, 검색어 필터를 지정한다. 검색어는 ‘,’를 이용하여 복수개를 지정할 수 있다.

{% highlight python %}
#[CODE 2]
def fetch(filter, jsonResult):
    
    twoauth = TWoauth(CONSUMER_KEY, CONSUMER_SECRET, ACCESS_TOKEN, ACCESS_SECRET)

    url = "https://stream.twitter.com/1.1/statuses/filter.json" 
    parameters = []
    parameters.append({'track', filter})
{% endhighlight %}

<br/>
‘/statuses/filter.json’ 엔드포인트(endpoint)는 필터(track)의 조건에 맞는 모든 공개 트윗을 반환해준다. “POST”와 “GET” 방식을 모두 허용하지만 매개변수가 너무 많아 길어지게 되면 요청이 거부될 수 있으므로, 매개변수가 많은 경우에는 “POST’를 사용하는 것이 좋다.
<br/><br/>
기본적으로 최대 400개의 검색어와 5,000개의 사용자 ID를 허용하므로 최근 이슈가 되고 있는 검색어를 분석하기에 충분할 수 있다고 볼 수 있다.
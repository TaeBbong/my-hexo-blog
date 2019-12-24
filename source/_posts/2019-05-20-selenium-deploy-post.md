---
layout: post
title:  "[개발TIP&기록] 웹 크롤링을 위해 Selenium Ubuntu 서버에 배포하기"
date:   2019-05-20
excerpt: "Selenium ChromeDriver 설치 가이드"
tag: 
- Selenium
- Python
- Chrome
- Chromedriver
- Ubuntu

comments: true
---


## 환경
* ```Ubuntu 18.0.4``` (AWS EC2 인스턴스)
* ```Chrome 74```


## 들어가며
* 웹 크롤링을 전문으로 외주를 진행하면, ```Selenium```을 정말 많이 쓰게 됩니다.(절대 웹 테스트용으로는 안쓰네요ㅎ..)
* 실 서비스에 항상 적합한 툴은 아니지만, 자주 안 돌려도 되거나, 찾을 데이터가 많지 않은 경우 종종 배포까지 사용하기도 합니다.
* ```Selenium```을 서버에서 사용하려면 ```Chrome``` 및 ```Chromedriver```를 서버에 세팅해야하는데, 초반에는 자료가 많이 없어 헤맸었습니다.
* 그래서 기록을 목적으로 설치 및 배포 방법을 정리해보려합니다.


## Chrome 설치
* 많이 놓치는 부분이 ```Chromedriver```를 쓰려면 ```Chrome```도 설치되어야 한다는 점입니다.
* ```Chrome```을 ```Ubuntu```에서 설치하려면 다음과 같은 명령어를 입력합니다.

~~~bash
$ wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add - 
$ sudo sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'
~~~

* 위와 같은 명령어로 ```apt-get```을 위한 준비를 진행합니다.
* 이후 다음 명령어를 입력해 실제 설치를 진행합니다.

~~~bash
$ sudo apt-get update
$ sudo apt-get install google-chrome-stable
~~~

* 이를 통해 안정화 버전의 크롬을 설치할 수 있습니다.
* ***크롬 버전***을 확인해야합니다!

~~~bash
$ google-chrome --version
~~~

* 현재 시점에는 74.x.x 버전으로 설치가 됩니다.
* 이 버전에 호환되는 ```Chromedriver```를 다운로드 받아야합니다.


## Chromedriver 다운로드
* 다음 링크에서 버전에 맞는 ```Chromedriver```를 찾으면 됩니다.
* [Chromedriver](http://chromedriver.chromium.org/downloads)
* 이 프로젝트에서는 74버전에 맞게 진행하겠습니다.
* 버전을 선택하면 OS 별로 다운로드 받을 수 있는 링크가 보이는 페이지로 이동하는데, 
* 여기서 ```linux``` 링크를 오른쪽 마우스로 복사합니다.
* 이후 서버에서 다음과 같은 명령어로 다운로드 받습니다.

~~~bash
$ wget https://chromedriver.storage.googleapis.com/74.0.3729.6/chromedriver_linux64.zip
~~~

* 다운로드된 압축파일을 풀면 됩니다!
* 압축파일을 풀기 위해 ```unzip```을 설치합니다.

~~~bash
$ sudo apt-get install unzip
~~~

* 압축을 풀어봅시다!

~~~bash
$ unzip chromedriver_linux64.zip
~~~

* 압축을 풀면 같은 디렉토리에 ```chromedriver``` 파일이 생성된걸 볼 수 있습니다!
* 이를 프로젝트 폴더로 이동시켜서(본인이 설정한 path) 사용하시면 됩니다.


## 예시: Django 프로젝트에서 사용하기
* ```Django```에서 이를 연동하여 사용할 수 있습니다.
* 당연히 ```selenium```을 설치해야겠죠?
* 가상환경에서 설치해봅시다.

~~~bash
(myvenv) $ pip install selenium
~~~

* 배포할 때에는 실제로 크롬 창이 열릴 필요가 없죠!
* 그래서 ```headless``` 상태로 만들어서 돌려야합니다.
* 다음과 같이 코드를 작성해볼게요.

~~~python
driver_path = os.path.join(os.path.dirname(__file__), 'chromedriver') # Chromedriver Path 가져오기
options = Options() # Option 객체 생성
options.headless = True # headless option True 설정
driver = webdriver.Chrome(driver_path, options=options) # WebDriver 객체 생성
~~~

* 이제 설정이 완료되었습니다. 
* 나머지는 로컬에서 작업하시는 것과 동일하기 때문에 바로 ```Selenium``` 프로젝트를 시작하시면 됩니다!
* ```Selenium```이 꽤 무겁고, 시간도 오래걸려서 물론 서버에서 사용할 때 많은 주의를 기울여야 합니다.(이것 때문에 서버 한번 날렸..)
* 여기까지 ```Selenium```을 ```Ubuntu``` 서버 환경에서 사용하는 방법을 알아보았습니다:)
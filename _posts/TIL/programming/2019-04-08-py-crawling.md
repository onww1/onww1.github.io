---
layout: article
title: Selenium으로 크롤링하기
permalink: /programming/py-crawling
key: TIL-programming-py-crawling
tags: TIL 프로그래밍 Python Crawling

header:
  theme: dark
  background: 'linear-gradient(135deg, rgba(0, 189, 128, .7), rgba(0, 128, 255, .8))'

article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 189, 128, .25), rgba(0, 128, 255, .3))'
    src: /assets/images/post/background/background44.jpg

aside:
  toc: true
sidebar:
  nav: programming
license: false
show_author_profile: true
---

## Python에서 Selenium으로 백준 크롤링하기
<!--more-->

안녕하세요. **Mochalatte** 입니다.

오늘은 **Python**에 있는 `Selenium`과 `BeautifulSoup4`를 이용하여 크롤링하는 것을 공부하고 이용해 보았습니다.

우선 크롤링을 공부하게 된 이유는 PS 폴더 정리를 하는 상황에서 파일 이름에 문제 이름을 넣어 새롭게 지정해주고, `git`에 `commit`을 할 때도 커밋 메시지에 문제 이름이 들어간 메시지로 하고 싶었습니다. 그런데 파일로 가지고 있는 문제의 수가 천 문제를 넘어가는 상황에서 모든 문제들에 대한 정보를 입력하기가 어렵다고 판단하였습니다. 그래서 최대한 손으로 타이핑하는 것을 줄이고자 **크롤링**을 하기로 마음 먹었습니다.


---

### 크롤링 준비하기

크롤링을 하기 위해서 필요한 것들은 다음과 같습니다.

```text
준비물 : bs4, selenium, chromedriver
```

- 만약 `bs4`나 `selenium`이 설치되어 있지 않으시다면, **터미널**(또는 **cmd**)에서 다음과 같은 커맨드를 실행하여 설치해주시면 됩니다.
  ```bash
  pip install bs4
  pip install selenium

  # Linux, Mac 등에서 Python3를 사용하는 경우
  pip3 install bs4
  pip3 install selenium
  ```
- 만약 `chromedriver`가 설치되어 있지 않으시다면, [다운로드](http://chromedriver.chromium.org/downloads){:target="_blank"}로 이동하셔서 크롬 버전에 맞게 설치해주시면 됩니다.

---

### 준비물 들여다보기

크롤링을 시작하기에 앞서 준비물들에 대해서 간단한 이해를 하고 넘어가겠습니다.

#### 1. bs4
`bs4`는 `BeautifulSoup4`이고, 이것은 HTML과 XML 파일들의 데이터를 뽑아내기 위한 **파이썬 라이브러리**입니다. 특히 지금은 HTML을 받아올건데 이러한 HTML을 그냥 받으면 단순한 `str` 객체이지만 `bs4`를 이용하면 파이썬이 이해할 수 있는 객체 구조로 변환됩니다.

#### 2. selenium
`selenium`은 `web automation framework`입니다. 즉, 웹 브라우저를 **자동 제어**할 수 있게 해주는 프레임워크입니다. 자세히는 `selenium`의 `webdriver` **API**를 통해 브라우저를 제어하게 됩니다. `selenium`을 이용하면 직접 브라우저를 동작시키기 때문에 **동적 페이지**에 대한 크롤링이 가능하게 됩니다.

#### 3. chromedriver
`chromedriver`는 말 그대로 `chrome`을 조작하도록 도와주는 `driver`입니다. 즉, `chrome`을 통해서 크롤링을 하려면 기본적으로 `chrome`이 설치되어 있어야 하고, `selenium`이 `chrome`을 조작하기 위해서 추가적으로 `chromedriver`가 필요한 것입니다.

---

### 크롤링을 하기 전에

크롤링을 시작하기에 앞서 `selenium`을 사용할 때 먼저 다음과 같은 작업들을 해주어야 합니다.

#### 1. `webdriver`와 `BeautifulSoup`를 import하기
&nbsp;&nbsp;이것은 코드 상단에 다음과 같은 코드를 적어주면 됩니다.
```python
from selenium import webdriver
from bs4 import BeautifulSoup as bs
```

#### 2. `webdriver` option 설정
&nbsp;&nbsp;webdriver를 사용할 때 옵션을 설정할 수 있는데 Headless를 사용할 것인지 아닌지, window-size는 얼마로 할 것인지, gpu를 끌 것인지 아닌지 등과 같은 옵션을 설정할 수 있습니다. 이것은 다음과 같은 코드를 적어서 options에 저장해둡니다.
```python
# Chrome driver option
options = webdriver.ChromeOptions()
options.add_argument('headless')
options.add_argument('window-size=1920x1080')
options.add_argument('disable-gpu')
```
여기서 Headless란 창이 뜨지 않는다는 것을 의미합니다. 만약 Headless 옵션을 주지 않으면 크롤러를 실행했을 때, 크롬창이 떠서 혼자 지정된 움직임대로 움직이게 됩니다.
> 실제로 보면 꽤.. 신기합니다. 궁금하신 분들은 headless 옵션을 지우고 실행해보세요!

#### 3. `webdriver` 경로 지정
&nbsp;&nbsp;이제 webdriver에 chromedriver의 경로와 위에서 지정한 options를 함께 넘겨주면 chrome이 작동됩니다.
```python
# chrome_path에 chromedriver의 경로를 넣어주시면 됩니다.
driver = webdriver.Chrome('chrome_path', chrome_options=options)
```

---

### 백준 문제 리스트 크롤링하기

우선 `selenium`으로 백준 [전체 문제](https://www.acmicpc.net/problemset) 탭에서 **문제 번호**와 **제목**을 크롤링해보도록 하겠습니다. 앞에서 설정을 하고 켠 `webdriver`를 통해서 해당 페이지에 접근하고, 그 페이지의 `html`을 얻습니다. 그리고 그 `html`을  `BeautifulSoup`에게 넘겨주어 파싱합니다.

```python
driver.get('https://www.acmicpc.net/problemset')
html = driver.page_source
soup = bs(html, 'html.parser')
```

&nbsp;
이제 html의 구조를 파악해서 얻고자 하는 데이터가 어디에 있는지를 알아야 합니다. 이것을 확인하기 위해서 해당 페이지에서 `chrome 개발자 도구`를 켜서 소스를 봅니다. 

![chrome-developer-tools](/assets/images/post/TIL/programming/chrome-developer-tools.png)

&nbsp;
여기서 `개발자 도구`의 **좌측 상단**에 있는 **마우스 포인터**와 같은 버튼을 누르면 원하는 Element를 마우스로 선택할 수 있습니다.

![chrome-developer-tools-pointer](/assets/images/post/TIL/programming/chrome-developer-tools-pointer.png)

&nbsp;
이제 마우스로 원하는 부분에 갖다대면 그 Element에 해당하는 HTML 태그 부분을 가르킵니다.

![chrome-developer-tools-element-highlight.png](/assets/images/post/TIL/programming/chrome-developer-tools-element-highlight.png)

&nbsp;
반대 경우도 가능합니다. HTML 태그를 가르키면 그 태그에 해당하는 영역을 **Highlighting** 해줍니다.

![chrome-developer-tools-inverse.png](/assets/images/post/TIL/programming/chrome-developer-tools-inverse.png)

&nbsp;
이렇게 필요한 데이터의 위치를 알았으면 그 위치에 대한 정보를 알아야 합니다. 여러가지 정보가 있지만 여기서는 `CSS Selector`를 통하여 접근을 하도록 하겠습니다. `CSS Selector`도 `개발자 도구`를 통해서 쉽게 얻을 수 있습니다. `CSS Selector`를 얻고 싶은 태그를 **마우스 우클릭**하고 다음과 같이 접근하여 복사를 하면 됩니다.

![chrome-developer-tools-copy-selector.png](/assets/images/post/TIL/programming/chrome-developer-tools-copy-selector.png)

&nbsp;
이렇게 크롤링하고 싶은 데이터의 `CSS Selector`를 복사해서 `BeautifulSoup`에게 `select` 함수의 인자로 넘겨주면 파싱된 데이터 중에서 해당 경로에 맞는 데이터들을 뽑아서 리스트로 리턴해줍니다.

&nbsp;
저희는 백준 전체 문제 탭에서 **문제 번호**와 **제목**을 가져올 것이므로 해당 부분의 `CSS Selector`를 가져옵니다. 그리고 그것을 `BeautifulSoup`에게 넣어주면 다음과 같습니다.
```python
problem_numbers = soup.select('#problemset > tbody > tr > td:nth-of-type(1)')
problem_titles  = soup.select('#problemset > tbody > tr > td:nth-of-type(2)')
```

반환된 것들은 해당 경로에 매칭되는 태그들의 **리스트**입니다.  따라서 반복문을 통해 순차적으로 출력하면 됩니다. 전체 코드는 다음과 같습니다.

```python
#-*- coding:utf-8 -*-

from selenium import webdriver
from bs4 import BeautifulSoup as bs

# Chrome driver option
options = webdriver.ChromeOptions()
options.add_argument('headless')
options.add_argument('window-size=1920x1080')
options.add_argument('disable-gpu')

# chrome_path에 chromedriver의 경로를 넣어주시면 됩니다.
driver = webdriver.Chrome('chrome_path', chrome_options=options)

# 전체 문제 페이지를 불러옵니다.
driver.get('https://www.acmicpc.net/problemset')
html = driver.page_source
soup = bs(html, 'html.parser')

# 문제 번호와 제목에 해당되는 태그의 정보를 주고 데이터를 받아옵니다.
problem_number = soup.select('#problemset > tbody > tr > td:nth-of-type(1)')
problem_title = soup.select('#problemset > tbody > tr > td:nth-of-type(2)')

# 순차적으로 출력합니다.
for index in range(len(problem_number)):
	print(problem_number[index].text, problem_title[index].text)
```

#### Result
![crawling_result](/assets/images/post/TIL/programming/crawling_result.png)

---

### 로그인해서 알고리즘 분류 가져오기

저는 실제로 크롤링을 할 때, 알고리즘 분류가 필요했었는데 확인해보니 알고리즘 분류는 로그인을 해야만 보이는 정보였습니다. 그래서 로그인을 할 필요가 있었습니다. `selenium`은 실제로 브라우저를 조작하는 것이므로 실제로 저희가 로그인을 하는 것처럼 로그인 페이지에서 아이디와 비밀번호를 입력하고 로그인 버튼을 눌러서 로그인을 하면 됩니다.

그럼 이제 로그인하는 방법에 대해서 알아보도록 하겠습니다. 

우선 앞에서 했던 것처럼 `webdriver`를 사용하기 전에 해주어야 할 것들을 해주고 나서, `driver`를 통해서 **로그인 페이지**를 가져옵니다. 그런 다음 **로그인 폼**에 아이디와 비밀번호를 입력해주어야 하는데 이건 `driver`를 통해서 가능합니다. ID와 PW을 입력하는 `input` 태그를 보면 `name`이라는 attribute가 있는데 그것을 이용하여 해당 태그에 접근하고, `send_keys` 함수를 통해서 값을 전달하면 됩니다.

![input_tags](/assets/images/post/TIL/programming/input_tags.png)

&nbsp;

그리고 `CSS Selector`를 얻었던 것과 비슷하게 로그인 버튼의 `XPath`를 얻어서 그 경로를 이용하여 로그인 버튼에 접근하고 `click` 함수를 호출하면 로그인 버튼을 누르게 됩니다. 로그인을 하는 코드는 다음과 같습니다.
```python
# login page를 켜고, 주어진 ID와 PW를 input 칸에 넣어준 후 로그인 버튼을 클릭한다.
driver.get('https://www.acmicpc.net/login?next=%2F')
driver.find_element_by_name('login_user_id').send_keys(user_id)
driver.find_element_by_name('login_password').send_keys(user_pw)
driver.find_element_by_xpath('/html/body/div[3]/div[3]/div/div/form/div[4]/div[2]/button').click()
```

&nbsp;
알고리즘 분류는 각 문제 페이지 하단에 존재하는데 앞에서 했던 것과 비슷하게 그 데이터에 접근하여 값을 뽑아내면 됩니다. 저는 이 과정을 제가 가지고 있는 `bojxxxx.yyy`의 형태로 존재하는 파일들의 이름을 넘겨주어 파일에 쓰여있는 번호에 해당하는 문제에 접근하여 **제목**과 **알고리즘 분류**를 뽑아오도록 코드를 작성하였습니다. 해당 코드는 **Github**에 올려 두었습니다.

[Source](https://github.com/onww1/TIL/blob/master/Python/Crawling/crawler.py){:.button.button--outline-success.button--pill}{:target="_blank"}

---

지금까지 **Selenium**과 **BeautifulSoup4**를 이용하여 크롤링을 하는 것에 대해서 다루었고, 실제로 제가 어떻게 사용했는지도 다루었습니다.

혹시라도 부족한 부분이 보이시면 언제든지 **피드백**을 해주세요!

감사합니다. 🙇🏻‍

---


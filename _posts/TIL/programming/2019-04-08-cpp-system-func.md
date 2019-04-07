---
layout: article
title: C++ system 함수 사용하기
permalink: /programming/cpp-system-func
key: TIL-programming-cpp-system-function
tags: TIL 프로그래밍 C++

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

## C++ system 함수로 터미널 커맨드 실행하기
<!--more-->

안녕하세요. **Mochalatte** 입니다.

오늘은 **C++ `system` 함수**를 이용하여 **터미널**에서 **커맨드**를 실행시키는 것을 다루려고 합니다.

---

평소 어떤 작업을 할 때, **반복적으로** 일부분만 다른 커맨드를 작성하여 실행시켜야 할 일이 생기곤 합니다. 이러한 상황에서 일일이 커맨드를 작성하여 실행시키는 것은 육체적 노동이 따르게 됩니다. 

저도 이번에 폴더를 정리하면서 비슷한 상황에 놓였고, 그러한 상황을 해결해보고자 검색을 하다가 `system` 함수를 알게 되었습니다.

---

우선 `system` 함수는 `stdlib.h` 라이브러리를 include하면 사용을 할 수 있고, **prototype**은 다음과 같습니다.

```c++
int system(const char* command);
```
prototype에서 알 수 있듯이, `system` 함수는 실행시키고자 하는 커맨드를 담고 있는 **const** 문자열을 파라미터로 받게 됩니다. 그리고 이 함수를 통해 반환되는 값은 시스템 환경과 라이브러리 구현에 따라 다르지만 통상적으로 이 함수를 호출할 때 넘겨줬던 **커맨드를 실행했을 때의 반환값**과 같습니다.

#### Example code
```c++
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    int ret = system("ls -a");
    return 0;
}
```

#### Result
![cpp-system-function-example](/assets/images/post/TIL/programming/cpp-system-func-example.png)

---

저는 이 함수를 이용하여 파일들의 위치를 바꾸고, `git`으로 `commit`까지 하도록 만들었습니다. 폴더를 정리하는 과정에서 비슷한 이름의 파일들이 매우 많았고, 그것들을 다른 폴더에 옮기면서 이름도 바꾸고, [Github](https://github.com/onww1)에 올리려고 했기 때문에 이와 같은 작업을 진행했습니다. 

---

제가 작성했던 첫 번째 프로그램은 `bojxxxx.yyy`와 같이 `xxxx`라는 수와 `yyy`라는 확장자를 가진 파일에 대하여 `번호`와 `확장자`, `알고리즘 분류`, `문제 제목`을 주면 그 파일을 해당 알고리즘 분류 폴더로 이동시키고, 파일 이름을 `boj번호_문제_제목.확장자`와 같이 바꾸어서 `git`으로 `commit`을 시키는 것이었습니다. 

[Source](https://github.com/onww1/TIL/blob/master/C%2B%2B/system_function/mv.cpp){:.button.button--outline-success.button--pill}{:target="_blank"}

---

제가 작성했던 두 번째 프로그램은 한 문제를 여러 번 풀었을 때, `bojxxxx_z.yyy`와 같이 `xxxx`라는 문제 번호 뒤에 `_z`를 붙여서 표현을 했었는데 이 중에서 가장 마지막 것을 `bojxxxx.yyy`로 바꾸고 나머지 파일들은 모두 지우도록 하는 프로그램이었습니다. 

[Source](https://github.com/onww1/TIL/blob/master/C%2B%2B/system_function/rm.cpp){:.button.button--outline-success.button--pill}{:target="_blank"}

---

지금까지 **C++ system** 함수에 대해서 다루고, 제가 이 함수를 이용해서 어떤 작업을 했는지에 대해서 알아보았습니다.

혹시라도 부족한 부분이 보이시면 언제든지 **피드백**을 해주세요!

감사합니다. 🙇🏻‍

---


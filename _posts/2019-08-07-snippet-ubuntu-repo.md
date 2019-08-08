---
layout: post
title:  "우분투 Repository"
date:   2019-08-07 09:00:00
categories: Snippet
permalink: /archivers/snippet_ubuntu_repo
nocomments: true
use_math: true 
---

# Ubuntu Repository 

### 구름 IDE 세팅값

우분투 16.04 LTS 컨테이너를 생성하면 왠지 모르게 업그레이드가 안되는 애들이 있다. 몇 번 삽질한 결과 아래를 추가하자.

<!--more-->


```bash
vi /etc/apt/sources.list
```

위의 파일을 열고 다음을 추가한다.

```bash
deb http://archive.ubuntu.com/ubuntu bionic main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu bionic-security main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu bionic-updates main restricted universe multiverse
```

그리고 갱신한다.

```bash
sudo apt-get update
sudo apt-get upgrade
```


### Firefox Headless 세팅

```bash
sudo apt-add-repository ppa:mozillateam/firefox-next
```

다음을 설치한다.

```bash
sudo apt-get install firefox xvfb
```

가상 디스플레이 설정해준다.


```bash
Xvfb :10 -ac &
export DISPLAY=:10
```

파이어폭스 실행 확인한다.
```bash
firefox
```



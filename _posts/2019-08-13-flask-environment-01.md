---
layout: post
title:  "Flask 미니 프로젝트 01"
date:   2019-08-07 09:00:00
categories: Python
permalink: /archivers/python_flask_01
nocomments: false
use_math: true 
---

# Flask 미니 프로젝트

요즘 파이썬이 대세입니다. 인터프리터 언어를 극도로 싫어하지만 어느 정도는 알아야겠다 싶어 언어에 익숙해질 겸 그냥 배우는건 재미없으니 Flask로 개인 프로젝트를 하나 진행해볼까 합니다. 

### virtualenv 설치

일단 Flask 개발환경 구축부터 합시다. 플라스크를 철시하는 가장 편히란 방법은 가상 환경을 사용하는 것입니다. 가상 환경은 개별적으로 설치하는 패키지에 대한 파이썬 인터프리터의 프라이빗 복사본 환경을 의미합니다. 이런 환경은 글로벌 파이썬 인터프리터에 영향을 미치지 않습니다. 

<!--more-->

일단 설치해 봅시다. 프로젝트 환경은 구름IDE Ubuntu 16.04.LTS를 활용하겠습니다. 

```bash
$ sudo apt-get install python-virtualenv
```

이제 virtualenv 커맨드로 가상 환경을 생성해 봅시다. 이 커맨드는 가상 환경의 이름을 인수로 받습니다. 선택한 이름의 폴더가 현재 디렉토리에 생성되고 그 안에는 가상 환경과 관계된 모든 파일이 존재하게 됩니다. 

가상 환경에서 공통적으로 사용하는 명명 규칙은 **venv**입니다.

저는 아래와 같이 폴더를 만들고 환경을 만들겠습니다.

```bash
$ mkdir wras_sys_4
$ virtualenv venv
Running virtualenv with interpreter /usr/bin/python2
New python executable in /workspace/gp_blank/wras_sys_4/venv/bin/python2
Also creating executable in /workspace/gp_blank/wras_sys_4/venv/bin/python
Installing setuptools, pkg_resources, pip, wheel...done.
```

이제 private 파이썬 인터프리터를 포함하는 새로운 가상 환경에서 venv 폴더가 존재하게 됩니다.

```bash
$ ls -al ./venv
합계 32
drwxrwxr-x 7 root root 4096  8월 12 15:37 .
drwxrwxr-x 3 root root 4096  8월 12 15:37 ..
drwxrwxr-x 2 root root 4096  8월 12 15:37 bin
drwxrwxr-x 2 root root 4096  8월 12 15:37 include
drwxrwxr-x 3 root root 4096  8월 12 15:37 lib
drwxrwxr-x 2 root root 4096  8월 12 15:37 local
-rw-rw-r-- 1 root root   61  8월 12 15:37 pip-selfcheck.json
drwxrwxr-x 3 root root 4096  8월 12 15:37 share
```

이제 가상 환경을 활성화합니다. 

```bash
$ source venv/bin/activate
(venv) root@goorm:/workspace/gp_blank/wras_sys_4#
```

윈도우라면 명령은 아래와 같습니다. 

```cmd
venv\Scripts\activate
```

가상 환경이 활성화되면 파이썬 인터프리터의 위치가 PATH에 추가되지만 이 변경사항은 영구적이지는 않고 현재 커맨드 세션에만 영향을 미칩니다. 가상 환경이 활성화되면 이를 표시하기 위해 아래와 같이 커맨드라인이 바뀝니다. 


```bash
(venv) root@goorm:/workspace/gp_blank/wras_sys_4#
```

작업이 완료되었다면 deactivate를 입력하면 됩니다.


### Flask 설치



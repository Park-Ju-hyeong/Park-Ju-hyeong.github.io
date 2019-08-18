---
layout: post
title: "Using docer for ubuntu from windows"
category: windows
tags: windows ubuntu docker
excerpt: 윈도우즈 안에있는 우분투에서 도커를 사용하는 방법.
mathjax: true
author: J. H. Park
sitemap :
    changefreq : daily
    priority : 0.5
---

* content
{:toc}

# 윈도우즈 안에있는 우분투에서 도커를 사용하는 방법  
  
2년 전만 해도 windows 에서 docker 를 사용하려면, `docker for windows` 라는 프로그램을 통해서 진행했었다.  
현재 많이 사용하는 방법은 WSL 이라는 건데 윈도우에서 ubuntu를 사용할 수 있게 해주는 기능이 있다. (MS 에서 ubuntu 개발진과 공동으로 개발한 기능이라고 한다.)  





# WSL (Windows Subsystem for Linux)

## Requirement



# 설치 방법

## 1. MS sotre 에서 ubuntu 설치

MS store 에서 ubuntu 를 검색한다. 각자 자신이 사용할 버전으로 설치하면 된다.  
필자는 사용하는 프로그램들이 아직 18.04에 최적화가 안돼있는 것 같아 16.04 를 설치하였다.  

![](C:/Users/jpark/Documents/GitHub/Park-Ju-hyeong.github.io/_posts/2019_08_images/ms_sotre_ubuntu.PNG)


## 2. Windows 기능 켜기  

MS store에서 ubuntu 설치 후 바로 실행했을 때 문제없이 linux 계정설정으로 넘어가면 이번 단계를 넘어가면 된다.  
하지만 __`0x8007019e`__ 같은 에러가 난다면, cmd 나 powershell 을 키고 아래와 같이 명령어를 날린다.  


```cmd
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

`C:\Users\<Username>\AppData\Local\Microsoft\WindowsApps`

# Reference

1. https://docs.microsoft.com/ko-kr/windows/wsl/install-win10
1. https://medium.com/rkttu/wsl%EC%97%90%EC%84%9C-native-docker-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0-ff75b1627a87  
1. https://www.clien.net/service/board/cm_nas/12671058  
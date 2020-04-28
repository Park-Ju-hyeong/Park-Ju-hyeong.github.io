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

# WSL (Windows Subsystem for Linux)

구글에서 여러 글들을 보면서 문제를 해결해 나간 것을 한번에 정리하고자 한다.  


# Requirement

* `windows 10 >= RS4` 

> windows 빌드 버전중에서 Redstone 4 (RS4) 이상이 필요합니다.  
하위 버전에서도 ubuntu 가 작동할 진 몰라도 docker 를 실행하기 위해선 rs4 이상 빌드가 필요한 것 같습니다. 
```
< win + r >  키 눌러서 실행창 띄우고 < winver > 입력 
```

![](https://github.com/Park-Ju-hyeong/Park-Ju-hyeong.github.io/blob/master/_posts/2019_08_images/windows_version.PNG?raw=true)

# 윈도우즈 안에있는 우분투에서 도커를 사용하는 방법  
  
2년 전만 해도 windows 에서 docker 를 사용하려면, `docker for windows` 라는 프로그램을 통해서 진행했었다.  
현재 많이 사용하는 방법은 WSL 이라는 건데 윈도우에서 ubuntu를 사용할 수 있게 해주는 기능이 있다. (MS 에서 ubuntu 개발진과 공동으로 개발한 기능이라고 한다.)  


# 설치 방법

## 1. MS sotre 에서 ubuntu 설치

MS store 에서 ubuntu 를 검색한다. 각자 자신이 사용할 버전으로 설치하면 된다.  
필자는 사용하는 프로그램들이 아직 18.04에 최적화가 안돼있는 것 같아 16.04 를 설치하였다.  

![](https://github.com/Park-Ju-hyeong/Park-Ju-hyeong.github.io/blob/master/_posts/2019_08_images/ms_sotre_ubuntu.PNG?raw=true)


## 2. Windows 기능 켜기  

MS store에서 ubuntu 설치 후 바로 실행했을 때 문제없이 linux 계정설정으로 넘어가면 이번 단계를 넘어가면 된다.  
하지만 __`0x8007019e`__ 같은 에러가 난다면, cmd 나 powershell 을 키고 아래와 같이 명령어를 날린다.  


```cmd

Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux

```

## 3. ubuntu 에서 docker 설치 

이제 terminal에서 docker 를 설치하면 된다.  
하지만 docker 사이트에서 진행하는 방식으로 설치하면 version 이 2019년 8월 기준 19.xx 로 설치된다.  필자가 무수히 많은 설치와 삭제를 진행해봤지만 19.xx 버전으로는 docker 가 작동하지 않는 것 같고 17.xx 버전으로 설치해야 작동하는 것 같다.  

```
sudo su -

apt update

wget https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/docker-ce_17.03.3~ce-0~ubuntu-xenial_amd64.deb

dpkg -i docker-ce_17.03.3~ce-0~ubuntu-xenial_amd64.deb

apt -f -y install

sudo apt-mark hold docker-ce

sudo service docker start
```

## 4. 추가 작업

3. 까지 진행하면 정상적으로 작동해야 할 것 같지만, docker 명령어는 인식하면서 docker ps 나 run 등 하위 명령어는 작동을 안한다..  

googleing등을 통해 해결을 했는데 아직 이해는 못하고 있다.  

ubuntu terminal 을 종료하고 cmd 나 powershell 을 통해서 아래 경로로 접근한다.  
```
C:\Users\<Username>\AppData\Local\Microsoft\WindowsApps
```
해당 경로 아래에 자신이 설치한 버전에 따라 `ubuntu.exe` 또는  `ubuntu1804.exe` 또는 `ubuntu1604.exe` 가 있을 것이다.  
필자는 16.04 버전을 설치했기에 `ubuntu1604.exe` 가 있다.  
이후 아래 명령어를 입력한다. 

```
ubuntu1604.exe -c "sudo service docker start && sudo docker ps > /dev/null && echo From now on, you can minimize this window and use the docker in other Ubuntu WSL sessions. If you are using a version of Windows 10 RS3 or earlier, do not close this window. && tail -f /dev/null"
```
실행 다 하면 종료하고 ubuntu terminal 실행해서 docker ps 입력하면 정상적으로 실행이 될 것이다.

# Reference

1. https://docs.microsoft.com/ko-kr/windows/wsl/install-win10
1. https://medium.com/rkttu/wsl%EC%97%90%EC%84%9C-native-docker-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0-ff75b1627a87  
1. https://www.clien.net/service/board/cm_nas/12671058  
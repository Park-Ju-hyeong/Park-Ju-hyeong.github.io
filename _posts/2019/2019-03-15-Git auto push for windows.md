---
layout: post
title: "Git auto push for windows"
category: git
tags: git github auto
excerpt: 윈도우에서 자동으로 github에 push 를 해주는 배치 파일입니다.
mathjax: true
author: J. H. Park
sitemap :
    changefreq : daily
    priority : 0.5
---

* content
{:toc}

# 자동으로 깃헙 푸시하기

윈도우에서 일정 시간마다 github 에 푸시해주는 파일입니다.  

우선적으로 clone 을 한 상태이어야 합니다.  
클론한 폴더를 보시면 .git 파일이 존재해야 작동합니다.  
여러 레파지토리를 실행하고 싶으면  `cd path ~ git push` 부분을 복붙해서  
path 부분만 크론한 레파지토리 디렉토리로 바꿔주면 됩니다.  

# CMD 백그라운드에서 실행하기

배치파일을 실행하면 검은창이 떠서 귀찮은데 아래 `.vbs` 코드의 path 부분을 본인의 bat 파일의 경로로 바꿔서  vbs 파일을 실행하면 검은 CMD 창 안뜬 상태로 백그라운드에서 실행됩니다.  


# full code
[GitAutoPush.bat](https://gist.github.com/Park-Ju-hyeong/a4b554145037576330ccf81ea479ba52)  
[BackgroundCMD.vbs](https://gist.github.com/Park-Ju-hyeong/021da09e2dd5729285e3b9d52330f753#file-backgroundcmd-vbs)


# GitAutoPush.bat

```
:loop
	:: Navigate to the directory you wish to push to GitHub
	::Change <path> as needed. Eg. C:\Users\pookie\Desktop\Writings
	cd C:\Users\jhpark\Documents\GitHub\Docker

	::Initialize GitHub
	git init
	
	::Pull any external changes (maybe you deleted a file from your repo?)
	git pull
	
	::Add all files in the directory
	git add --all
	
	::Commit all changes with the message "auto push". 
	::Change as needed.
	git commit -m "auto push"
	
	::Push all changes to GitHub 
	git push
	
	::Alert user to script completion and relaunch.
	echo Complete. Relaunching...
	
	::Wait 3600 seconds until going to the start of the loop.
	::Change as needed.
	TIMEOUT 3600
	
::Restart from the top.	
goto loop
```


# BackgroundCMD.vbs

```
Set WinScriptHost = CreateObject( "WScript.shell" )

WinScriptHost.Run Chr(34) & "C:\Users\jhpark\Documents\GitHub\GitAutoPush.bat" & Chr(34), 0

Set WinScriptHost = Nothing
```

# Reference

1. https://github.com/Rich2020/GitHub-auto-push-for-Windows
2. https://souk0721.github.io/studynote/2018/07/04/basic-vbs-01.html
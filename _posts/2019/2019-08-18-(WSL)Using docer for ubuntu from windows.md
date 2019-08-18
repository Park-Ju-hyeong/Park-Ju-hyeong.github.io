---
layout: post
title: "Get tasklist from python and windows"
category: python
tags: os python windows
excerpt: 파이썬에서 tasklist 를 불러옵니다.
mathjax: true
author: J. H. Park
sitemap :
    changefreq : daily
    priority : 0.5
---

* content
{:toc}

# 파이썬에서 프로세스 리스트 가져오기

cmd 에서 `tasklist` 명령어를 치면 현재 프로레스 리스트를 확인할 수 있습니다.  

`os.system` 으로 실행하면 텍스트 형태로 가져올 수 없습니다. 

`subprocess.call` 도 마찬가지로 특정 명령문을 실행시킬 때만 사용할 수 있습니다.

`subprocess.check_output` 함수를 이용해서 리턴 결과를 가져올 수 있습니다.


# TASKLSIT

```python  
import subprocess
import re    

def get_processes_running():
    tasks = subprocess.check_output(['tasklist']).decode('cp949', 'ignore').split("\r\n")
    p = []
    for task in tasks:
        m = re.match("(.+?) +(\d+) (.+?) +(\d+) +(\d+.* K).*",task)
        if m is not None:
            p.append({"image":m.group(1),
                        "pid":m.group(2),
                        "session_name":m.group(3),
                        "session_num":m.group(4),
                        "mem_usage":m.group(5)
                        })
    return p
```
```
[
	{'image': 'System Idle Process',
	'pid': '0',
  	'session_name': 'Services',
 	 'session_num': '0',
  	'mem_usage': '8 K'},
  	.
  	.
  	.
]
```

# Reference

1. https://stackoverflow.com/questions/13525882/tasklist-output
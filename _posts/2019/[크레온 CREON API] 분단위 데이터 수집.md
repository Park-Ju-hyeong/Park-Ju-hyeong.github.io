---
layout: post
title: "[크레온 CREON API] 분단위 데이터 수집"
category: 주식
tags: 크레온 creon api stock python
excerpt: 대신증권 크레온 api 를 통해서 분단위 데이터를 수집합니다.
mathjax: true
author: J. H. Park
sitemap :
    changefreq : daily
    priority : 0.5
---

* content
{:toc}

# import


```python
import time
import win32com.client
import pandas as pd
```

일단위 데이터와 분단위 데이터를 선택해서 가져올 수 있는 함수입니다.

분단위데이터는 2년치정도 제공하는 것으로 보입니다.

분단위 데이터를 모두 가져오는 데 25 초정도 걸리고

일단위 데이터는 1초 안으로 가져옵니다.

한 요청당 최대 2856개를 가져올 수 있고

15초당 60건을 요청할 수 있습니다.

`2856 * 60 = 171360`

# object


```python
class CREON(object):
    """대신증권 크레온 API"""
    
    def __init__(self):
        # 연결 여부 체크
        self.objCpCybos = win32com.client.Dispatch("CpUtil.CpCybos")
        bConnect = self.objCpCybos.IsConnect
        if (bConnect == 0):
            print("PLUS가 정상적으로 연결되지 않음. ")
            exit()
     
    def setMethod(self, code, char, from_yyyymmdd=None, to_yyyymmdd=None, count=None):
        """
        count는 보통 상식의 데이터 개수가 아니다.
        여기서는 한번 요청 시 가져와지는 데이터의 개수이다.
        한번 요청 시 최대 2856개 가능하다.
        
        원하는 데이터 개수가 있으면 to_yyyymmdd 로 가져온 다음에 잘라서 사용한다.
        하루에 분단위 데이터가 381개이다. (* 마지막 10분은 동시호가)
        
        """
        # object 구하기
        self.objStockChart = win32com.client.Dispatch("CpSysDib.StockChart")
        self.objStockChart.SetInputValue(0, code)  # 종목코드
        
        if to_yyyymmdd:
            self.objStockChart.SetInputValue(1, ord('1'))  # 요청 구분 '1': 기간, '2': 개수
            self.objStockChart.SetInputValue(2, from_yyyymmdd)  # To 날짜
            self.objStockChart.SetInputValue(3, to_yyyymmdd)  # From 날짜
        elif count:
            self.objStockChart.SetInputValue(1, ord('2'))  # 개수로 받기
            self.objStockChart.SetInputValue(4, count)  # 조회 개수
        else: raise print("기간을 입력해주세요.")
            
        self.objStockChart.SetInputValue(5, [0, 1, 2, 3, 4, 5, 8])  # 요청항목 - 날짜, 시간,시가,고가,저가,종가,거래량
        self.objStockChart.SetInputValue(6, ord(char))  # '차트 주기 - 분/틱
        self.objStockChart.SetInputValue(7, 1)  # 분틱차트 주기
        
        self.objStockChart.SetInputValue(9, ord('1'))  # 수정주가 사용
        
        
        
        self.data = {
            "date": [],
            "time": [],
            "open": [],
            "high": [],
            "low": [],
            "close": [],
            "vol": [],
        }
        
    def checkRequest(self):
        
        self.objStockChart.BlockRequest()
        
        rqStatus = self.objStockChart.GetDibStatus()
        
        if rqStatus != 0: 
            
            return False
        
#         else:
#             print("통신상태 양호, 누적 개수 {}".format(len(self.data["date"])))
        
        self.count = self.objStockChart.GetHeaderValue(3)
        
        if self.count <= 1: 
            
            return False
        
        return int(self.count)
    
    def checkRemainTime(self):
        
        # 연속 요청 가능 여부 체크
        remainTime = self.objCpCybos.LimitRequestRemainTime / 1000.
        remainCount = self.objCpCybos.GetLimitRemainCount(1)  # 시세 제한
        
        if remainCount <= 0:
            print("15초당 60건으로 제한합니다.")
            time.sleep(remainTime)
            
    
    def getStockPriceMin(self):
        
        while 1:
        
            self.checkRemainTime()
            rows = self.checkRequest()

            if rows:

                for i in range(rows):

                    self.data["date"].append(self.objStockChart.GetDataValue(0, i))
                    self.data["time"].append(self.objStockChart.GetDataValue(1, i))
                    self.data["open"].append(self.objStockChart.GetDataValue(2, i))
                    self.data["high"].append(self.objStockChart.GetDataValue(3, i))
                    self.data["low"].append(self.objStockChart.GetDataValue(4, i))
                    self.data["close"].append(self.objStockChart.GetDataValue(5, i))
                    self.data["vol"].append(self.objStockChart.GetDataValue(6, i))
            else:

                break
                
    
        return self.data
```

# run


```python
creon = CREON()
```


```python
creon.setMethod(code="A005930", char="m", from_yyyymmdd=20200101, to_yyyymmdd=10000101)
```


```python
%%time
samsung = creon.getStockPriceMin()
```

    15초당 60건으로 제한합니다.
    Wall time: 24.1 s
    


```python
tmp = pd.DataFrame(samsung)
tmp.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>vol</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>189232</th>
      <td>20170213</td>
      <td>905</td>
      <td>37800</td>
      <td>37880</td>
      <td>37800</td>
      <td>37860</td>
      <td>48100</td>
    </tr>
    <tr>
      <th>189233</th>
      <td>20170213</td>
      <td>904</td>
      <td>37880</td>
      <td>37900</td>
      <td>37780</td>
      <td>37780</td>
      <td>80500</td>
    </tr>
    <tr>
      <th>189234</th>
      <td>20170213</td>
      <td>903</td>
      <td>37880</td>
      <td>37900</td>
      <td>37840</td>
      <td>37880</td>
      <td>61400</td>
    </tr>
    <tr>
      <th>189235</th>
      <td>20170213</td>
      <td>902</td>
      <td>37920</td>
      <td>37980</td>
      <td>37820</td>
      <td>37860</td>
      <td>98000</td>
    </tr>
    <tr>
      <th>189236</th>
      <td>20170213</td>
      <td>901</td>
      <td>37740</td>
      <td>37940</td>
      <td>37720</td>
      <td>37940</td>
      <td>453900</td>
    </tr>
  </tbody>
</table>
</div>




```python
creon.setMethod(code="A005930", char="D", from_yyyymmdd=20200101, to_yyyymmdd=10000101)
```


```python
%%time
samsung = creon.getStockPriceMin()
```

    Wall time: 607 ms
    


```python
tmp = pd.DataFrame(samsung)
tmp.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>vol</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10541</th>
      <td>19800109</td>
      <td>0</td>
      <td>38</td>
      <td>41</td>
      <td>37</td>
      <td>41</td>
      <td>1243500</td>
    </tr>
    <tr>
      <th>10542</th>
      <td>19800108</td>
      <td>0</td>
      <td>37</td>
      <td>37</td>
      <td>36</td>
      <td>37</td>
      <td>1344000</td>
    </tr>
    <tr>
      <th>10543</th>
      <td>19800107</td>
      <td>0</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>604000</td>
    </tr>
    <tr>
      <th>10544</th>
      <td>19800105</td>
      <td>0</td>
      <td>32</td>
      <td>34</td>
      <td>32</td>
      <td>34</td>
      <td>393500</td>
    </tr>
    <tr>
      <th>10545</th>
      <td>19800104</td>
      <td>0</td>
      <td>34</td>
      <td>34</td>
      <td>33</td>
      <td>33</td>
      <td>131500</td>
    </tr>
  </tbody>
</table>
</div>



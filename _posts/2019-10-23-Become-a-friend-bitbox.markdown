---
layout: post
title:  "Bit the Box"
series:
date: 2019-10-23 15:30:00 +0900
abstract: ""
tags: [bit]
image:
toc: true
categories: Tech
---

비트더박스! 비트더박스!  
네네~ 압니다~ beat the box~  

## 질문  
scp 속도제한을 700mbps 으로 설정하세요.  
<br>

참고 1.   

```bash
$ man scp
scp - secure copy (remote file copy program)

-l limit
Limits the used bandwidth, specified in Kbit/s.
```

참고 2.  
bps = Bit Per Second (초당 1비트를 전송)  
Mbps = Mega Bit Per Second (초당 1메가 비트를 전송)  



## 2개의 답

동.  
700mbps -> 87mb/s->89,000kb/s 이거니까 89,000*8  
자동계산기로 돌린거에 8bit 를 곱했구요.  
<br>  

양.  
700mbps = 700Mb/s = 700,000kb/s  
1000씩 곱했지요.  
고로 scp -l 700000  
<br>

683,593 과 700,000 아주 큰(?) 차이는 안나지만 궁금하네요.  
정답을 아시는분은 댓글로.. please~

## 단위

byte 를 제외한 단위에서는 1,000 단위로 올림하고,  
byte 는 1,024단위로 올림합니다.  
<br>
bit 에서 byte 로 단위가 바뀔때는 8bit = 1byte 로 바뀌지요.  

<br>
크기순으로 나열해볼께요.  
<br>

비트(b,bit) > 바이트(B, byte) > 킬로바이트(KB) > 메가바이트(MB) > 기가바이트(GB) > 테라바이트(TB) > 페타바이트(PB) > 엑사바이트(EB) > 제타바이트(ZB) > 요타바이트(YB) > 브론토바이트(VB) > 락시아바이트(RB) > 에르키스탄바이트(OB) > 큐타바이트(QB) > 엑스싸인트(XC)  
<br>

장학퀴즈에 나온적이 있었지요.  
누가 저걸 알까? 싶었는데 저걸 순서대로 읖던분이 계셔서 놀랬던기억이 새록새록~  

<br>
내친김에 테이블도 만들어볼께요.

{:.table.table-key-value-60}

| 단위 | 하위 단위 |
|---|---|
| 1 kilobit | 1,000 bit     |
| 1 megabit | 1,000 kilobit |   
| 1 byte    | 8 bit         |
| 1 kbyte   | 1,024 byte    |
| 1 mbyte   | 1,024 kbyte   |


1 Kbps = 1,000 bps = 1 Kb/sec = 1 Kb/s  
1 Mbps =  1,000,000 bps = 1,000 kbps  =  1 Mb/sec = 1 Mb/s

## b vs B !!

네네~ 보시자마자 아시지요?  
저녀석들~~~ 나를 헷갈리게 만드는 녀석들 입지요.  
<br>
대표적으로 KB/s 와 Kb/s , Mbps 와 MBps 정도 예를 들수 있겠네요.  
<br>

Mbps 는 Mega Bit Per Second 이고,  
MBps 는 Mega Byte Per Second 이지요.  
<br>

고로, 1MBps는 8Mbps랍니다.  



## 우리집 인터넷 속도는 기가망이라네~

속으셨어요. (다짜고짜 속았다니!)  
보통 기가망이라고 하면 동영상 1G 파일을 한번에 받을 수 있을꺼란 기대를 하지 않나요?  
이것은 바로 단위의 함정(?)이라고 볼수 있겠네요.  
<br>

기가망으로 알고 있는 당신의 인터넷은 몇 byte 일까요.  
먼저 기가망의 단위를 확인해봐야죠.  
1기가비트(1Gbps) 이나요?  
bps 는 Bits Per Second 약자로 초당 전송하는 1비트를 의미합니다.  
30bps라고 하면 1초에 30비트를 전송하는 속도라는 거지요.  
8bit가 1byte 이므로 8로 나누어 줍니다. (1000/8 = 125 MB)  
<br>
1 Gbps = 1000 Mbps = 125 MB  
<br>
그렇습니다.  
저희가 기가망으로 알고있는 인터넷망은 최대 초당 125MB 전송할 수 있는 망이였던거지요.  
현실은 초당 125MB 이하로 속도가 나오지요.  (시무룩)

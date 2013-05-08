---
layout: post
title: "SSH Reverse tunnel uppsetning"
description: ""
category: OS X
tags: [Proxy, Squidman, OS X, Tunnel, Reverse]
---
{% include JB/setup %}
# SSH reverse tunnel uppsetning

Ég þarf reglulega að vinna við netþjóna sem hafa enga tengingu við internetið og þá eru stundum góð ráð dýr ef uppfæra þarf hugbúnað t.d.

Ég ætla að setja upp dæmi sem sýnir fram á SSH reverse tunnel frá OS X vél sem keyrir Squid Proxy þjón sem að netþjónninn notar til að uppfæra sig yfir internetið.

Byrjið á þvi að sækja Squidman sem er proxy fyrir OS X. Smellið á pakkann og farið í gegnum uppsetningarferlið.

![image](/images/squidman.png)

![image](/images/squidman_preferences.png)
Breytið portinu í 6588 t.d.

Fyrst tengirðu þig inn á vélina sem þú vilt að fái nettengingu í gegnum proxy uppsetningu með SSH.


        #ssh -R 8080:localhost:6588 notandi@netþjónn
        [root@netþjónn ~]# netstat -an | grep 8080
        tcp   0   0 127.0.0.1:8080 0.0.0.0:* LISTEN
        tcp   0   0 ::1:8080            :::* LISTEN

-R er rofi fyrir portið sem proxy þjónninn er að hlusta á, og :6588 er portið sem þú ert að hlusta á í proxy uppsetningunni.

        [root@netþjónn ~]# export http_proxy=http://localhost:8080
		
Setjið proxy breytuna í yum.conf og gerið svo yum update og þá sést að þið hafið núna tengingu við internetið og getið uppfært eins og ekkert sé.

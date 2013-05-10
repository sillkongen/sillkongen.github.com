---
layout: post
title: "HLS straumar, notaðu Linux netþjón sem videótæki"
description: ""
category: 
tags: []
---
{% include JB/setup %}
Ég hef verið að sækja strauma handa krökkunum mínum svo þau geti horft á sama efnið aftur og aftur og til þess að spara mér bandvídd þá er ég að taka þessa strauma upp og hlaða þeim upp á netþjón innanhúss

Ég setti upp OpenVZ sýndarvél heima sem keyrir Ubuntu 12.04 LTS útgáfu.

Uppfærsla á pakkatré og uppsetning á ffmpeg.

        #apt-get update
        #apt-get upgrade
        #apt-get install ffmpeg

Ég er með skriftu sem heitir vcr.sh

        #!/bin/sh
        now=$(date +"%m_%d_%Y_%H_%M_%S")
        ffmpeg -i http://hostname.domain.com/path/playlist.m3u8 -t $1 -vcodec copy $2$now.mkv

 1. Breytan $1 er lengdin á upptökunni. (sekúndur)
 2. Breytan $2 er skráanafnið.

Ég er svo með crontab færslu sem tekur alltaf upp á sama tíma á laugardögum.

        0 8 * * 6 sh /path/vcr.sh 10800 Laugardagar

Hver skrá fær þá nafn sem er Laugardagar+dagsetning.mkv
þegar upptökunni er lokið færi ég hana yfir á miðlægan skráaþjón hjá mér

        rsync --remove-source-files -pavl /recordings/$2$now.mkv* user@hostname:/path/to/destination

---
title: 工作相关
abbrlink: 5e87d0e6
date: 2024-01-10 20:49:15
tags:
---

##### 服务器
<!-- more -->
|地址|账号|密码|
|---|---|---|
|192.168.2.22|user|abc1234@|
|192.168.2.7|mansion|MXzaq1|
|192.168.2.5|mansion|abc!123|
|192.168.2.9|mansion|abc!123|
|192.168.5.6|680319|38YVKQMJ|
|192.168.2.12|mansion|Mxabc!123@|

##### 下载代码
|项目|命令|
|---|---|
|t35|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/t35 -b t35/develop<br>repo sync -j8<br>repo forall -c git checkout t35/develop|
|livenavigation|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b livenavigation/develop && repo sync<br>repo forall -c git branch livenavigation/develop origin/livenavigation/develop<br>repo forall -c git checkout livenavigation/develop|
|t30|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b t30/develop && repo sync<br>repo forall -c git branch t30/develop origin/t30/develop<br>repo forall -c git checkout t30/develop|
|smartmap|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/t35 -b smartmap/develop <br>repo sync -j8 <br> repo forall -c git checkout smartmap/develop|
|lanesdk|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b lanesdk/develop <br> repo forall -c git checkout lanesdk/develop|
|JL|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/geelyparkiing -b parking/develop<br>repo sync -j8<br>repo forall -c git branch parking/develop origin/parking/develop <br>repo forall -c git checkout parking/develop|
|ids3.7|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b icas3/develop --depth=1 && repo sync<br>repo forall -c
---
title: 常用的项目git下载路径
tags: work
abbrlink: '963e4779'
date: 2023-11-02 20:35:06
---

|项目|命令|
|---|---|
|t35|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/t35 -b t35/develop<br>repo sync -j8<br>repo forall -c git checkout t35/develop --depth=1|
|livenavigation|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b livenavigation/develop && repo sync<br>repo forall -c git branch livenavigation/develop origin/livenavigation/develop<br>repo forall -c git checkout livenavigation/develop|
|t30|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b t30/develop && repo sync<br>repo forall -c git branch t30/develop origin/t30/develop<br>repo forall -c git checkout t30/develop|
|smartmap|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/t35 -b smartmap/develop <br>repo sync -j8 <br> repo forall -c git checkout smartmap/develop|
|lanesdk|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b lanesdk/develop <br> repo forall -c git checkout lanesdk/develop|
|JL|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/geelyparkiing -b parking/develop<br>repo sync -j8<br>repo forall -c git branch parking/develop origin/parking/develop <br>repo forall -c git checkout parking/develop|
|ids3.7|repo init -u ssh://cuifh@192.168.3.39:39999/manifest/cns3 -b icas3/develop --depth=1 && repo sync<br>repo forall -c git branch icas3/develop origin/icas3/develop<br>repo forall -c git checkout icas3/develop|
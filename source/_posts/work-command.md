---
title: 常用Linux命令
tags: work
abbrlink: c49a4fe0
date: 2023-11-02 20:42:36
---
#### 7z解压
```bash
7z解压:`7z x CHN_S01001402_252_LACDW_2206_China.zip -o/mnt/navi_data/MP2023/`
```
#### 编译重定向
```bash
    make -j16 2>&1 | tee build.txt
```

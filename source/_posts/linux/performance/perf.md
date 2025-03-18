---
title: perf采样火焰图
abbrlink: c7467172
tags: linux perfermance
excerpt: linux perf 采样
categories:
  - linux
  - performance
date: 2025-03-18 15:51:45
---
### 火焰图
```bash
perf record -e cpu-clock -g -p `pidof xxx`
perf script -i perf.data &> perf.unfold
/mnt/sda1/software/Perf/FlameGraph/stackcollapse-perf.pl perf.unfold &> perf.folded
/mnt/sda1/software/Perf/FlameGraph/flamegraph.pl perf.folded > perf.svg
```
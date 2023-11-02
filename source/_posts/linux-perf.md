---
title: Linux下性能测试
tags: worik linux perf
abbrlink: ee9b2faf
date: 2023-11-02 20:46:38
---
- 安装[perf](https://askubuntu.com/questions/50145/how-to-install-perf-monitoring-tool)
- 下载[FlameGraph](https://github.com/brendangregg/FlameGraph)
- 进行采样  

```bash
perf record -e cpu-clock -g -p `pidof MXNavi` 
```

```bash
perf script -i perf.data &> perf.unfold 
```

```bash
/mnt/software/Perf/FlameGraph/stackcollapse-perf.pl perf.unfold &> perf.folded 
```

```bash
/mnt/software/Perf/FlameGraph/flamegraph.pl perf.folded > perf.svg
```

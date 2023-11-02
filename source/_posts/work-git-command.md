---
title: 常用的git命令
tags: work
abbrlink: 6957f0ab
date: 2023-11-02 20:40:31
---
### 导出变更
```bash
git archive -o c:/Users/yourusername/Desktop/export.zip NewCommitId $(git diff --name-only OldCommitId NewCommitId)
```
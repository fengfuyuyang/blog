---
title: git
date: 2022-08-16 15:18:22
tags:
  - git
categories:
  - git
---

git命令
<!-- more -->

### 更改某次提交

1. 将HEAD移到需要更改的commit上,修改对应commit前`pick`为`edit`,保存并退出
``` bash
git rebase -i commit_id^
```

2. 修改文件

3. 暂存改动的文件
``` bash
git add [改动的文件]
```

4. 追加改动到第一步指定的commit上
``` bash
git commit --amend
```

5. 移动HEAD到最新commit处
``` bash
git rebase --continue
```

6. 解决冲突
7. 再次执行
``` bash
git rabase --continue
```

### 本地分支落后远端分支

本地分支commit与远端分支commit一致，本地分支未提交的commit移动到最后
```bash
git rebase [<upstream> [<branch>]]
```
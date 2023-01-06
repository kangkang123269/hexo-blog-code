---
title: git rebase合并多次提交记录
tags: [实习学习]
categories: [git]
description: 实习git rebase的使用场景
date: 2022-10-28
cover: http://tny.im/Ihnfq
---

# git rebase 合并多次提交记录

**第一步**：根据自己的开发分支创建一个`dev_one`分支

**第二步**：`git reabse -i HEAD~n`分支（目标分支）n 为前几次要合并的次数

**第三步**：可以看到编辑框，自己输出 s 命令，把后面要合并的都编辑为 s，第一次可以为 pick，编辑后，`ctrl + c`，再`:wq`保存

**第四步**：保存之后，我们再编辑下，第一次可以改变提交记录的名字，`:wq`保存退出

**第五步**：根据 master 分支创建一个分支`dev_two`

**第六步**：`dev_two`合并`dev_one`（git merge）

**第七步**：解决合并冲突

**第八步**：`dev_two`推送到远程分支，之后就可以发送合并到远程 master 的请求了

---
title: Git 杂记
date: 2019-07-12 19:25:46
categories: Skills
index_img:
banner_img:
tags: Git
updated: 2019-07-12 19:25:46
---

## Learning through play

> https://learngitbranching.js.org 是一个非常好玩的 Git 学习网站。

- `git describe <ref>`：描述离你最近的锚点（也就是标签 `tag`)。
  - `<ref>` 可以是任何能被 Git 识别成提交记录的引用，如果你没有指定的话，Git 会以你目前所检出的位置（HEAD）。
  - 输出的结果：`<tag>_<numCommits>_g<hash>`。`tag` 表示的是离 `ref` 最近的标签， `numCommits` 是表示这个 `ref` 与 `tag` 相差有多少个提交记录， `hash` 表示的是你所给定的 `ref` 所表示的提交记录哈希值的前几位。当 `ref` 提交记录上有某个标签时，则只输出标签名称
- `git pull` = `fetch` + `merge`; `git pull --rebase` = `fetch` + `rebase`
- 操作符 `^`：不仅可以指定向上返回一代，其后还可以接数字，当父节点有多个导致有多条返回路径可以选择时使用。
- `git revert`：与 `git reset` 一样，可以回到之前的提交，但不同的是 reset 是直接返回，revert 是复制之前的提交到当前提交之后。
- `git checkout -b [本地分支] [远程分支]` 用于远程跟踪分支。
- `git push origin <source>:<destination>` 和 `git fetch origin <source>:<destination>` 用来处理本地和远程仓库分支名称不同的情况，但特别的是，`git push origin :<destination>` 会删除远程仓库中的分支，`git fetch origin :<destination>` 会在本地创建一个新的分支。

## `git rebase` 命令
> 在不同分支和同一分支中都有用处

### 替代 `git pull`

假设当前分支为 `feature`。`git rebase` 可以实现拉取 `master` 的内容，并且移动 `feature` 的提交（可以设置区间）到 `HEAD`。

**用法**[^1]

```bash
# 在 feature 分支 rebase orgin
$ git checkout feature
$ git rebase origin 

# 若 rebase 过程中出现冲突 -> 解决冲突 -> git add .（无需 git commit）
$ git rebase --continue

# 回到 rebase 始前的状态
$ git rebase --abort

# git push 时会报错（不能进行 fast-forwad）解决方法：
$ git push --force-with-lease origin feature 
```

### 编辑、删除、复制、粘贴提交

**用法**[^2]

```bash
# 对单一分支进编辑删除复制等操作
$ git rebase -i [startpoint] [endpoint] # 前开后闭

# 将某一段 commit 粘贴到另一个分支上
$ git rebase [startpoint] [endpoint] --onto [branchName]
# 改变 HEAD 指向
$ git checkout master
$ git reset --hard  0c72e64
```

[^1]: [Git Community Book](http://gitbook.liuhui998.com/4_2.html)

[^2]: [【Git】rebase 用法小结](https://www.jianshu.com/p/4a8f4af4e803)
 
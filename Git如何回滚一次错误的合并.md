# Git如何回滚一次错误的合并

## 问题抛出
随着我们的功能分支和测试分支增多，一些分支合并的动作会越来越频繁，不可避免地会产生错误的分支合并，这个issue就是告诉大家如何回滚一次错误的合并。

### 过程简介

**采用revert命令**，举例：

1. 我们一不小心将开发中的功能分支feat-xxx合并merge到了开发分支develop，于是我们需要撤销合并的动作提交M，这个动作本身会产生新的提交W：
```bash
git revert -m 1 [merge动作产生的提交编号M]
```

2. 后来feat-xxx分支已经开发完成，可以正式合并至develop了，则首先需要**撤销之前的撤销提交**：
```bash
git revert [revert合并动作的提交编号W]
```

3. 之后便可以再次发起功能分支合并 :rocket: 
```bash
git checkout develop
git merge feat-xxx
```

这里如果没有上面的第二步：撤销之前的撤销，直接第三步，则分支合并是并不完全的。

### 原理简介

revert会产生一个新的提交，它会`undo the data, but doesn't undo the history`。

Linus explains the situation:

> Reverting a regular commit just effectively undoes what that commit
did, and is fairly straightforward. But reverting a merge commit also
undoes the _data_ that the commit changed, but it does absolutely
nothing to the effects on _history_ that the merge had.

> So the merge will still exist, and it will still be seen as joining
the two branches together, and future merges will see that merge as
the last shared state - and the revert that reverted the merge brought
in will not affect that at all.

> So a "revert" undoes the data changes, but it's very much _not_ an
"undo" in the sense that it doesn't undo the effects of a commit on
the repository history.

> So if you think of "revert" as "undo", then you're going to always
miss this part of reverts. Yes, it undoes the data, but no, it doesn't
undo history.

### 中文参考

https://juejin.im/post/5b5ab8136fb9a04f834659ba  

## 文末小彩蛋

对于bugfix分支，我们从master迁出后，就不要修改版本号了，因为对应的snapshot版本并不会与别的分支冲突，否则合并回去时又要修改master分支版本号~
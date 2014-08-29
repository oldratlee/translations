功能分支工作流
======================

![](images/git-workflow-feature-branch-1.png)

一旦你玩转了[集中式工作流](workflow-centralized.md)，在开发过程中加上功能分支是用来鼓励开发者之间协作和简化交流一个很简单的做法。

功能分支工作流背后的核心思路是所有的功能开发应该在一个专门的分支，而不是在`master`分支上。
这个隔离让可以方便多个开发者在各自的功能上开发而不会弄乱主干代码。
另外，也保证了`master`分支的代码一定不会是有问题的，极大有利于集成环境。

功能开发隔离也让[`pull requests`](pull-request.md)工作流成功可能，
[`pull requests`](pull-request.md)工作流能为每个分支发起一个讨论，给另外开发者在分支合入正式项目之前表示赞同机会。
另外，如果你在功能开发中有问题卡住了，可以开一个`pull requests`来向同学们征求建议。
这些做法的重点就是，`pull requests`让团队成员之间互相评论工作变成非常方便！

:beer: 工作方式
---------------------

功能分支工作流仍然用中央仓库，并且`master`分支还是代码正式的项目历史。
但不是直接提交本地历史到各自的本地`master`分支，开发者每次在开始新功能前先创建一个新分支。
功能分支应该有个有描述性的名字，比如`animated-menu-items`或`issue-#1061`，这样可以让分支有个清楚且高聚焦的目标。

在`master`分支和功能分支，`Git`是没有技术上的区别，所以开发者可以用和集中式工作流中完全一样的方式编辑、暂存和提交修改到功能分支上。

并且功能分支也可以（且应该）`push`到中央仓库中。这样不修改正式代码不可以和其它开发者分享提交的功能。
在中央仓库上有多个功能分支不会带任何问题，而`master`仅有的一个『特殊』分支。当然，这也是备份大家本地提交一个便利的做法。

### `Pull Requests`

分支除了可以隔离功能的开发，也使得通过[`Pull Requests`](pull-request.md)讨论变更成为可能。
一旦某个开发完成一个功能，不是立即合并到`master`，而是`push`到中央仓库的功能分支上并发起一个`Pull Request`请求合并修改到`master`。
在修改成为主干代码前，这让其它的开发者有机会先去`Review`变更。

`Code Review`是`Pull Requests`的一个重要的收益，但Code Review实践的目的实际上是讨论代码一个通用方式。
你可以把`Pull Requests`作为专门给某个分支的讨论。这意味着可以在更早的开发过程中就可以进行`Code Review`。
比如，一个开发者开发功能时需要帮助，要做的就是发起一个`Pull Request`，相关的人就会自动收到通知，在相关的提交旁边能看到需要帮助解决的问题。

一旦`Pull Request`被接受了，发布功能要做的和集中式工作流就很像了。
首先，确定本地的`master`分支和上游的`master`分支是同步的。然后合并功能分支到本地`master`分支并`push`已经更新的本地`master`分支到中央仓库。

仓库管理的产品解决方案像[`Bitbucket`](http://bitbucket.org/)或[`Stash`](http://www.atlassian.com/stash)，可以良好地支持`Pull Requests`。可以看看`Stash`的[`Pull Requests`文档](https://confluence.atlassian.com/display/STASH/Using+pull+requests+in+Stash)。

:beer: 示例
---------------------

下面的示例演示了如何把`Pull Requests`作为`Code Review`的方式，但注意`Pull Requests`可以用于很多其它的目的。

### 小红开始开发一个新功能

![](images/git-workflow-feature-branch-2.png)

在开始开发功能前，小红需要一个独立的分支。使用下面的命令[新建一个分支](https://www.atlassian.com/git/tutorial/git-branches#!checkout)：

```bash
git checkout -b marys-feature master
```

这个命令检出一个基于`master`名为`marys-feature`的分支，`Git`的`-b`选项表示如果分支还不存在则新建分支。
这个新分支上，小红按老套路编辑、暂存和提交修改，按需要提交以实现功能：

```bash
git status
git add <some-file>
git commit
```

### 小红要去吃个午饭

早上小红为新功能添加一些提交。
去吃午饭前，`push`功能分支到中央仓库是很好的做法，这是一个方便的备份，如果和其它开发协作，也让他们可以看到小红的提交。

```bash
git push -u origin marys-feature
```

这条命令`push` `marys-feature`分支到中央仓库（`origin`），`-u`选项设置本地分支去跟踪远程对应的分支。
设置好跟踪的分支后，小红就可以使用`git push`命令省去指定推送分支的参数。

### 小红完成功能开发

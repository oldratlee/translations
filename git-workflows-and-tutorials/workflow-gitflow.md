`Gitflow`工作流
============================

![Git Workflows: Gitflow Cycle](images/git-workflows-gitflow.png)

这节介绍的[`Gitflow`工作流](http://nvie.com/posts/a-successful-git-branching-model/)借鉴自在[nvie](http://nvie.com/)的*Vincent Driessen*。

`Gitflow`工作流定义了一个用于解决项目发布的严格分支模型。相应地也比[功能分支工作流](workflow-feature-branch.md)复杂几分，提供了管理大型项目的一个健壮的框架。

这个工作流没有比功能分支工作流有新的概念和命令。而是为不同的分支分配一个很明确的角色，并定义如何和什么时候分支要交互。
比起功能分支工作流，使用为做准备、维护和记录发布使用各自的分支。当然你也用上功能分支工作流所有的好处：`Pull Requests`、隔离实验性开发和更高效的协作。

:beer: 工作方式
---------------------

`Gitflow`工作流仍然用中央仓库作为所有开发者的交互中心。和其它的工作流一样，开发者在本地工作并`push`到要中央仓库中。

### 历史分支

相对使用仅有一个`master`分支，本工作流使用2个分支记录项目的历史。`master`分支存储了正式发布的历史，而`develop`分支作为功能的集成分支。
这样也方便`master`分支上的所有提交分配一个版本号。

![](images/git-workflow-release-cycle-1historical.png)

剩下要说明的问题围绕着这2个分支的区别展开。

### 功能分支

每个新功能属于一个自己的分支，这样可以[`push`到中央仓库以备份和协作](https://www.atlassian.com/git/tutorial/remote-repositories#!push)。
但功能分支不是从`master`分支上拉出新分支，而是使用`develop`分支作为父分支。当新功能完成时，合并回`develop`分支。功能应该从不直接到`master`分支交互。

![](images/git-workflow-release-cycle-2feature.png)

注意，从含义和目的上来看，功能分支加上`develop`分支是功能分支工作流的用法。但`Gitflow`工作流没有在这里止步。

### 发布分支

![](images/git-workflow-release-cycle-3release.png)

一旦`develop`分支上有了做一次发布（或者说快到了既定的发布日）的足够功能，就从`develop`分支上`fork`一个发布分支。
新建的分支用于开始下一轮的开发循环，之后新的功能不能再加到这个分支上，这个分支只应该做`Bug`修复、文档生成和其它面向发布任务。
一旦对外发布的工作都完成了，发布分支合并到`master`分支并分配一个版本号打好`Tag`。
另外，这些从新建发布分支以来的做的修改要合并回`develop`分支。

使用一个做准备分布的专门分支，使得一个团队可以在完善当前的发布版本，同时另一个团队继续开发下个版本的功能。
这也打造定义良好的开发阶段（比如，可以很轻松地说，『这周我们要做准备发布版本4.0』，并且在仓库的目录结构中可以真实地看到）。

一般的分支约定：

```
用于新建发布分支的分支: develop
用于合并的分支: master
分支命名: release-* or release/*
```

### 维护分支

![](images/git-workflow-release-cycle-4maintenance.png)

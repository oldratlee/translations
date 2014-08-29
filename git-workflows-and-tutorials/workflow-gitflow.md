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

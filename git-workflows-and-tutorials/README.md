原文链接：[Comparing Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)  
译文发在[博乐在线](http://www.jobbole.com/)： [http://blog.jobbole.com/76550/](http://blog.jobbole.com/76843/)，2014-09-14  
PS：原文的老链接和标题是[Git Workflows and Tutorials](https://www.atlassian.com/git/workflows)，`atlassian`改地址后换了文章标题，译文保留使用原标题。

## 🍎 译序 

关于`Git`工作流主题，也许这是目前最全面最深入的说明。这篇指南以大家在`SVN`中已经广为熟悉使用的集中式工作流作为起点，循序渐进地演进到其它高效的分布式工作流，还介绍了如何配合使用便利的`Pull Request`功能，体系地讲解了各种工作流的应用。
如果你`Git`用的还不多，可以从前面的讲的工作流开始操练。操作过程去感受指南的讲解：解决什么问题、如何解决问题，这样理解就深了，也方便活用。

行文中实践原则和操作示例并重，对于`Git`的资深玩家可以梳理思考提升，而新接触的同学，也可以跟着step-by-step操练学习并在实际工作中上手使用。

工作流其实不是一个初级主题，背后的本质问题其实是 有效的项目流程管理 和 高效的开发协同约定，而不仅是`Git`或`SVN`等[`VCS`](http://zh.wikipedia.org/wiki/%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)或[`SCM`](http://zh.wikipedia.org/wiki/%E8%BD%AF%E4%BB%B6%E9%85%8D%E7%BD%AE%E7%AE%A1%E7%90%86)工具的使用。

关于`Git`工作流主题，网上体系的中文资料不多，主要是零散的操作说明，希望这篇文章能让你更深入理解并在工作中灵活有效地使用起来。

`Gitflow`工作流是经典模型，处于核心位置，体现了工作流的经验和精髓。随着项目过程复杂化，你会感受到这个工作流中的深思熟虑和威力！

`Forking`工作流是分布式协作的（`GitHub`风格）可以先看看`GitHub`的Help：[Fork A Repo](https://help.github.com/articles/fork-a-repo/)和[Using pull requests](https://help.github.com/articles/using-pull-requests/) 。照着操作，给一个`GitHub`项目贡献你的提交，有操作经验再看指南容易意会。指南中给了[自己实现`Fork`的方法](https://github.com/oldratlee/translations/blob/master/git-workflows-and-tutorials/workflow-forking.md#%E5%BC%80%E5%8F%91%E8%80%85fork%E6%AD%A3%E5%BC%8F%E4%BB%93%E5%BA%93)：`Fork`就是服务端的克隆。在指南的操练中使用的是代码托管服务（如`GitHub`、[`Bitbucket`](https://bitbucket.org)），可以点一下按钮就让开发者完成仓库的`fork`操作。

文中`Pull Request`的介绍用的是[`Bitbucket`](https://bitbucket.org)代码托管服务，由于和`GitHub`基本一样，如果你用的是`GitHub`（我自己也主要使用`GitHub`托管代码），不影响理解和操作。

**_PS_**：

更多`Git`学习资料参见

- [`Git`的资料整理](https://github.com/xirong/my-git) by [@xirong](https://github.com/xirong)
- 自己整理的分享PPT [`Git`使用与实践](https://github.com/oldratlee/software-practice-miscellany/blob/master/git/git-gitlab-usage.pptx) @ [个人整理一些`Git`](https://github.com/oldratlee/software-practice-miscellany/tree/master/git)

----------------

- 🙈 [自己](http://weibo.com/oldratlee)理解粗浅，翻译中不足和不对之处，欢迎 👏
    - 建议，[提交`Issue`](https://github.com/oldratlee/translations/issues/new)
    - 指正，[`Fork`后提通过`Pull Request`贡献修改](https://github.com/oldratlee/translations/fork)
- 如有文章理解上有疑问 或是 使用过程中碰到些疑惑，请随意:raised_hands:[提交`Issue`](https://github.com/oldratlee/translations/issues/new) ，一起交流学习讨论！

----------------

`Git`工作流指南
======================

👉 工作流有各式各样的用法，但也正因此使得在实际工作中如何上手使用变得很头大。这篇指南通过总览公司团队中最常用的几种`Git`工作流让大家可以上手使用。

在阅读的过程中请记住，本文中的几种工作流是作为方案指导而不是条例规定。在展示了各种工作流可能的用法后，你可以从不同的工作流中挑选或揉合出一个满足你自己需求的工作流。

![Git Workflows](images/git_workflow.png)

🍺 概述
---------------------

### 集中式工作流

如果你的开发团队成员已经很熟悉`Subversion`，集中式工作流让你无需去适应一个全新流程就可以体验`Git`带来的收益。这个工作流也可以作为向更`Git`风格工作流迁移的友好过渡。

[了解更多 »](workflow-centralized.md)

![Git Workflows: SVN-style](images/git-workflow-svn.png)

### 功能分支工作流

功能分支工作流以集中式工作流为基础，不同的是为各个新功能分配一个专门的分支来开发。这样可以在把新功能集成到正式项目前，用`Pull Requests`的方式讨论变更。

[了解更多 »](workflow-feature-branch.md)

![Git Workflows: Feature Branch](images/git-workflow-feature_branch.png)

### `Gitflow`工作流

`Gitflow`工作流通过为功能开发、发布准备和维护分配独立的分支，让发布迭代过程更流畅。严格的分支模型也为大型项目提供了一些非常必要的结构。

[了解更多 »](workflow-gitflow.md)

![Git Workflows: Gitflow Cycle](images/git-workflows-gitflow.png)

### `Forking`工作流

`Forking`工作流是分布式工作流，充分利用了`Git`在分支和克隆上的优势。可以安全可靠地管理大团队的开发者（`developer`），并能接受不信任贡献者（`contributor`）的提交。

[了解更多 »](workflow-forking.md)

![Git Workflows: Forking](images/git-workflow-forking.png)

### `Pull Requests`

`Pull requests`是`Bitbucket`提供的让开发者更方便地进行协作的功能，提供了友好的`Web`界面可以在提议的修改合并到正式项目之前对修改进行讨论。

[了解更多 »](pull-request.md)

![Workflows: Pull Requests](images/pull-request.png)

目录
-----------------

- [译序](#apple-译序)
- [概述](#git工作流指南)
- [集中式工作流](workflow-centralized.md)
    - [工作方式](workflow-centralized.md#beer-工作方式)
        - [冲突解决](workflow-centralized.md#冲突解决)
    - [示例](workflow-centralized.md#beer-示例)
        1. [有人先初始化好中央仓库](workflow-centralized.md#有人先初始化好中央仓库)
        1. [所有人克隆中央仓库](workflow-centralized.md#所有人克隆中央仓库)
        1. [小明开发功能](workflow-centralized.md#小明开发功能)
        1. [小红开发功能](workflow-centralized.md#小红开发功能)
        1. [小明发布功能](workflow-centralized.md#小明发布功能)
        1. [小红试着发布功能](workflow-centralized.md#小红试着发布功能)
        1. [小红在小明的提交之上`rebase`](workflow-centralized.md#小红在小明的提交之上rebase)
        1. [小红解决合并冲突](workflow-centralized.md#小红解决合并冲突)
        1. [小红成功发布功能](workflow-centralized.md#小红成功发布功能)
    - [下一站](workflow-centralized.md#beer-下一站)
- [功能分支工作流](workflow-feature-branch.md)
    - [工作方式](workflow-feature-branch.md#beer-工作方式)
        - [`Pull Requests`](workflow-feature-branch.md#pull-requests)
    - [示例](workflow-feature-branch.md#beer-示例)
        1. [小红开始开发一个新功能](workflow-feature-branch.md#小红开始开发一个新功能)
        1. [小红要去吃个午饭](workflow-feature-branch.md#小红要去吃个午饭)
        1. [小红完成功能开发](workflow-feature-branch.md#小红完成功能开发)
        1. [小黑收到`Pull Request`](workflow-feature-branch.md#小黑收到pull-request)
        1. [小红再做修改](workflow-feature-branch.md#小红再做修改)
        1. [小红发布她的功能](workflow-feature-branch.md#小红发布她的功能)
        1. [与此同时，小明在做和小红一样的事](workflow-feature-branch.md#与此同时小明在做和小红一样的事)
    - [下一站](workflow-feature-branch.md#beer-下一站)
- [`Gitflow`工作流](workflow-gitflow.md)
    - [工作方式](workflow-gitflow.md#beer-工作方式)
        - [历史分支](workflow-gitflow.md#历史分支)
        - [功能分支](workflow-gitflow.md#功能分支)
        - [发布分支](workflow-gitflow.md#发布分支)
        - [维护分支](workflow-gitflow.md#维护分支)
    - [示例](workflow-gitflow.md#beer-示例)
        1. [创建开发分支](workflow-gitflow.md#创建开发分支)
        1. [小红和小明开始开发新功能](workflow-gitflow.md#小红和小明开始开发新功能)
        1. [小红完成功能开发](workflow-gitflow.md#小红完成功能开发)
        1. [小红开始准备发布](workflow-gitflow.md#小红开始准备发布)
        1. [小红完成发布](workflow-gitflow.md#小红完成发布)
        1. [最终用户发现`Bug`](workflow-gitflow.md#最终用户发现bug)
    - [下一站](workflow-gitflow.md#beer-下一站)
- [`Forking`工作流](workflow-forking.md)
    - [工作方式](workflow-forking.md#beer-工作方式)
        - [正式仓库](workflow-forking.md#正式仓库)
        - [`Forking`工作流的分支使用方式](workflow-forking.md#forking工作流的分支使用方式)
    - [示例](workflow-forking.md#beer-示例)
        1. [项目维护者初始化正式仓库](workflow-forking.md#项目维护者初始化正式仓库)
        1. [开发者`fork`正式仓库](workflow-forking.md#开发者fork正式仓库)
        1. [开发者克隆自己`fork`出来的仓库](workflow-forking.md#开发者克隆自己fork出来的仓库)
        1. [开发者开发自己的功能](workflow-forking.md#开发者开发自己的功能)
        1. [开发者发布自己的功能](workflow-forking.md#开发者发布自己的功能)
        1. [项目维护者集成开发者的功能](workflow-forking.md#项目维护者集成开发者的功能)
        1. [开发者和正式仓库做同步](workflow-forking.md#开发者和正式仓库做同步)
    - [下一站](workflow-forking.md#beer-下一站)
- [`Pull Requests`](pull-request.md)
    - [解析`Pull Request`](pull-request.md#解析pull-request)
    - [工作方式](pull-request.md#beer-工作方式)
        - [在功能分支工作流中使用`Pull Request`](pull-request.md#在功能分支工作流中使用pull-request)
        - [在`Gitflow`工作流中使用`Pull Request`](pull-request.md#在gitflow工作流中使用pull-request)
        - [在`Forking`工作流中使用`Pull Request`](pull-request.md#在forking工作流中使用pull-request)
    - [示例](pull-request.md#beer-示例)
        1. [小红`fork`正式项目](pull-request.md#小红fork正式项目)
        1. [小红克隆她的`Bitbucket`仓库](pull-request.md#小红克隆她的bitbucket仓库)
        1. [小红开发新功能](pull-request.md#小红开发新功能)
        1. [小红`push`功能到她的`Bitbucket`仓库中](pull-request.md#小红push功能到她的bitbucket仓库中)
        1. [小红发起`Pull Request`](pull-request.md#小红发起pull-request)
        1. [小明review `Pull Request`](pull-request.md#小明review-pull-request)
        1. [小红补加提交](pull-request.md#小红补加提交)
    - [下一站](pull-request.md#beer-下一站)

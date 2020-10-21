# Git 的使用

## 版本控制系统

### 什么是版本控制

版本控制是用于对某一组文件进行版本的记录，即在某一时刻为这一组文件提供一个快照，并将此快照进行存储。当你需要查看一个之前的快照（称之为“版本”）时，版本控制系统可以显示出当前版本与上一个版本之间的所有改动的细节。

### 为什么需要进行版本控制

1. 存储文件副本

   可能你平时在进行coding的时候，需要对当前的这个文件进行备份，避免你写的代码使得原来的文件混乱了，导致回不到原来的结果。尤其在修复bug的时候，是非常重要的。

2. 版本控制

   避免版本管理混乱。这是使用版本管理的最主要原因，也是版本管理的目的所在。你肯定不会希望在本地手动备份了多个副本后，到头来却不知道那个备份是最新的，那个备份进行了什么修改，修改日期是什么时候等等一切你记不清的问题。而版本管理软件能解决这些问题，它有详细的日志，能记住你的每一次提交、每一次改动，并且能够比较查看不同版本之间的异同，并且可以恢复到之前的任一版本。

3. 提高协同效率

   在进行多人开发的时候，使用版本控制系统可以非常好的避免在代码合并的时候可能出现的文件混乱等现象，例如你在手动合并代码的时候，可能会漏掉某些文件，也可能某个文件的某个段落漏掉了等等，这在两个人协同开发的时候就容易出现问题，三个，四个人，会有非常大的概率出现这些问题。

4. 明确分工责任

   使用版本控制系统可以非常容易定位出现bug的代码是谁写的，可以评估每个人的代码能力，代码风格等，明确责任。

## 操作其他远程仓库

Git 除了可以操作自己的本地仓库和 origin 远程仓库，也可以添加其他仓库进行操作。如果其他仓库开放了写入权限，还可以直接更改其他仓库。

本节涉及如下知识点：

1. 管理远程仓库列表
2. 从其他远程仓库合并
3. 将代码推送到其他远程仓库

### 管理远程仓库列表

每个 Git 本地仓库都可以记录许多远程仓库，每个远程仓库记录（简称“远程记录”）都可以起一个名字（简称“远程记录名”），这些远程仓库的地址都可以通过 `git remote` 命令进行操作。常用的操作有增、删、查、改。

**增**：如果要增加一个远程仓库，地址是 https://gitlab.gwmodel.whu.edu.cn/HPDell/GitDemo.git ，并命名为 gitlab ，则使用如下命令

```bash
git remote add gitlab http://gitlab.gwmodel.whu.edu.cn/HPDell/GitDemo.git
## 格式
# git remote add <远程记录名> <远程仓库地址>
```

远程仓库地址可以是 http https ssh 等类型的地址。

**查**：如果要查看本地仓库添加了哪些远程，则使用如下命令

```bash
git remote -v
## 输出
# origin  git@github.com:HPDell/GitDemo.git (fetch)
# origin  git@github.com:HPDell/GitDemo.git (push)
# gitlab  http://gitlab.gwmodel.whu.edu.cn/hpdell/GitDemo.git (fetch)
# gitlab  http://gitlab.gwmodel.whu.edu.cn/hpdell/GitDemo.git (push)
```

每个远程仓库对应了一个 fetch 地址和一个 push 地址，一般情况下 fetch 和 push 地址是一样的。因为我们添加远程的时候就指定了一个地址。

如果不加 `-v` 参数，那么只会输出所有远程记录名

**改**：修改远程仓库涉及到两个修改，即“重命名远程仓库”和“修改远程仓库地址”。

重命名远程仓库使用 `git remote rename` ，例如把 gitlab 远程名重命名为 lab ，使用如下命令

```bash
git remote rename gitlab lab
## 格式
# git remote rename <远程记录名> <新名称>
```

修改远程仓库地址使用 `git remote set-url` ，例如把 gitlab 远程仓库的地址修改为 ssh 的地址，使用如下命令

```bash
git remote set-rul gitlab git@gitlab.gwmodel.whu.edu.cn:hpdell/GitDemo.git
## 格式
# git remote set-url <远程记录名> <新地址>
```

**删**：如果要删除一个远程记录，如 gitlab ，则使用如下命令

```bash
git remote remove gitlab 
## 格式
# git remote remove <远程记录名>
```

总结起来，这套命令其实非常好记，基本格式就是

```bash
git remote <动作> <远程记录名> [其他参数]
```

不同的动作及其含义和参数总结如下表

| 动作      | 动作含义               | 参数                     |
| --------- | ---------------------- | ------------------------ |
| `add`     | 新增远程记录           | 远程仓库地址             |
| `rename`  | 重命名远程记录         | 新的记录名               |
| `set-url` | 设置远程记录的仓库地址 | 远程仓库地址             |
| `remove`  | 删除远程记录           |                          |
|           | 显示远程记录列表       | 有 `-v` 表示显示详细信息 |

### 从其他远程仓库合并

这个情况一般发生在当别人的仓库中做了一些更改，想要合并到自己的仓库中时。例如：

- 张三和李四的仓库克隆自同一个仓库，张三做了一些修改，李四想要合并张三的修改
- 张三克隆了 Vue 的仓库，但尤雨溪合并了其他人提交的 Pull Request ，张三需要将这些更新合并到自己的仓库中

比如我要合并 HaoKunT/GitDemo 的仓库，执行这样操作的完整流程是这样的

```bash
git remote add HaoKunT git@github.com:HaoKunT/GitDemo.git
git fetch HaoKunT
git checkout HaoKunT/master 
git checkout -b master-HaoKunT
git checkout master
git merge --no-ff master-HaoKunT
```

或者省略到中间 `git checkout` 的操作，直接合并“远程分支”。

```bash
git remote add HaoKunT git@github.com:HaoKunT/GitDemo.git
git fetch HaoKunT
git merge --no-ff HaoKunT/master
```

总结起来有四步：添加远程、获取远程、签出分支、合并分支。签出分支一步可以省略，此时合并分支直接合并远程分支。

**添加远程**：添加一个远程记录，地址是远程仓库。和之前所讲的一样。

**获取远程**：获得远程仓库中的提交记录。这里使用的是 `git fetch` 命令，后面可以加一个远程记录名作为参数，用于将远程仓库的记录获取到本地仓库的 .git 文件夹中，而不对代码文件作修改。如果要获取一个远程记录中的仓库 HuYigong/GitDemo 的递交记录，使用方法是

```bash
git fetch HuYigong/GitDemo
## 格式
# git fetch <远程记录名>
```

而如果不加参数，则认为是获取 origin 远程的提交记录。

**签出分支**：签出同样也是使用的 `git checkout` ，后面加分支名。这个分支可以是一个远程分支。如果签出的分支名是远程分支名，那么签出的结果是使 HEAD 指向一个提交记录，此时需要再执行下列命令创建一个本地分支，这个分支拥有的提交记录和远程一样。

```bash
git checkout -b master-HaoKunT
## 格式
# git checkout -b <新分支名>
```

在签出之前可以查看一下远程仓库的分支等信息，使用的是 `git branch ` 命令，后面加上 `-r` 参数可以查看远程分支，加上 `-a` 参数可以查看远程和本地分支

```bash
git branch -a -v 
## 输出
#  main                4566a89 Create README.md
#* master-HaoKunT      4566a89 Create README.md
#  remotes/origin/HEAD -> origin/main
#  remotes/origin/main 4566a89 Create README.md
#  remotes/qqrepo/main 160531f Update README.md
```

> 三个签出的操作，可以使用另一套方法完成：新建分支、设置上游、拉取代码。
>
> ```bash
> git checkout -b master-HaoKunT
> git branch --set-upstream-to=HaoKunT/master 
> git pull --rebase
> ```
>
> 其中，`git branch` 所加的选项 `--set-upstream-to=HaoKunT/master` 称为设置上游。

**合并分支**：签出到要合并到的分支后，同样也是使用 `git merge ` 命令来合并新的分支。后面可以加一个本地分支，也可以加一个远程分支。

> 之所以本地仓库可以直接合并远程分支，是因为经过 `git fetch` 操作后，远程仓库中的所有提交记录就已经下载到本地仓库中了，此时这些分支就和本地仓库中未签出的分支一样，可以进行同样的操作。所以他们虽然叫做远程分支，但是是存储在本地仓库中的。

### 推送到其他远程仓库

如果要推送到的远程仓库有权限，那么可以直接使用 `git push` 命令推送到远程仓库，后面跟上要推送到的远程记录名，远程会创建一个相同的分支。例如，将 master 分支推送到 HaoKunT/master 

```bash
git push HaoKunT master
```

如果后面不加 `master` 参数，则指定推送当前分支。

## 团队协作

高效团队协作不仅需要优秀的工具，也需要良好的使用方案。除了好用的工具以外，不同角色的成员如何使用工具也值得研究。本节介绍一下“工作流”以及其他团队协作技巧。

### 分支的性质和作用

当创建一个仓库的时候，会自动创建一个 `master` 分支（或者是 `main` 分支），这个分支称为“主分支”，其他签出的分支就称为“其他分支”。分支虽然叫分支，但不是项目的分叉，将项目带往不同的方向（虽然可以这样用）。所有的其他分支最终都要归结到主分支上。

#### 性质

可以说，提交是版本管理的基础，分支是团队协作的基础。在团队协作的实践中，往往一个分支对应了软件的一个功能开发过程。将不同的任务分发给不同的成员之后，成员在自己的分支上进行开发，最终通过合并分支操作将这些功能集成在一起，形成一个具有完整功能的软件。

| 分支生命周期 | 软件开发过程       |
| ------------ | ------------------ |
| 创建         | 需求提出           |
| 提交         | 需求实现           |
| 合并         | 功能测试、需求完成 |

#### 分类

分支可以分为两类：长期分支和短期分支。这两类分支各有不同的用处。

**短期分支**：合并到主分支后就可以被删除的分支，俗称“阅后即焚”。这些分支往往对应一个目的，根据目的的类型将其分为很多类，并设定统一前缀。短期分支可以同时存在多个（当然命名不能重复）。

> 阅后即焚的含义是：管理员在审查完代码后觉得可以合并，就合并分支，然后删除这个分支。“阅”即“code review”。

**长期分支**：版本库一直存在的分支。通常这个分支用于集成功能（即所有短期分支合并到这个分支上），并标记 tag 等信息，同时也和持续集成等自动化工具一起使用。所有短期分支都从长期分支上签出，以保证分支间的关系。长期分支往往只存在少数几个。

#### 关系

当分支 `B` 从分支 `A` 签出时，这两个分支就形成了 `A --> B` 的关系链。

一般情况下，保持这样的关系链不变，而且总是从 `B` 合并到 `A` ，而避免反向合并。如果要在 `B` 分支上应用 `A` 分支的更改，需要使用 `git rebase` 命令。

### 工作流

即 Git 工作流。但其实目前不只有一种工作流方案，不同公司内部会有不同的工作流，我们实验室不同项目也采用过不同的工作流（例如 [VideoFireMonitorSystem](https://github.com/GWmodel-Lab/VideoFireMonitorSystem) 和 [实验室主页](https://github.com/GWmodel-Homepage/) 两个项目）。所以工作流的选取是根据实际情况而定的。下面介绍几个常用的工作流，并分析一下适用情况。

#### Git Workflow

这是来自 Git 本身推荐的工作流，比较适合一般的多人协作开发项目。其特点是：

- 远程仓库只有一个，所有人或部分人对远程仓库具有读写权限。
- master 分支作为正式发布版本，release 分支作为预发布版本。
- 分支的权限不做明确要求，所有对仓库具有读写权限的人均可读可写。

![Git Workflow](./images/GitWorkflow.png)

这个工作流主要包含几个类型的分支：

- 长期分支 `master` 用于管理发布版本，每次 commit (其他分支向它合并形成的 merge commit)应当对应一个 Tag，也就是形成一个发布版。
- 长期分支 `develop` 用于管理开发版本，所有的开发都会汇总到这个分支。
- 短期分支 `release` 用于在正式发布之前的预发布版本，在这个版本中的提交都应当是修复 Bug，不能在本分支上开发新的功能。本分支应当从 `develop` 检出，Bug 修复之后合并到 `develop` 和 `master`。
- 短期分支 `feature` 用于新功能的开发，可以有多个。本分支应当从 `develop` 分支检出，功能开发完成后合并(merge)到 `develop`。
- 短期分支 `hotfix` 用于在版本发布之后的紧急 Bug 修复。本分支应当从 `master` 分支检出，在 Bug 修复之后直接合并(merge)到 `master` 和 `develop` 。

整个开发过程中，可以根据如下时序图进行分支的创建、合并等操作。

![Git Workflow 时序图](./images/GitWorkflowTime.png)

这个 Git Workflow 适合于以下情况的使用：

- 远程仓库只有一个，并不涉及多个远程仓库的情况
- 专门有一个预发布的过程，往往对应了 β-测试。测试时不再开发其他功能

因而被称为“单远程库预发布”模式。

#### 简化的 Git Workflow 

这个工作流去掉了 Git Workflow 中的 `develop` 和 `release` 分支，所有 `feature` 分支从 `master` 分支上签出，并合并到 `master` 分支上。

这个 Git Workflow 适合于以下情况的使用：

- 团队成员比较少，或只有个人开发
- 不需要、或没有精力维护预发布过程

往往个人开发者在开发时，采用这个工作流比较合适。

#### GitHub Workflow

这是 GitHub 推荐的工作流，比较适合一般的开源项目的开发。

![GitHub Workflow](./images/GitHubWorkflow.jpg)

这个工作流的特点是：

- 有一个中央仓库，托管在 GitHub 上，每个开发人员通过 Fork 操作创建自己的远程仓库。
- 自己只操作自己的远程仓库，一般没有中央仓库的写权限。
- 推荐每个人直接将更改应用到 `master` 分支上，而不需要 `develop` 分支等。个人仓库如何维护，中央仓库并不关心。
- 个人远程仓库通过 Pull Request 向中央仓库提交更改。

这个工作流不同公司也有不同的扩展方法，如字节跳动一般采用如下工作流

![ByteDance Workflow](./images/ByteDanceWorkflow.jpg)

这种工作流适合于以下情况的使用：

- 团队开发人员众多，或者并不都是团队内部人士。
- 有专门的代码审查和测试人员，或配置了自动测试环境。

对于“单远程库”，如果设置某些分支的写权限，也可以实现类似于此方法的工作流。

### 合并提交

合并提交有以下三种方法： merge 、rebase 和 Pull Request 。三种方法分别适合不同的场景。

以这样一个情况进行举例。其中 `A1` 是一个提交，字母表示分支，数字表示提交的顺序。

```bash
A1 -> A2
  \
   B1 -> B2
```

**merge** ：对于具有如下关系的两个分支 `A-->B` ，将 `B` 的更改应用到 `A` 上。使用如下命令：

```bash
# [branch: A]
git merge --no-ff B
```

其中，选项 `--no-ff` 表示非快进式合并，建议使用该选项。使用该选项之后，git 会在使提交记录保留原有的分支结构

```
A1 -> A2 -> + -> 新的提交
  \        /
   B1 -> B2
```

如果没有这个参数，合并后两个分支的提交就会混在一起

```
A1 -> B1 -> A2 -> B2 -> + -> 新的提交
```

**rebase** ：对于具有如下关系的两个分支 `A-->B` ，将 `A` 的更改应用到 `B` 上。使用如下命令：

```bash
# [branch: B]
git rebase A
```

这样操作的结果是

```
A1 -> A2
        \
         B1 -> B2
```

这样 `B` 分支就已经包含了 `A` 上较新的更改。

> 此时 `B` 分支上所有提交的 ID 都已经发生更改，当推送到远程仓库时，会出现冲突，但这个冲突并不真的冲突，只是在同样的位置上添加了同样的代码导致的。所以**可以直接强制推送到远程仓库**。

**Pull Request** ：简称 PR 。这是只有 GitHub 和 GitLab 等 Git 托管服务才有的功能， Git 本身是没有的。这个过程发生在两个仓库之间。

![Pull Request](./images/PullRequest.png)

仓库 HPDell/GitDemo 从 GWmodel-Lab/GitDemo （中央仓库） 通过 Fork （暂称“派生”）操作得到，那么 HPDell/GitDemo 向 GWmodel-Lab/GitDemo 发起 PR ，这个是 PR 是属于 GWmodel-Lab/GitDemo 的，也只有 GWmodel-Lab/GitDemo 的管理员可以合并该提交。

如果 PR 已经发起，那么之后源仓库中源分支上的任何更改都会添加到这个 PR 的提交记录中，因此可以不断对代码进行完善。

创建 PR 时，可以选择源仓库、源分支、目标仓库、目标分支四项。默认情况下，目标仓库是被 Fork 的仓库，源分支和目标分支一致。目标仓库可以修改，但可选择的仓库必须**最终**都是从中央仓库派生的仓库，它可能时从派生库中派生的，这不影响。

中央仓库合并 PR 后，更改不会自动应用到派生的远程仓库。此时，可在本地仓库中添加一条中央仓库的远程记录，拉去中央仓库的更新，并推送到远程仓库

```bash
# [branch: main]
git remote add organize git@github.com:GWmodel-Lab/GitDemo.git
git fetch organize
git rebase organize/main
```

使用 PR 的好处在于：

- 可以和持续集成服务结合，对代码进行自动测试
- 便于对分支的管理，可以记录测试和交流情况
- 可以和聊天软件或邮件结合，自动发送通知


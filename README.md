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

## 自己操作别人的仓库

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

总结起来有四步：添加远程、获取远程、签出分支、合并分支。

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
git checkout -b <新分支名>
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

**合并分支**：签出到要合并到的分支后，同样也是使用 `git merge ` 命令来合并新的分支。

> 三个签出的操作，可以使用另一套方法完成：新建分支、设置上游、拉取代码。
>
> ```bash
> git checkout -b master-HaoKunT
> git branch --set-upstream-to=HaoKunT/master 
> git pull --rebase
> ```
>
> 其中，`git branch` 所加的选项 `--set-upstream-to=HaoKunT/master` 称为设置上游。

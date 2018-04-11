### git常用命令

#### git常用命令流程图

![](https://github.com/wisestcoder/blog/blob/master/git/images/gitflow.png)

#### 全命令

- workspace: 本地的工作目录。（记作A）
- index：缓存区域，临时保存本地改动。（记作B）
- local repository: 本地仓库，只想最后一次提交HEAD。（记作C）
- remote repository：远程仓库。（记作D）

> 以下所有的命令的功能说明，都采用上述的标记的A、B、C、D的方式来阐述。
```bash
  #初始化
git init //创建
git clone /path/to/repository //检出
git config --global user.email "you@example.com" //配置email
git config --global user.name "Name" //配置用户名
#操作
git add <file> // 文件添加，A → B
git add . // 所有文件添加，A → B
git commit -m "代码提交信息" //文件提交，B → C
git commit --amend //与上次commit合并, *B → C
git push origin master //推送至master分支, C → D
git pull //更新本地仓库至最新改动， D → A
git fetch //抓取远程仓库更新， D → C
git log //查看提交记录
git status //查看修改状态
git diff//查看详细修改内容
git show//显示某次提交的内容
#撤销操作
git reset <file>//某个文件索引会回滚到最后一次提交， C → B
git reset//索引会回滚到最后一次提交， C → B
git reset --hard // 索引会回滚到最后一次提交， C → B → A
git checkout // 从index复制到workspace， B → A
git checkout -- files // 文件从index复制到workspace， B → A
git checkout HEAD -- files // 文件从local repository复制到workspace， C → A
#分支相关
git checkout -b branch_name //创建名叫“branch_name”的分支，并切换过去
git checkout master //切换回主分支
git branch -d branch_name // 删除名叫“branch_name”的分支
git push origin branch_name //推送分支到远端仓库
git merge branch_name // 合并分支branch_name到当前分支(如master)
git rebase //衍合，线性化的自动， D → A
#冲突处理
git diff //对比workspace与index
git diff HEAD //对于workspace与最后一次commit
git diff <source_branch> <target_branch> //对比差异
git add <filename> //修改完冲突，需要add以标记合并成功
#其他
gitk //开灯图形化git
git config color.ui true //彩色的 git 输出
git config format.pretty oneline //显示历史记录时，每个提交的信息只显示一行
git add -i //交互式添加文件到暂存区
```

#### 工作常用命令
```bash
#初始设置
git config --global user.name "<用户名>" #设置用户名
git config --global user.email "<电子邮件>" #设置电子邮件
#本地操作
git add [-i] #保存更新，-i为逐个确认。
git status #检查更新。
git commit [-a] -m "<更新说明>" #提交更新，-a为包含内容修改和增删，-m为说明信息，也可以使用 -am。
#远端操作
git clone <git地址> #克隆到本地。
git fetch #远端抓取。
git merge #与本地当前分支合并。
git pull [<远端别名>] [<远端branch>] #抓取并合并,相当于第2、3步
git push [-f] [<远端别名>] [<远端branch>] #推送到远端，-f为强制覆盖
git remote add <别名> <git地址> #设置远端别名
git remote [-v] #列出远端，-v为详细信息
git remote show <远端别名> #查看远端信息
git remote rename <远端别名> <新远端别名> #重命名远端
git remote rm <远端别名> #删除远端
git remote update [<远端别名>] #更新分支列表
#分支相关
git branch [-r] [-a] #列出分支，-r远端 ,-a全部
git branch <分支名> #新建分支
git branch -b <分支名> #新建并切换分支
git branch -d <分支名> #删除分支
git checkout <分支名> #切换到分支
git checkout -b <本地branch> [-t <远端别名>/<远端分支>] #-b新建本地分支并切换到分支, -t绑定远端分支
git merge <分支名> #合并某分支到当前分支
```

#### stash 暂存命令
使用场景，如你正在一个分支 a 上修改着代码什么的，但是这时候，有一个紧急的任务，需要你切换到另一个分支 b 去做些工作，而 a 上的代码还是个半吊子，不想去 commit 甚至不想去 add ，这时候 stash 命令就大有用处了，前提是没有执行 commit
```bash
git stash #当前分支没有 commit 的代码先暂存起来，分支也会变得干净
git stash list #查看暂存区记录
git stash apply #恢复至暂存前的状态
git stash drop #删除最近的一条stash记录
git stash pop #等同于同时执行了apply和drop
git stash clear #清空暂存区的所有记录
```

#### git diff 命令
```bash
git diff #工作区与暂存区之间的差异对比
git diff --cached #暂存区与版本库之间的差异对比
git diff master #工作区与版本库之间的差异对比
```

#### github更新自己Fork的代码
1、clone 自己账号里fork的分支
```bash
git clone to/my/repo.git
```
2、增加远程原始分支到本地（可以用 git remote -v 命令查看远程分支列表）
```bash
$ git remote -v
origin  to/my/repo.git (fetch)
origin  to/my/repo.git (push)
```
如果没有远程原始分支则需要增加：
```bash
git remote add alias to/origin.repo.git
```
查看确认远程分支列表：
```bash
git remote -v
origin  to/my/repo.git (fetch)
origin  to/my/repo.git (push)
alias  to/origin.repo.git (fetch)
alias  to/origin.repo.git (push)
```
3、fetch原始源分支的新版本到本地
```bash
git fetch alias
```
4、合并两个版本的代码
```bash
git merge alias/master
```
5、把最新的代码提交到github自己的仓库
```bash
git push origin master
```

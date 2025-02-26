刚开始添加https地址时写错了，最后面少加了.git

```bash
$ git remote add origin https://github.com/bitHY1/practice
```

用git fetch要账户验证，登录了一下github，虽然地址错了，但是成功了

```bash
$ git fetch
info: please complete authentication in your browser...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (6/6), 2.59 MiB | 4.37 MiB/s, done.
From https://github.com/bitHY1/practice

 * [new branch]      main       -> origin/main
```

后续git branch，log等各种都不出结果，连当前分支也不显示
又开始git fetch和git pull操作反复使用查看，均出现了连接失败还有其他问题

```bash
$ git fetch origin
fatal: unable to access 'https://github.com/bitHY1/practice/': Failed to connect to github.com port 443 after 21108 ms: Could not connect to server
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

git branch --set-upstream-to=origin/<branch> master
```

意识到可能时地址出错，修改地址，多种修改方法，set-url, git remote rm origin再重新添加等
重新git fetch失败

```bash
$ git fetch
fatal: unable to access 'https://github.com/bitHY1/practice.git/': Recv failure: Connection was reset
```

意识到网络原因，不开代理的话就用以下命令取消代理，但仍然可能存在失败

```bash
$ git config --global --unset http.proxy
$ git config --global --unset https.proxy
```

开vpn的话，报错是端口443不合适，因此查看电脑网络设置，代理时的端口，时7890，做以下设置

```bash
$ git config --global https.proxy http://127.0.0.1:7890
$ git config --global http.proxy http://127.0.0.1:7890
```

再开vpn，成功fetch

```bash
$ git fetch
From https://github.com/bitHY1/practice

 * [new branch]      main       -> origin/main
```

地址和之前差不多，也不知道是不是地址的问题，但是仍然发现工作区没有显示远端仓库的内容，git branch,log等操作也是和之前一样

询问chatgpt解决，但原理仍不清楚
加了-a，终于有反应了

```bash
$ git branch -a
  remotes/origin/main
#基于origin/main新建分支main并切换，工作区出现内容，但原来的master不见了
hy@DESKTOP-7LCBTGK MINGW64 ~/Desktop/test01 (master)
$ git checkout -b main origin/main
branch 'main' set up to track 'origin/main'.
Switched to a new branch 'main'
#此时不加-a也有结果显示
hy@DESKTOP-7LCBTGK MINGW64 ~/Desktop/test01 (main)
$ git branch

* main
```

打算删除本地仓库，从头再来一遍
之后再尝试ssh的方法



在本地修改此文件，先改成markdown格式，再记录一些之后的操作

再次fetch，merge，把该文件也克隆到本地了

```bash
hy@DESKTOP-7LCBTGK MINGW64 ~/Desktop/test01 (main)
$ git fetch
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (3/3), 2.39 KiB | 407.00 KiB/s, done.
From https://github.com/bitHY1/practice
   f611bd4..330ed31  main       -> origin/main

hy@DESKTOP-7LCBTGK MINGW64 ~/Desktop/test01 (main)
$ git log
commit f611bd4282a07886e90e336ef2ca33773411d5a6 (HEAD -> main)
Author: bitHY1 <100462239+bitHY1@users.noreply.github.com>
Date:   Tue Dec 24 14:39:28 2024 +0800

    Add files via upload

commit 42913364b5bd238e77d77ada2abd2e96497ee9dc
Author: bitHY1 <100462239+bitHY1@users.noreply.github.com>
Date:   Mon Dec 23 17:45:33 2024 +0800

    Initial commit

hy@DESKTOP-7LCBTGK MINGW64 ~/Desktop/test01 (main)
$ git branch
* main

hy@DESKTOP-7LCBTGK MINGW64 ~/Desktop/test01 (main)
$ git branch -a
* main
  remotes/origin/main

hy@DESKTOP-7LCBTGK MINGW64 ~/Desktop/test01 (main)
$ git merge   remotes/origin/main
Updating f611bd4..330ed31
Fast-forward
 problems | 63 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 63 insertions(+)
 create mode 100644 problems

```



之后push一下再重开吧


在dell电脑上继续另一个项目的实验，git branch出现与上述相同的情况，本分支也不显示
想到可能是当前分支有改动，但是还未提交的关系，commit之后再执行分支相关操作，均正常
此处的分支改动所造成的影响，具体改动是指在工作区添加了文件未跟踪，还是做了git add操作，因时间较久，忘记了，后续有精力再深究

该项目因为其中有.idea目录，因此添加了一个.gitignore文件，但是在提交了也把.idea目录提交了
经询问gpt，.gitignore 只能防止 Git 跟踪新的文件或文件夹。
如果你在 .gitignore 文件中添加了 .idea/，但是文件已经被 Git 跟踪并且已经提交过，那么你需要先停止 Git 对这些文件的跟踪。（后续再进行实际验证）
解决方法：
git rm -r --cached .idea/

在执行git log时发现显示的仅是当前所处分支的历史提交
有多种可选项和参数来实现不同的目的，eg：
git log --all --graph --oneline

在dell电脑上想push东西去另一个test仓库，也是先git remote add origin url建立联系
然后使用如下命令
git push -u origin my-branch:remote-branch
其中-u是--set-upstream的缩写，目的是建立远程分支与本地分支的联系
以后可以直接使用 git push 和 git pull，Git 会自动知道要推送或拉取哪个分支。
如果本地分支名和远程分支名相同，可以直接如下：
git push -u origin master
但是master是我的本地分支名，远程分支名是main，这样操作也成功了，发现在远程仓库多了一个master分支

git status或者git branch -vv都可以查看本地分支与远程分支对应关系
默认情况下，每个本地分支只跟踪一个远程分支，但你可以为同一个本地分支设置多个远程仓库（如 origin 和 backup），但每次推送只能指定一个远程分支：
git push origin master
git push backup master

根据询问gpt，即使已经设置了本地和远程分支的跟踪关系，在直接使用git push时，需位于当前分支，且当前分支有跟踪关系
如果在一个未设置跟踪分支的本地分支上使用 git push，Git 不会推送另一个已跟踪的分支，而是会出现错误，要求你明确指定要推送的远程分支（已验证确实是这样）

在dell电脑上设置好了ssh，发现在连接远程仓库时可以同时使用https和ssh的url，还可以fetch和push各用一个
（蛮迷惑的，改成ssh后，实际验证中用了以下命令
git remote set-url --add origin https://github.com/bitHY1/test.git
再查看，依然是ssh，只有像以下这种，才是fetch和push分开各用一个
git remote set-url --push origin url）
改了远程仓库的url后，之前设置的分支间的跟踪关系依然存在。这种跟踪关系通常是独立于远程仓库 URL 的。只要你没有删除或创建新的本地分支，或者没有改变分支的配置，跟踪关系应该保持不变。

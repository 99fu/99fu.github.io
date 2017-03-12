---
layout: wiki
title: Git
categories: Git
description: Git 常用操作记录。
keywords: Git, 版本控制
---

### 常用命令

| 功能                      | 命令                                  |
|:--------------------------|:--------------------------------------|
| 添加文件/更改到暂存区     | git add filename                      |
| 添加所有文件/更改到暂存区 | [git add .][git add --all]                             |
| 提交                      | git commit -m "msg"                     |
| 从远程仓库拉取最新代码    | git pull origin master                |
| 推送到远程仓库            | git push origin master                |
| 查看配置信息              | git config --list                     |
| 查看文件列表              | git ls-files                          |
| 比较工作区和暂存区        | git diff                              |
| 比较暂存区和版本库        | git diff --cached                     |
| 比较工作区和版本库        | git diff HEAD                         |
| 从暂存区移除文件          | git reset HEAD filename               |
| 查看本地远程仓库配置      | git remote -v                         |
| 回滚                      | git reset --hard 提交SHA              |
| 强制推送到远程仓库        | git push -f origin master             |
| 修改上次 commit           | git commit --amend                    |
| 推送 tags 到远程仓库      | git push --tags                       |
| 推送单个 tag 到远程仓库   | git push origin [tagname]             |
| 删除远程分支              | git push origin --delete [branchName] |
| 远程空分支（等同于删除）  | git push origin :[branchName]         |

### Q&A

#### 如何解决gitk中文乱码，git ls-files 中文文件名乱码问题？

在~/.gitconfig中添加如下内容

```
[core]
   quotepath = false
[gui]
   encoding = utf-8
[i18n]
   commitencoding = utf-8
[svn]
   pathnameencoding = utf-8
```

参考 <http://zengrong.net/post/1249.htm>

#### 如何处理本地有更改需要从服务器合入新代码的情况？

```
git stash
git pull
git stash pop
```

#### stash

查看 stash 列表：

```
git stash list
```

查看某一次 stash 的改动文件列表（不传最后一个参数默认显示最近一次）：

```
git stash show stash@{0}
```

以 patch 方式显示改动内容

```
git stash show -p stash@{0}
```

#### 如何合并 fork 的仓库的上游更新？

```
git remote add upstream https://upstream-repo-url
git fetch upstream
git merge upstream/master
```

#### 如何通过 TortoiseSVN 带的 TortoiseMerge.exe 处理 git 产生的 conflict？
* 将 TortoiseMerge.exe 所在路径添加到 `path` 环境变量。
* 运行命令 `git config --global merge.tool tortoisemerge` 将 TortoiseMerge.exe 设置为默认的 merge tool。
* 在产生 conflict 的目录运行 `git mergetool`，TortoiseMerge.exe 会跳出来供你 resolve conflict。

  > 也可以运行 `git mergetool -t vimdiff` 使用 `-t` 参数临时指定一个想要使用的 merge tool。

#### 不想跟踪的文件已经被提交了，如何不再跟踪而保留本地文件？

`git rm --cached /path/to/file`，然后正常 add 和 commit 即可。

#### 如何不建立一个没有 parent 的 branch？

```
git checkout --orphan newbranch
```

此时 `git branch` 是不会显示该 branch 的，直到你做完更改首次 commit。比如你可能会想建立一个空的 gh-pages branch，那么：

```
git checkout --orphan gh-pages
git rm -rf .
// add your gh-pages branch files
git add .
git commit -m "init commit"
```

#### submodule 的常用命令

**添加 submodule**

```
git submodule add git@github.com:philsquared/Catch.git Catch
```

这会在仓库根目录下生成如下 .gitmodules 文件并 clone 该 submodule 到本地。

```
[submodule "Catch"]
path = Catch
url = git@github.com:philsquared/Catch.git
```

**更新 submodule**

```
git submodule update
```

当 submodule 的 remote 有更新的时候，需要

```
git submodule update --remote
```

**删除 submodule**

在 .gitmodules 中删除对应 submodule 的信息，然后使用如下命令删除子模块所有文件：

```
git rm --cached Catch
```

**clone 仓库时拉取 submodule**

```
git submodule update --init --recursive
```

#### 删除远程 tag

```
git tag -d v0.0.9
git push origin :refs/tags/v0.0.9
```

或

```
git push origin --delete tag [tagname]
```

#### 清除未跟踪文件

```
git clean
```

可选项：

| 选项                    | 含义                             |
|-------------------------|----------------------------------|
| -q, --quiet             | 不显示删除文件名称               |
| -n, --dry-run           | 试运行                           |
| -f, --force             | 强制删除                         |
| -i, --interactive       | 交互式删除                       |
| -d                      | 删除文件夹                       |
| -e, --exclude <pattern> | 忽略符合 <pattern> 的文件        |
| -x                      | 清除包括 .gitignore 里忽略的文件 |
| -X                      | 只清除 .gitignore 里忽略的文件   |

#### 忽略文件属性更改

因为临时需求对某个文件 chmod 了一下，结果这个就被记为了更改，有时候这是想要的，有时候这会造成困扰。

```
git config --global core.filemode false
```

参考：[How do I make Git ignore file mode (chmod) changes?](http://stackoverflow.com/questions/1580596/how-do-i-make-git-ignore-file-mode-chmod-changes)

#### patch

将未添加到暂存区的更改生成 patch 文件：

```
git diff > demo.patch
```

将已添加到暂存区的更改生成 patch 文件：

```
git diff --cached > demo.patch
```

合并上面两条命令生成的 patch 文件包含的更改：

```
git apply demo.patch
```

将从 HEAD 之前的 3 次 commit 生成 3 个 patch 文件：

（HEAD 可以换成 sha1 码）

```
git format-patch -3 HEAD
```

生成 af8e2 与 eaf8e 之间的 commits 的 patch 文件：

（注意 af8e2 比 eaf8e 早）

```
git format-patch af8e2..eaf8e
```

合并 format-patch 命令生成的 patch 文件：

```
git am 0001-Update.patch
```

与 `git apply` 不同，这会直接 add 和 commit。

#### 只下载最新代码

```
git clone --depth 1 git://xxxxxx
```

这样 clone 出来的仓库会是一个 shallow 的状态，要让它变成一个完整的版本：

```
git fetch --unshallow
```

或

```
git pull --unshallow
```

#### 基于某次 commit 创建分支

```sh
git checkout -b test 5234ab
```

表示以 commit hash 为 `5234ab` 的代码为基础创建分支 `test`。

#### 恢复单个文件到指定版本

```sh
git reset 5234ab MainActivity.java
```

#### 恢复到指定版本

```sh
git reset --hard {版本号}
```

恢复 MainActivity.java 文件到 commit hash 为 `5234ab` 时的状态。

#### 放弃对git管理的文件修改
```
条件：bb.txt文件已经被git管理
过程：发现bb.txt文件被修改了，发现改了之后不是自己想要的结果
目的：想恢复到没有被修改的状态
解决办法：
git checkout bb.txt

git放弃对当前文件夹的所有文件的修改？
git checkout
```
#### git上传文件太大
```
git提交失败的解决办法 
1 提交后提示 
fatal: recursion detected in die handler 
问题原因： 
问题原因是http.postBuffer默认上限为1M所致。在git的配置里将http.postBuffer变量改大一些即可，比如将上限设为500M： 
git config --global http.postBuffer 524288000
在哪里执行以上命令呢？ 
打开git bash命令行工具。 
注意要加上--global。网上很多资料都没加这个参数。不加执行的话会报以下错误的： 
error:could not lock config file .git/config: no such file or directory.
使用TortoiseGit
右键TortoiseGit--settings--Git--Edit systemwide gitconfig--把postBuffer的值修改为524288000

git ssh失效解决办法
报错信息
git.exe clone --progress -v "https://git.duapp.com/appiddfged879rf" "F:\bae\yelp" 

Cloning into 'F:\bae\yelp'... 
fatal: unable to access 'https://git.duapp.com/appiddfged879rf/': SSL certificate problem: unable to get local issuer certificate

最简单的解决方法是执行下面的命令，然后重新执行 git clone 命令：
  git config --global http.sslVerify false
```


#### 设置全局 hooks

```sh
git config --global core.hooksPath C:/Users/mazhuang/git-hooks
```

然后把对应的 hooks 文件放在最后一个参数指定的目录即可。

比如想要设置在 commit 之前如果检测到没有从服务器同步则不允许 commit，那在以上目录下建立文件 pre-commit，内容如下：

```sh
#!/bin/sh

CURRENT_BRANCH=$(git rev-parse --abbrev-ref HEAD)

git fetch origin $CURRENT_BRANCH

HEAD=$(git rev-parse HEAD)
FETCH_HEAD=$(git rev-parse FETCH_HEAD)

if [ "$FETCH_HEAD" = "$HEAD" ];
then
    echo "Pre-commit check passed"
    exit 0
fi

echo "Error: you need to update from remote first"

exit 1
```


### 示例
#### 创建新的github仓库
```sh
git init
git add README.md
git commit -m "readme.md"
git remote add origin "https://github.com/99fu/99fu.github.io.git"
git push -u origin master
```
#### 在新的仓库中创建分支
```
在本地创建新的分支 git branch newbranch
切换到新的分支 git checkout newbranch
将新的分支推送到github git push origin newbranch
在本地删除一个分支： git branch -d newbranch
在github远程端删除一个分支： git push origin :newbranch (分支名前的冒号代表删除)
```
#### 直接使用git pull和git push的设置
```sh
git branch --set-upstream-to=origin/master master
git branch --set-upstream-to=origin/ThirdParty ThirdParty
git config --global push.default matching
```
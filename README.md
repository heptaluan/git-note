## 安装 / 初始化

放在最前面，使用 ```git help log / add / commit...``` 可以查看相关命令的操作方式

----

安装完成后首先需要设置用户和地址

```js
git config --global user.name "name"

git congig --global user.email "email"
```

然后新建一个文件夹用作 git 仓库，再初始化这个 git 仓库

```js
git init  // 初始化

pwd  // 显示当前目录

ls   // 显示当前目录文件
``` 

然后添加文件到 git 仓库

```js
git add 文件名  // 添加文件，可以反复添加多次

git commit -m "描述"  // 提交文件到仓库
```


#### git add 的各种区别:

```js

git add -A   // 添加所有改动

git add *     // 添加新建文件和修改，但是不包括删除

git add .    // 添加新建文件和修改，但是不包括删除

git add -u   // 添加修改和删除，但是不包括新建文件


// 在 commit 前撤销 add:

git reset <file> // 撤销提交单独文件

git reset        // unstage all due changes

// add / commit 前撤销对文件的修改:

git checkout -- README.md  // 注意, add添加后(同 commit 提交后)就无法通过这种方式撤销修改

```




## 查看工作区状态

使用

```js
git status  // 查看工作区状态
```

如果出现如下警告：

```js
warning: CRLF will be replaced by LF in readme.txt.
The file will have its original line endings in your working directory.
```

可以使用：

```js
git config core.autocrlf false
```

至于出现原因，详细见：[git如何避免”warning: LF will be replaced by CRLF“提示？](https://www.zhihu.com/question/50862500)

提交前的时候可以查看具体修改了哪些内容，使用：

```
git diff  // 工作区 和 暂存区 的比较

git diff --cached  // 暂存区 和 分支 的比较

// 还有下面这个
// HEAD 参数是可变的，也就是可以将工作区的内容与版本库里指定的任意版本比较
// 比如 git diff 345et6 --<file>
git diff HEAD --<file>  
```


## 版本回退

可以使用 git log 命令来查看历史记录

```js
git log
```

如果感觉输出信息太多，可以使用 --pretty=oneline 参数（使其一行显示）

```js
git log --pretty=oneline
```

回退到上一个版本，使用 reset 命令

```js
git reset --hard HEAD^
```

如果回退到前两个版本，可以使用 HEAD^^ 或者 Head~2，依次类推

```js
git reset --hard Head^^

git reset --hard HEAD~2
```

如果回退版本后，在使用 git log 后就查看不到之前最新版本的信息了

这个时候可以使用下面命令指定回到之前某个版本（4cda21 这个就是之前使用 git log 查看的版本号，只需要前面几位即可，git 会去自动查找）

```js
git reset --hard 4cda21
```

但是如果找不到之前的版本号，可以使用下面命令来查看提交过的每一次命令

```js
git reflog
```

小小总结：

* 回退　/ 更新 到指定的版本，使用 ```git reset --hard commit_id```

* 回退前，使用 ```git log``` 查看提交历史，确定回退版本

* 向跳转到指定版本，使用 ```git reflog``` 查看历史提交记录，确定跳转版本



## 工作区和暂存区

简单来说，就是先提交修改的文件到暂存区，然后再**一次性**提交暂存区的所有修改

```add``` 的作用就是把工作区的更新到暂存区，而 ```commit``` 则是把缓冲区更新到仓库

把文件往 git 仓库中添加的时候，是分两步执行的：

1. 首先使用 ```git add``` 把文件添加进去，实际上就是把文件修改添加到暂存区

2. 然后使用 ```git commit``` 提交修改，实际上就是把暂存区的所有文件提交到当前分支

因为我们在创建 git 版本库的时候，git 自动为我们创建了唯一一个 master 分支，所以，```git commit``` 就是往 ```master``` 分支上提交更改

需要注意的是：如果修改文件后，没有使用 git add 将修改放入暂存区，那么在 commit 的时候只会把暂存区的修改提交

简单总结就是：每次修改不 add 到暂存区，就不会加入到 commit 中



## 撤销修改

主要使用下面这两个命令

```js
// 撤销对工作区修改
// 这个命令是以最新的存储时间节点（add 和 commit）为参照，覆盖工作区对应文件 file
// 这个命令改变的是工作区
git checkout -- file

// 清空 add 命令向暂存区提交的关于 file 文件的修改（Ustage）
// 这个命令仅改变暂存区，并不改变工作区，这意味着在无任何其他操作的情况下，工作区中的实际文件同该命令运行之前无任何变化
git reset HEAD -- file
```

在没有推送到远程仓库之前的撤销主要分为以下几种：

* 修改了文件，想直接撤销（这个时候还没有 add），使用 ```git checkout -- file```

* 修改了文件，但是又 add 到了暂存区，这个时候想放弃修改，可以使用 ```git reset HEAD file``` 可以把暂存区的修改撤销掉

* 如果已经 commit，想要撤销修改，这个时候就可以使用版本回退（git reset --hard HEAD^）

一个额外的需求，希望彻底的舍弃本地未 push 的 commit

因为通过像 git reset 这样的操作，如果 push 了远程仓库是看不到被你舍弃的 commit

但是在本地还是可以通过 reflog 来找到，解决办法就是：

这个时候就需要先 reset，然后 push，再去把本地的库删掉，重新 clone 即可
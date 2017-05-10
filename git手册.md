# git教程

## git简介

git是一个开源的分布式版本控制系统，用于敏捷高效的处理任何大小的项目。

## git工作流程相关概念

首先要理解git是如何工作的，我们先要理解git工作区，暂存区，版本库的概念。

**工作目录：**就是你在电脑里当前工作的环境目录。

**暂存区域：**英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

**git仓库：**工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

## git工作流程

1.在工作目录中修改文件。

2.暂存文件，将文件的快照放入暂存区域。

3.提交更新，找到暂存区域的文件，将快照永久性存储到git仓库目录。

这里针对的git操作分别为：git add fileName 或者git add *，这是git工作的第一步,将工作目录中的变化**添加**到缓存区，相当于形成下一次将要提交的快照。

然后通过git  commit -m "cms框架搭建"完成第二步，将文件的快照提交到暂存区域，也就是实际提交改动，此时你的结果已经提交到head，指向你最后一次提交的结果。

最后通过git push  origin cms将暂存区的文件，更新到远程的git仓库中。

## git基本操作

### 1.查看远程分支

~~~
git branch -a
~~~

### 2.创建分支

~~~\
git branch  branchName.比如：创建cms分支，git branch cms
~~~

### 3.删除分支（本地）

~~~
git branch -d  branchName.比如:删除cms分支，git branch -d cms
~~~

### 4.删除分支（远程）

~~~
git push origin --delete branchName.比如：删除远程cms分支, git push origin --delete branchName.
~~~

### 5.切换分支

~~~
git checkout branchName
~~~

### 6.合并分支到当前分支

~~~
git merge  branchName，比如把cms分支合并到当前分支（加入为agent），那么一定要注意更新本地分支。
~~~

### 7.回滚日志，回退

~~~
git reset --hard commit_id ，注意：git reset --hard是回退到当前最新版本号。
~~~

### 8.根据版本号创建tag

~~~
git tag -a  tag name   versionid  -m  comments
~~~

### 9.提交tag到远程库

~~~
git push origin --tag
~~~

### 10.获取远程tag

~~~
git fetch origin tag  tagname 
~~~

### 11.删除远程tag

~~~
git push origin --delete tag   tagname
~~~

### 12.查看日志

~~~
git log
~~~

### 13.暂存当前工作

多人协作时，当你正在agent分支上编辑某文件A，但你需要切换到另一个分支上去，此时你本地正在编辑的文件又不想编辑，这个时候，如果是git checkout cms是无法实现分支切换的。这时，你需要**git stash**来暂存当前正在进行的工作。然后切换到cms分支修改bug，但是记得你在cms分支修改完bug之后，切换到agent分支时，一定要记得**git stash pop**，取消暂存的区域，然后才能接着编辑文件A。

### 14.放弃当前修改

`git fetch origin`

`git reset --hard origin/master`

## 创建代码仓库

git init  创建一个新的git仓库，它用来将已存在但还没有版本控制的项目转换成一个git仓库，或者创建一个新的git仓库。执行了这个命令之后，会在当前的目录下增加一个.git文件夹，这就是你的git仓库，才开始记录项目版本情况了。

git clone  giturl  克隆项目。

## git高级—子项目操作

### 1.使用场景

基于公司的项目会越来越多，常常需要提取一个公共的类库提供给多个项目使用，但是这个`library`怎么和`git`在一起方便管理呢？
我们需要解决下面几个问题：

- 如何在git项目中导入`library库`?
- `library库`在其他的项目中被修改了可以更新到远程的代码库中?
- 其他项目如何获取到`library库`最新的提交?
- 如何在clone的时候能够自动导入`library库`?

解决以上问题，可以考虑使用git的 `Submodule`来解决。

### 2.什么是Submodule？

`git Submodule` 是一个很好的多项目使用共同类库的工具，他允许类库项目做为`repository`,子项目做为一个单独的`git项目`存在父项目中，子项目可以有自己的独立的`commit`，`push`，`pull`。而父项目以`Submodule`的形式包含子项目，父项目可以指定子项目`header`，父项目中会的提交信息包含`Submodule`的信息，再`clone父项目`的时候可以把`Submodule`初始化。

### 3.在项目中使用Submodule

使用`git`命令可以直接添加`Submodule`：

~~~
git submodule add <repo> <destpath>
~~~

~~~java
git submodule add git@github.com:jjz/pod-library.git pod-library
~~~

使用 `git status`命令可以看到

~~~java
git status
~~~

~~~java
 On branch master
    Changes to be committed:
    
        new file:   .gitmodules
        new file:   pod-library
~~~

可以看到多了两个需要提交的文件：`.gitmodules`和 `pod-library` 
`.gitmodules` 内容包含`Submodule`的主要信息，指定`reposirory`,指定路径:

~~~java
 [submodule "pod-library"]
        path = pod-library
        url = git@github.com:jjz/pod-library.git
~~~

可以看到记录了子项目的目录和子项目的`git`地址信息。
`pod-libray`内容只保护子项目的`commit id`，就能指定到对于的`git header`上,例如:

~~~java
Subproject commit 4ac42d2f8b9ba0c2f0f2f2ec87ddbd529275fea5
~~~

`4ac42d2f8b9ba0c2f0f2f2ec87ddbd529275fea5`就是子项目的`commit id`，父项目的git并不会记录`Submodule`的文件变动，它是按照`commit git`指定`Submodule`的`git header`。

另外,*这两个文件都需要提交到父项目的git中*。

还可以这样使用命令添加`Submodule`

~~~
git add .gitmodules pod-ibrary
git commit -m "pod-library submodule"
git submodule init
~~~

### 4.修改Submodule

**首先需要确认有对Submodule的commit权限**。
进入`Submodule`目录里面:

~~~java
cd pod-library/
~~~

修改其中的一个文件看下文件的可以用`git status`查看变动:

~~~java
git status
~~~

```java
modified:   pod-library/UseAFHTTP.h
```

提交`Submodule`的更改内容：

~~~java
git commit -a -m'test submodule'
~~~

然后`push` 到远程服务器:

~~~
git push
~~~

然后再回到父目录,提交`Submodule`在父项目中的变动：

~~~java
cd ..
~~~

然后看一下父目录有啥变化：

~~~java
git status
~~~

~~~
modified: pod-library (new commits)
~~~

可以看到`pod-library`中已经变更为`Submodule`最新的`commit id`:

~~~java
Subproject commit 330417cf3fc1d2c42092b20506b0d296d90d0b5f
~~~

需要把`Submodule`的变动信息推送到父项目的远程服务器:

~~~
git commit -m'update submodule'
git push
~~~

这样就把子模块的变更信息以及子模块的变更信息提交到远程服务器了，从远程服务器上更新下来的内容就是最新提交的内容了。

总之一句话，提交的时候先提交到子项目，然后再提交到父项目中。

### 5.更新Submodule

有两种方式：一种是父目录下直接更新，一种是先更新子目录，再更新父目录。

* 父目录直接更新

  ~~~java
  git submodule foreach git pull
  ~~~

* 子目录下更新

  ~~~java
  >cd pod-library
  git pull
  >cd ..
  git pull
  ~~~

  可以看到在`Submodule`的目录中,使用`git`和单独的一个项目是一样的,注意更新`Submodule`的时候如果有新的`commit id`产生，需要在父项目产生一个新的提交，pod-libray文件中的 `Subproject commit`会变为最新的`commit id`。

### 6.clone Submodule

`clone Submodule`有两种方式 一种是采用递归的方式clone整个项目，一种是clone父项目，再更新子项目。

* 采用递归参数 `--recursive`

  ~~~java
  git clone git@github.com:jjz/pod-project.git --recursive
  ~~~

输出结果：

~~~java
 loning into 'pod-project'...
    remote: Counting objects: 57, done.
    remote: Compressing objects: 100% (45/45), done.
    remote: Total 57 (delta 13), reused 49 (delta 8), pack-reused 0
    Receiving objects: 100% (57/57), 18.79 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (13/13), done.
    Checking connectivity... done.
    Submodule 'pod-library' (git@github.com:jjz/pod-library.git) registered for path 'pod-library'
    Cloning into 'pod-library'...
    remote: Counting objects: 34, done.
    remote: Compressing objects: 100% (25/25), done.
    remote: Total 34 (delta 8), reused 30 (delta 7), pack-reused 0
    Receiving objects: 100% (34/34), 12.95 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (8/8), done.
    Checking connectivity... done.
    Submodule path 'pod-library': checked out '330417cf3fc1d2c
~~~

可以看到`init Submodule` 会自动被`clone`下来。

* 先clone父项目，再初始化子项目submodule

  ~~~
  git clone git@github.com:jjz/pod-project.gitcd pod-project
  ~~~

  初始化submodule

  ~~~java
  git submodule init
  ~~~

  更新submodule

  ~~~
  git submodule update
  ~~~

  运行结果：

  ~~~java
   Cloning into 'pod-library'...
    remote: Counting objects: 34, done.
    remote: Compressing objects: 100% (25/25), done.
    remote: Total 34 (delta 8), reused 30 (delta 7), pack-reused 0
    Receiving objects: 100% (34/34), 12.95 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (8/8), done.
    Checking connectivity... done.
    Submodule path 'pod-library': checked out '330417cf3fc1d2c42092b20506b0d296d90d0b5f'
  ~~~

  ### 7.删除Submodule

  `git` 并不支持直接删除`Submodule`,而是需要手动删除对应的文件:

  ~~~java
      cd pod-project

      git rm --cached pod-library
      rm -rf pod-library
      rm .gitmodules

      更改git的配置文件config:
      vim .git/config
  ~~~

  可以看到`Submodule`的配置信息：

  ~~~java
  [submodule "pod-library"]
    url = git@github.com:jjz/pod-library.git
  ~~~

  删除submodule相关的内容,然后提交到远程服务器:

  ~~~
  git commit -a -m 'remove pod-library submodule'
  ~~~

  ​






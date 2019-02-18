
本文演示如何在本地创建仓库和分支仓库，然后上传到GitHub,环境假设如下：

演示系统：ubuntu 16.04   
本地仓库：/home/git/vank3f3
远端仓库地址：https://github.com/vank3f3/base.git

本文主要内容


1. 创建一个GitHub仓库
2. 创建一个本地仓库
3. 推送本地仓库内容到GitHub仓库
4. 本地增加一个分支，推送到GitHub
5. 在另外一台电脑克隆仓库进行分支操作修改

### 一、创建一个GitHub仓库
登录GitHub创建一个base仓库，见下图，这里就不详细描述了，注意不勾选初始化生成README,防止冲突
![在GitHub创建一个base 仓库](https://raw.githubusercontent.com/vank3f3/base/master/images/1.png)

### 二、创建一个本地仓库

#### 2.1 初始化本地仓库
```
cd /home/git/vank3f3 &&  git clone https://github.com/vank3f3/base.git  && cd ./base &&  mkdir ./images 
```

#### 2.2 创建一个Dockerfile文件
```
echo '
FROM alpine:3.5
MAINTAINER vank3f3
LABEL "vendor"="vank3f3"  "author"="vank3f3"
'> Dockerfile
```

#### 2.3 根据Dockerfile生成镜像
输出如下信息 

```
root@ubuntu:/home/git/vank3f3/base # docker build -t vank3f3/base:alpine3.5 .

Sending build context to Docker daemon  76.29kB
Step 1/3 : FROM alpine:3.5
3.5: Pulling from library/alpine
8cae0e1ac61c: Pull complete 
Digest: sha256:66952b313e51c3bd1987d7c4ddf5dba9bc0fb6e524eed2448fa660246b3e76ec
Status: Downloaded newer image for alpine:3.5
 ---> f80194ae2e0c
Step 2/3 : MAINTAINER vank3f3
 ---> Running in 6ea69316e73f
Removing intermediate container 6ea69316e73f
 ---> 423a5e876acd
Step 3/3 : LABEL "vendor"="vank3f3"  "author"="vank3f3"
 ---> Running in 59ec584b80c3
Removing intermediate container 59ec584b80c3
 ---> 014aa9134f33
Successfully built 014aa9134f33
Successfully tagged vank3f3/base:alpine3.5

```

查看生成的镜像


```
root@ubuntu:/home/git/vank3f3/base # docker images vank3f3/base
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
vank3f3/base        alpine3.5           014aa9134f33        54 seconds ago      4MB
```

运行容器，查看版本号
```bash
root@ubuntu:/home/git/vank3f3/base # docker run --rm vank3f3/base:alpine3.5 cat /etc/alpine-release
3.5.3
```

### 三、同步本地仓库到远端GitHub

查看当前状态
```
root@ubuntu:/home/git/vank3f3/base # git status
位于分支 master

初始提交

未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）
  （使用 "git add ./"  提交当前目录下的所有文件）


	Dockerfile
	README.md
	images/



提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）

```
向暂存区中添加文件然后重新查看状态
```
root@ubuntu:/home/git/vank3f3/base # git add --all
root@ubuntu:/home/git/vank3f3/base # git status
位于分支 master

初始提交

要提交的变更：
  （使用 "git rm --cached <文件>..." 以取消暂存）

	新文件夹:     images/
	新文件:       Dockerfile
	新文件:       README.md

root@ubuntu:/home/git/vank3f3/base # 
```
根提交
```
root@ubuntu:/home/git/vank3f3/base # git commit -a -m "first commit"
 
[master (root-commit) 1249a59] first commit
3 files changed, 308 insertions(+)
create mode 100644 Dockerfile
create mode 100644 README.md
create mode 100644 images/1.png

```
再次查看状态，可以看到一个干净的工作区
```
root@ubuntu:/home/git/vank3f3/base # git status
On branch master
Your branch is based on 'origin/master', but the upstream is gone.
  (use "git branch --unset-upstream" to fixup)
nothing to commit, working directory clean

```
配置上传到远端GitHub仓库,

```
git config --global user.email "vank3f3@xxxx.com"
git config --global user.name "vank3f3"
cat ~/.gitconfig 
```
添加origin标识符为远端仓库名称
```
root@ubuntu:/home/git/vank3f3/base # git remote add origin https://github.com/vank3f3/base.git
root@ubuntu:/home/git/vank3f3/base # cat .git/config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
[remote "origin"]
        url = https://github.com/vank3f3/base.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master

```
推送到远端GitHub
```
git push -u origin master
Username for 'https://github.com': 			----> 输入用户名
Password for 'https://vank3f3@github.com':  ----> 输入密码
Counting objects: 9, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (9/9), 33.81 KiB | 0 bytes/s, done.
Total 9 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To https://github.com/vank3f3/base.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```
可以看到上面把本地的master分支上传到远端的master分支


### 四、新建一个ubuntu16.04分支

#### 4.1 查看本地分支情况

```
root@ubuntu:/home/git/vank3f3/base # git branch
* master
```
#### 4.2 新建一个本地分支ubuntu16.04

```
root@ubuntu:/home/git/vank3f3/base # git checkout -b ubuntu16.04
切换到一个新分支 'ubuntu16.04'
root@ubuntu:/home/git/vank3f3/base # git branch
  master
* ubuntu16.04
root@ubuntu:/home/git/vank3f3/base #
```

#### 4.3 然后修改分支内容


```

echo '
FROM ubuntu:16.04
MAINTAINER vank3f3
LABEL "vendor"="vank3f3"  "author"="vank3f3"
'> Dockerfile

```
#### 4.4 验证dockerfile是否正确
```
root@ubuntu:/home/git/vank3f3/base # docker build -t vank3f3/base:ubuntu16.04 .
Sending build context to Docker daemon  155.1kB
Step 1/3 : FROM ubuntu:16.04
16.04: Pulling from library/ubuntu
7b722c1070cd: Pull complete 
5fbf74db61f1: Pull complete 
ed41cb72e5c9: Pull complete 
7ea47a67709e: Pull complete 
Digest: sha256:e4a134999bea4abb4a27bc437e6118fdddfb172e1b9d683129b74d254af51675
Status: Downloaded newer image for ubuntu:16.04
 ---> 7e87e2b3bf7a
Step 2/3 : MAINTAINER vank3f3
 ---> Running in c4f587d05616
Removing intermediate container c4f587d05616
 ---> b933aadfc44b
Step 3/3 : LABEL "vendor"="vank3f3"  "author"="vank3f3"
 ---> Running in 994b602b275a
Removing intermediate container 994b602b275a
 ---> 3e65a79c3663
Successfully built 3e65a79c3663
Successfully tagged vank3f3/base:ubuntu16.04

```


#### 4.4 添加分支内容
```
root@ubuntu:/home/git/vank3f3/base # git status
On branch ubuntu16.04
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   Dockerfile
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")

root@ubuntu-xenial:/vagrant/git/vank3f3/base# git add ./
root@ubuntu-xenial:/vagrant/git/vank3f3/base# git status
On branch ubuntu16.04
Your branch is up-to-date with 'origin/ubuntu16.04'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   Dockerfile
        modified:   README.md

```
#### 4.5 推送到远端GitHub ubuntu16.04分支下

```
root@ubuntu:/home/git/vank3f3/base # git push -u origin ubuntu16.04
Username for 'https://github.com': <--输入用户名
Password for 'https://vank3f3@github.com': <--输入密码
Total 0 (delta 0), reused 0 (delta 0)
remote: 
remote: Create a pull request for 'ubuntu16.04' on GitHub by visiting:
remote:      https://github.com/vank3f3/base/pull/new/ubuntu16.04
remote: 
To https://github.com/vank3f3/base.git
 * [new branch]      ubuntu16.04 -> ubuntu16.04
Branch ubuntu16.04 set up to track remote branch ubuntu16.04 from origin.
```


#### 4.6 检验一下远端是否有分支

![github branch](https://raw.githubusercontent.com/vank3f3/base/master/images/2.png)


### 五、在另外一台电脑上克隆仓库和获取最新的远程分支

下面是操作命令，我就不把输入列出了
```
git clone https://github.com/vank3f3/base.git
git branch -a
git checkout -b ubuntu16.04 origin/ubuntu16.04
```

修改分支内容然后提交更改
```
vi somefile
git diff
git add --all
git commit -am "Add change from second computer"
git pull  
```

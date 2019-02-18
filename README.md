
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

关联GitHub
```
root@ubuntu:/home/git/vank3f3/base # git config --global user.email vank3f3@XXXXX.com
root@ubuntu:/home/git/vank3f3/base # git config --global user.name vank3f3
```

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
位于分支 master
无文件要提交，干净的工作区
```
配置上传到远端GitHub仓库,

```
git config --global user.email "lihaixin@15099.net"
git config --global user.name "lihaixin"
cat ~/.gitconfig 
[user]
	email = lihaixin@15099.net
	name = lihaixin
[http]
	proxy = http://192.168.3.106:8123
[https]
	proxy = https://192.168.3.106:8123
[color]
	ui = auto
```
添加origin标识符为远端仓库名称
```
root@ubuntu:/home/git/vank3f3/base #git remote add origin https://github.com/lihaixin/base.git
root@ubuntu:/home/git/vank3f3/base # cat .git/config 
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = https://github.com/lihaixin/base.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```
推送到远端GitHub
```
git push -u origin master
Username for 'https://github.com': <--输入用户名
Password for 'https://lihaixin@github.com': <--输入密码
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (5/5), 435 bytes | 0 bytes/s, done.
Total 5 (delta 0), reused 0 (delta 0)
To https://github.com/lihaixin/base.git
 * [new branch]      master -> master
分支 master 设置为跟踪来自 origin 的远程分支 master。
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
 # vi Dockerfile 
#
# Dockerfile for base:ubuntu16.04
#

FROM ubuntu:16.04
MAINTAINER Lee <noreply@lihaixin.name>
LABEL "vendor"="lihaixin.name" \
      "author"="Haixin Lee"
```
#### 4.4 验证dockerfile是否正确
```
root@ubuntu:/home/git/vank3f3/base # docker build -t lihaixin/base:ubuntu16.04 .
Sending build context to Docker daemon  5.632kB
Step 1/3 : FROM ubuntu:16.04
16.04: Pulling from library/ubuntu
aafe6b5e13de: Already exists 
0a2b43a72660: Already exists 
18bdd1e546d2: Already exists 
8198342c3e05: Already exists 
f56970a44fd4: Already exists 
Digest: sha256:f3a61450ae43896c4332bda5e78b453f4a93179045f20c8181043b26b5e79028
Status: Downloaded newer image for ubuntu:16.04
 ---> f7b3f317ec73
Step 2/3 : MAINTAINER Lee <noreply@lihaixin.name>
 ---> Running in 3deb0b22fd40
 ---> 552067c91f69
Removing intermediate container 3deb0b22fd40
Step 3/3 : LABEL "vendor" "lihaixin.name" "author" "Haixin Lee"
 ---> Running in d10456510147
 ---> 50451e045f77
Removing intermediate container d10456510147
Successfully built 50451e045f77
Successfully tagged lihaixin/base:ubuntu16.04
root@ubuntu:/home/git/vank3f3/base # 
```


#### 4.4 添加分支内容
```
root@ubuntu:/home/git/vank3f3/base # git status
位于分支 ubuntu16.04
要提交的变更：
  （使用 "git reset HEAD <文件>..." 以取消暂存）

	修改:         Dockerfile

root@ubuntu:/home/git/vank3f3/base # git commit -a -m "Change to ubuntu:16.04 base"
[ubuntu16.04 e81e626] Change to ubuntu:16.04 base
 1 file changed, 2 insertions(+), 2 deletions(-)
root@ubuntu:/home/git/vank3f3/base # git status
位于分支 ubuntu16.04
无文件要提交，干净的工作区
root@ubuntu:/home/git/vank3f3/base # 
```
#### 4.5 推送到远端GitHub ubuntu16.04分支下

```
root@ubuntu:/home/git/vank3f3/base # git push -u origin ubuntu16.04
Username for 'https://github.com': <--输入用户名
Password for 'https://lihaixin@github.com': <--输入密码
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 387 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/lihaixin/base.git
 * [new branch]      ubuntu16.04 -> ubuntu16.04
分支 ubuntu16.04 设置为跟踪来自 origin 的远程分支 ubuntu16.04。
```


#### 4.6 检验一下远端是否有分支

![github branch](https://raw.githubusercontent.com/lihaixin/base/master/images/1.png)


### 五、在另外一台电脑上克隆仓库和获取最新的远程分支

下面是操作命令，我就不把输入列出了
```
git clone https://github.com/lihaixin/base.git
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

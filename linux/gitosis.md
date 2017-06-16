# Git 服务器 Gitosis 安装设置
1.服务器上安装ssh支持
```
sudo apt-get install openssh-server openssh-client
```
2、创建个人公钥和私钥

在默认用户的主目录路径下，运行以下命令，按照提示创建公钥和私钥
```
ssh-keygen -t rsa
```
默认生成2048位，如果需要提高安全级别，也可以通过下面的命令创建公钥和私钥
```
ssh-keygen -t rsa -b 4096
```

3、安装 git服务器
```
sudo apt-get install git-core
```

4.创建git服务器管理用户
```
sudo useradd -m git // 创建git用户并创建/home/git主目录
sudo passwd git
```
5.安装python的setup tool
```
sudo apt-get install python-setuptools
```
6.获取并安装gitosis
```
cd /tmp
git clone https://github.com/tv42/gitosis.git
cd gitosis
sudo python setup.py install
```
7.初始化gitosis
```
sudo -H -u git gitosis-init < /tmp/id_rsa.pub // 准备一枚管理员公钥 id_rsa.pub，用他来管理所有项目
```
初始化之后 git home 目录包含如下目录：
```
gitosis/projects.list // 项目列表
.gitosis.conf -> /home/git/repositories/gitosis-admin.git/gitosis.conf // 配置文件
repositories/gitosis-admin.git // gitosis 仓库
```
8.加入权限
```
sudo chmod 755 /home/git/repositories/gitosis-admin.git/hooks/post-update
```
9.从客户端下载 gitosis.git 仓库进行配置（注意，客户端必须有管理员公钥 id_rsa.pub）
```
git clone git@192.168.1.130:gitosis-admin.git
cd gitosis-admin/
```
文件中的目录如下：
```
gitosis.conf  // 配置文件
keydir/       // 存放其他成员公钥的目录，公钥格式大概为 aa@bb.pub bb@cc.pub
```
10.修改gitosis.conf文件，如下所示，实现不同的成员管理不同的项目
```
[gitosis]
[group gitosis-admin]
writable = gitosis-admin
members = aa@bb.pub
[group developers]
writable = helloworld
members = aa@bb.pub bb@cc.pub
[group test] 
readonly = helloworld
members = bb@cc.pub
```
11.创建新的项目 demo
```
su git
cd ~/repositories/     // 项目和 gitosis-admin.git 放在同一个位置
mkdir demo.git
cd demo.git/
git init --bare
```
12.然后配置好 gitosis.conf 增加一个项目配置，并指定可以参与的人员公钥
```
[group demo] // 名字可以随便取
writable = demo // 这里的demo指 demo.git 项目，如果指定为 readonly 则表示此项目只读模式
members = bb@cc.pub
```
修改之后，管理员提交 git push

13.其他有权限的成员下载 demo 项目
```
git clone git@192.168.1.130:demo.git
```
然后就可以修改和提交了
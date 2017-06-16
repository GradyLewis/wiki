# 通过源码包安装 git 版本

首先安装依赖：
```bash
sudo yum install curl-devel expat-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker # gettext-devel 
```
下载并安装：
```bash
cd /usr/src
wget https://www.kernel.org/pub/software/scm/git/git-2.3.1.tar.gz
tar -xzf git-2.3.1.tar.gz
cd git-2.3.1
sudo make prefix=/usr/local/git all
sudo make prefix=/usr/local/git install
```
将下面的内容加入 /etc/bashrc 底部：
```bash
export PATH=$PATH:/usr/local/git/bin
```
在控制台引入文件，或者重启控制台：
```bash
source /etc/bashrc
```
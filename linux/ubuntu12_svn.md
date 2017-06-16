# Ubuntu 12 SVN安装手册
安装命令
```
sudo apt-get install subversion
```
创建项目目录
```
sudo mkdir /home/svn
cd /home/svn/project
sudo chmod -R 777 project/
```
创建svn仓库
```
sudo svnadmin create /home/svn/project
```
执行命令后生成以下文件：
```
conf   db   format   hooks   locks   README.txt
```
设置访问权限

1. 进入config/svnserve.conf，配置以下两项：
```
anon-access = none # 游客的权限（none, read, write）
auth-access = write # 账户的权限（none, read, write）
password-db = passwd # 使用的相同目录下的用户/密码文件
```
2. 进入config/password，添加账户和密码
```
[users]
user1 = 123456
user2 = 123456
```
3. 进入config/authz，设置访问权限
```
[groups]
admin = user1,user2 # admin组下有两个账号
@admin = rw # 给admin组分配读写权限
```
启动svn服务器
```
svnserve -d -r /home/svn --listen-port 81 # 默认端口3690，因此需要防火墙开启此端口
```
```
-d 表示svnserver以“守护”进程模式运行
-r 指定文件系统的根位置，这样客户端不用输入全路径，就可以访问版本库
```
如：svn://192.168.1.3/project

注意：对配置文件的修改立即生效，不需要重启svn服务器
导入项目到svn服务器

在window平台安装svn客户端TortoiseSVN,使用TortoiseSVN中import功能
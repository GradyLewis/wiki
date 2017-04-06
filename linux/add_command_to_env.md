# 将命令加入系统环境变量的几种方式
以安装在 /opt 下的 git 为例，通过两种方式：
## 普通用户
### vim /etc/environment

```ruby
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/src/jdk1.8.0_111/bin:/opt/git/bin"
```

### touch /etc/profile.d/git.sh
```ruby
# git.sh
GIT_HOME=/opt/git
export PATH=$PATH:$GIT_HOME/bin
```
以上命令使所有普通用户可以自动载入git环境目录。
## ROOT
此时使用如下命令可以让 sudo 使用相关的环境变量
```ruby
sudo -E su
```
或者
### vim /etc/sudoers
```ruby
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/git/bin"
```
保存后使用如下命令也可以生效
```ruby
sudo -s
```
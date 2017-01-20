# Web中不使用密码执行sudo命令
## sudoers
```ruby
# 授权运行 Web 的用户不使用密码通过 sudo 执行某 bash 命令
# sudo visudo -f /etc/sudoers.d/www
www      ALL=(ALL)  NOPASSWD:ALL
www      ALL=(ALL)  NOPASSWD:/bin/ps,/usr/bin/wget
```
## 关闭默认只能通过终端执行 sudo
```ruby
# sudo vim /etc/sudoers
# 将以下设置注释掉
# Defaults requiretty
```
# MYSQL远程登录权限设置
## mysqld.conf 配置
```ruby
bind-address = 0.0.0.0
```
重启 mysql 服务
## mysql console 配置
```ruby
use mysql;
# 从任意外部访问主机，用户名和密码为 root / root
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
# 指定某台主机访问
GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.0.1' IDENTIFIED BY 'root' WITH GRANT OPTION;
# 使规则生效
FLUSH PRIVILEGES;

# 删除授权
use mysql;
REVOKE ALL PRIVILEGES ON *.* FROM 'root'@'%';
delete from user where host = '%' and user = 'root';
FLUSH PRIVILEGES;
```
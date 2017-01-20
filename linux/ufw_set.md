# UFW防火墙基础配置
```ruby
> sudo -s

# 启用防火墙
> ufw enable

# 查询状态
> ufw status

# 开放端口供任何外部访问
> ufw allow 3000
To              Action        From
--              ------        ----
3000            ALLOW         Anywhere

# 禁用任何外部访问
> ufw deny 3001
To              Action        From
--              ------        ----
3001            DENY         Anywhere

# 允许/禁用某个IP访问某端口
> ufw allow from 192.168.0.100 to any port 100
> ufw deny from 192.168.0.101 to any port 101
To              Action        From
--              ------        ----
100             ALLOW         192.168.0.100             
101             DENY          192.168.0.101

# 在指定位置插入过滤规则
> ufw insert 1 deny from 192.168.0.102 to any port 102
Rule inserted
To              Action        From
--              ------        ----
102             DENY          192.168.0.102
...<其他规则>...

# 删除规则
> ufw delete deny from 192.168.0.102 to any port 102
> ufw delete allow from 192.168.0.100 to any port 100
Rule deleted
```
# RedisObject
1.首先Linux系统安装redis，以Ubuntu为例
```
sudo apt-get install redis-server
```
2.在项目中引入Gemfile
```
gem 'redis-objects'
```
3.在Object中引入
```
#encoding:utf-8
class PersonInfo < ActiveRecord::Base
  include Redis::Objects
end
```
4.使用
```
PersonInfo.redis.get('abc') # 获取key为abc的值，如果没有，则返回 nil
PersonInfo.redis.set('abc', '111') # 设置key为abc的值
PersonInfo.redis.setex('abc', 60, '111') # 设置key为abc的值，60s（1分钟）之后失效
```
# ERROR 2006 (HY000) at line xx: MySQL server has gone away 解决方法
这个一般发生在导入SQL表过大时，MYSQL失去响应

解决办法

找到my.ini文件, 添加一句
```bash
max_allowed_packet=16M
```
如果不行将16M再加大

加大 wait_timeout 也可起一定作用


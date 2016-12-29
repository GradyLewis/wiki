# 定时任务运行Rails Task
假设项目根目录为 `PROJECT_HOME`，Rails Task定义于`lib/tasks/task.rake`中。

1. 首先在`PROJECT_HOME/script`中创建`cron_job.sh`，内容如下：
```ruby
#!/bin/bash
# 载入rails运行环境
source /home/lewis/.rvm/gems/ruby-2.1.8@demo/environment
cd {PROJECT_HOME} && rake db:task RAILS_ENV=production
```
2. 修改`cron_job.sh`为可执行
```ruby
chmod +x cron_job.sh
```
3. 在系统Crontab中加入如下语句
```ruby
# sudo vim /etc/crontab
*/5 * * * * lewis {PROJECT_HOME}/script/cron_job.sh # 五分钟执行一次
```
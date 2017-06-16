# 通过rsync进行远程文件夹同步
创建并进入指定文件夹，然后运行如下命令：
```
mkdir -p /var/app/
cd /var/app/
rsync -zrtopg --delete root@192.168.0.1:/var/www/app/ ./
```
rsync 参数含义
<pre>
-z            传输过程中压缩文件
-r            必须加上次参数才能递归同步文件夹
-t            保留每个文件的创建和更新时间不变
-og        保留每个文件的属主(owner)和属组(group)
-p            保留文件的权限不变
-W          不进行文件增量验证，直接更新整个文件
--delete    精确同步，如果源主机上文件被删，目的主机上的文件也会被删除
--bwlimit  限制同步下载速度，单位 KB
--update 仅仅进行更新，跳过所有已经存在于DST，并且文件时间晚于要备份的文件。(不覆盖更新的文件)
</pre>
若不想输入密码，则在源主机的 authorized_keys 加入目标主机上的 pub 公钥

这样就可以在 crontab 上加入一条：
<pre>
0 0     * * *   root    cd /root/ && ./rsync_data.sh
</pre>
每晚12点运行同步脚本，同步数据

停止同步
<pre>ps aux</pre>
查看执行的进程 PID
<pre>kill PID</pre>

自动同步脚本

# 删除已经存在的同步进程
```
/bin/ps aux | /bin/grep 'rsync -zrtopg' | /usr/bin/awk '{print $2}' | xargs /bin/kill
```
# 以 512KB 的速度同步文件夹
```
cd /var/app/ && /usr/bin/rsync -zrtopg --delete --bwlimit=512 root@192.168.0.1:/var/www/app/ ./ &
```
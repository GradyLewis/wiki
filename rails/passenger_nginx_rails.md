# Ubuntu 14.04 LTS 下 Passenger + Nginx + Rails 部署手册
1.系统准备
```
sudo apt-get update
sudo apt-get install -y curl gnupg build-essential
sudo apt-get install -y nodejs
sudo ln -sf /usr/bin/nodejs /usr/local/bin/node
```
2.安装 Passenger + Nginx 组件
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
sudo apt-get install -y apt-transport-https ca-certificates
sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger trusty main > /etc/apt/sources.list.d/passenger.list'
sudo apt-get update
sudo apt-get install -y nginx-extras passenger
```
3.取消 /etc/nginx/nginx.conf 种下面两行的注释
```
# passenger_root /some-filename/locations.ini;
# passenger_ruby /usr/bin/passenger_free_ruby;
```
passenger_root 如果不存在，可以用命令 passenger-config --root 获取，然后加入里面。

4.重启 nginx 服务，服务器已经可以访问了
```
sudo service nginx restart
```
5.更新系统
```
sudo apt-get update
sudo apt-get upgrade
```
6.增加 Rails 项目配置文件
```
sudo vim /etc/nginx/sites-enabled/myapp.conf # 记得删除 sites-enabled 文件夹下面的 default 文件。
server {
    listen 80;
    server_name yourserver.com;

    # Tell Nginx and Passenger where your app's 'public' directory is
    root /var/www/myapp/code/public;

    # Turn on Passenger
    passenger_enabled on;
    passenger_ruby /path-to-ruby; # 可以用 passenger-config about ruby-command 命令获得 passenger_ruby 的位置
}
```
7.重启 nginx 服务，服务器已经可以访问了
```
sudo service nginx restart
```

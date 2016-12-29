# DEB包使用本地依赖包安装
```ruby
sudo -s
apt-get -y install aptitude # 安装用于创建离线deb包的工具 aptitude
 
aptitude clean # 清空临时下载目录
aptitude --download-only -y install ruby1.9.3 libruby1.8 curl libtimedate-perl # 将 ruby1.9.3 及其所有依赖包下载到临时目录
mkdir -p /opt/local_deb/ruby1.9.3
cp /var/cache/apt/archives/*.deb /opt/local_deb/ruby1.9.3/ # 拷贝下载的deb包到指定目录
 
aptitude clean # 清空临时下载目录
aptitude --download-only -y install ruby-gnome2 libglib2.0-dev libatk1.0-dev libcairo2-dev libpango1.0-dev libgdk-pixbuf2.0-dev libgtk2.0-dev # 将 ruby-gnome2 及其所有依赖包下载到临时目录
mkdir -p /opt/local_deb/ruby-gnome2
cp /var/cache/apt/archives/*.deb /opt/local_deb/ruby-gnome2/ # 拷贝下载的deb包到指定目录
 
# 使用 dpkg 命令安装所有deb包
dpkg -i /opt/local_deb/ruby-gnome2/*.deb
dpkg -i /opt/local_deb/ruby1.9.3/*.deb
```
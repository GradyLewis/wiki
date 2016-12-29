# Ubuntu配置JDK环境
首先解压从官网下载的 jdk-8u91-linux-x64.tar.gz 文件到指定目录
```ruby
sudo tar -xvf jdk-8u91-linux-x64.tar.gz
sudo chown -R root:root jdk1.8.0_91/
```
配置环境变量命令：
```ruby
sudo vim /etc/profile
```
添加以下代码：
```ruby
export JAVA_HOME=/usr/local/src/jdk1.8.0_91  
export JRE_HOME=$JAVA_HOME/jre  
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib  
export PATH=$JAVA_HOME/bin:$PATH
```
将Java添加到root sudo权限下
```ruby
sudo vim /etc/environment
```
增加下面的内容，PATH一般已经存在，则用冒号隔开增加
```ruby
PATH＝"/usr/local/src/jdk1.8.0_91/bin"
CLASSPATH="/usr/local/src/jdk1.8.0_91/lib"
JAVA_HOME="/usr/local/src/jdk1.8.0_91"
```
为了使系统默认使用的是我们安装的jdk，还需执行如下命令
```ruby
sudo update-alternatives --install /usr/bin/java java /usr/local/src/jdk1.8.0_91/bin/java 300
sudo update-alternatives --install /usr/bin/javac javac /usr/local/src/jdk1.8.0_91/bin/javac 300
sudo update-alternatives --config java
```
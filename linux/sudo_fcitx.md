# Rubymine中fcitx在sudo无法输入的问题
以 Root 运行的程序在一般用户的 X 会话下总是有问题的（一般意义上，并不只针对 fcitx），这是因为 dbus 是一个仅限用户会话的进程。
唯一在root程序中的办法使用 fcitx 的办法是通过 XIM，需要启动程序前设置:
<pre>
GTK_IM_MODULE=xim
QT_IM_MODULE=xim
</pre>

因此解决方法如下（对ibus也有效，把对应的输入法修改为ibus即可）:
##方法1
1.创建 /etc/sudoers_env 加入以下内容代码:
```ruby
GTK_IM_MODULE=xim
QT_IM_MODULE=xim
QT4_IM_MODULE=xim
```
2.执行 sudo visudo 加入以下内容代码:
```ruby
Defaults env_keep += "XMODIFIERS"
Defaults env_file="/etc/sudoers_env"
```
##方法2
```ruby
sudo XMODIFIERS=@im=fcitx GTK_IM_MODULE=xim ./rubymine.sh
```
##方法3
直接在 rubymine.sh 里增加环境变量
```ruby
export XMODIFIERS=@im=fcitx
export GTK_IM_MODULE=xim
```

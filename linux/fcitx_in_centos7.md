# CentOS7 安装fcitx输入法
1.安装源
```bash
sudo yum install epel-release
cd /etc/yum.repos.d/
sudo wget https://copr.fedoraproject.org/coprs/mosquito/myrepo/repo/epel-7/mosquito-myrepo-epel-7.repo
```
2.fcitx输入法列表
```bash
yum search fcitx
```
选择一个安装

3.环境支持

在某些应用里有不能使用中文的问题，需要安装一些支持
```bash
sudo yum install gtk2-immodule-xim gtk3-immodule-xim
```
4.输入法配置工具，用于配置输入法默认字体、皮肤等
```bash
sudo yum install fcitx-configtool
```
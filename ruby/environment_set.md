#Ruby的一些环境变量设置
## Ruby 根目录
```ruby
export RUBY_HOME=/opt/ruby
```
## Gems 安装目录
```ruby
export GEM_HOME=/opt/gems
```
修改后，Gems 不会安装到默认目录下

## 系统环境 PATH 目录
将 `RUBY_HOME` 和 `GEM_HOME` 环境的 `bin` 目录加入 `PATH` 目录，执行 `ruby` 或 `bundle`、`rails`、`thin` 等命令需要这两个位置。
```ruby
export PATH=$PATH:$RUBY_HOME/bin:$GEM_HOME/bin
```
一般第三方 Gems 安装的命令如 bundle、rails、thin 等命令都位于 `$GEM_HOME/bin` 中

## Gems 查询目录
```ruby
export GEM_PATH=$RUBY_HOME/lib/ruby/gems/2.3.0:$GEM_HOME
```
一般包含两个位置： `Ruby` 安装目录的默认位置和自定义的 `GEM_HOME` 位置
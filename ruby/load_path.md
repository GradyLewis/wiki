# $LOAD_PATH和$:
`$LOAD_PATH` 或者 `$:` 是两个 Ruby 环境的全局变量，作用一样，是 Ruby 读取外部文件的一个环境变量数组，当使用 `require autoload load` 等载入模块的方法时，Ruby 会从 `$LOAD_PATH` 或者 `$:` 中依次寻找指定的模块，如果没有找到，则抛出 `LoadError` 异常。

以下是一个 Rails 项目中的 `$LOAD_PATH` 或者 `$:` 的内容：
```ruby
2.1.8 :002 > $:
 => ["/opt/meta/lib", "/opt/meta/vendor", "/opt/meta/app/admin", "/opt/meta/app/assets", "/opt/meta/app/controllers", "/opt/meta/app/helpers", "/opt/meta/app/mailers", "/opt/meta/app/models", ...]
```
因此当使用 `require 'abc'` 时，Ruby 便会在以上目录中查找是否有 `abc.rb` 这个文件，以及文件里是否存在 `module Abc` 这个模块。

如果需要添加自定义的环境变量，可以：
```ruby
path = '/my/own/path'
# 将 path 目录添加到 $LOAD_PATH 数组的首位
$LOAD_PATH.unshift path unless $LOAD_PATH.include?(path)
```
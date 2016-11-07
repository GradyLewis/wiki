# Ruby代码执行 Thin & Puma
```ruby
# Thin
port = 3000
# 参数会解析成 Thin 启动时需要的参数
thin_opts = ['-e', 'production', '-l', 'log/thin.log', '-P', '/tmp/log/thin.pid']
ARGV.push('start', '-p', port.to_s, '-a', '127.0.0.1', '-d', *thin_opts)
require 'rubygems'
version = '>= 0'
gem 'thin', version
# 找到 thin 这个 gem 下面的可执行文件 thin 的位置
load Gem.bin_path('thin', 'thin', version)

# Puma
port = 3000
# 参数会解析成 Puma 启动时需要的参数
puma_opts = ['-e', 'production', '--redirect-stdout', 'log/puma_out.log', '--redirect-stderr', 'log/puma_err.log', '--pidfile', '/tmp/log/puma.pid', '-d']
ARGV.push('config.ru', '-b', "tcp://127.0.0.1:#{port.to_s}", *puma_opts)
require 'rubygems'
version = '>= 0'
gem 'puma', version
# 找到 puma 这个 gem 下面的可执行文件 puma 的位置
load Gem.bin_path('puma', 'puma', version)
```
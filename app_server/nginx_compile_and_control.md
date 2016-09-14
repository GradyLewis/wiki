#Nginx的编译和Ruby控制模块编写
首先从 `Nginx` 官网下载源代码
```ruby
http://nginx.org/download/nginx-1.11.3.tar.gz
```
解压后进入目录，进行编译和安装，注意启用 SSL 模块 `--with-http_ssl_module`，指定安装位置 `--prefix`
```ruby

./configure --with-http_ssl_module --prefix=/opt/nginx
make && make install
```
使用 `Ruby` 控制 `Nginx`
```ruby
#encoding:utf-8
# Nginx 控制模块
require 'fileutils'

module Nginx
  class Nginx
    class << self
      # nginx pid 文件位置
      def pid_file
        '/opt/nginx/logs/nginx.pid'
      end

      def get_pid
        File.read(pid_file).to_i rescue 0
      end
      
      # nginx 可执行文件
      def exec_bin
        '/opt/nginx/sbin/nginx'
      end

      # 启动 nginx
      # Process.kill(0, PID) 用于检测制定进程是否存活，返回 1 或 0 状态
      # 需要注意如果 PID = 0, 也会返回 1，因此需要做特殊处理
      def start
        if get_pid == 0 || (Process.kill(0, get_pid).to_i rescue 0) != 1
          system("#{exec_bin}")
        end
        puts 'Nginx is running'
      end

      # 停止 nginx
      def stop
        result = Process.kill(0, get_pid).to_i rescue 0
        if result == 0 || get_pid == 0 || get_pid.nil?
          FileUtils.rm_r(pid_file) rescue 0
        else
          # nginx -s stop
          system("#{exec_bin} -s stop")
        end
        puts 'Nginx is stopped'
      end

      # 查询 nginx 状态
      def status
        if get_pid != 0 && (Process.kill(0, get_pid).to_i rescue 0) == 1
          puts 'Nginx is running'
        else
          puts 'Nginx is stopped'
        end
      end
    end
  end
end
```
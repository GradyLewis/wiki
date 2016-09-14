# Nginx + Thin 运行 Rails 项目
## Nginx 主要配置
```ruby
# 参考同级目录下 nginx.conf.default 文件内容
#user  nobody;
worker_processes  2;

error_log  /opt/meta/logs/nginx.error.log;

pid        /opt/meta/logs/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    access_log /opt/meta/logs/nginx.access.log;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    gzip  on;

    upstream web_ui { # upstream 可用作负载均衡
        server 127.0.0.1:3001; # 基于 Thin 运行的 Rails 应用
        # server 127.0.0.1:3002;
        # server 127.0.0.1:3003;
     }

    server {
        listen       0.0.0.0:8080 ssl; # 启用 SSL 访问
        server_name  localhost;

        root '/opt/meta/web_ui/public'; # WebUI 项目目录
        
        # OpenSSL 生成的自签名证书
        ssl_certificate      /opt/meta/nginx/certs/server.pem;
        ssl_certificate_key  /opt/meta/nginx/certs/server.key;

        location ^~ /assets/ { # Rails 项目编译的静态文件 css，js，image 等
            expires max;
            add_header Cache-Control public;
        }

        location / {
            proxy_pass http://web_ui; # 从 8080 端口转发至内网 3001 端口
        }

        error_page  404              /404.html;
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
}
```
## Thin 控制模块
```ruby
#encoding:utf-8
# Thin 控制模块
require 'fileutils'

module Thin
  class Thin
    class << self
      # thin pid 文件位置
      def pid_file
        "opt/meta/web_ui/logs/thin.pid"
      end

      def get_pid
        File.read(pid_file).to_i rescue 0
      end

      # 启动 thin
      # Process.kill(0, PID) 用于检测制定进程是否存活，返回 1 或 0 状态
      # 需要注意如果 PID = 0, 也会返回 1，因此需要做特殊处理
      def start
        Dir.chdir('opt/meta/web_ui')
        if get_pid == 0 || (Process.kill(0, get_pid).to_i rescue 0) != 1
          # 本地模式 3001 端口启动应用服务器，外部无法访问，只能通过 nginx 下发
          options = ['-a', '127.0.0.1', '-p', '3001'] # 127.0.0.1:3001
          options += ['-P', "logs/thin.pid", '-l', "logs/thin.log"] # 日志和PID位置
          options +=  ['-d', 'start'] # 以服务模式启动
          system('thin', *options) # 以设定的配置启动 Thin
        end
        puts 'Meta is running'
      end

      # 停止 thin
      # Process.kill(9, PID) 强制杀死进程
      def stop
        result = Process.kill(0, get_pid).to_i rescue 0
        if result == 0 || get_pid == 0 || get_pid.nil?
          FileUtils.rm_r(pid_file) rescue 0
        else
          FileUtils.rm_r(pid_file) if Process.kill(9, get_pid) == 1
        end
        puts 'Meta is stopped'
      end

      # 检测 thin 状态
      def status
        if get_pid != 0 && (Process.kill(0, get_pid).to_i rescue 0) == 1
          puts 'Meta is running'
        else
          puts 'Meta is stopped'
        end
      end
    end
  end
end
```
## 如何访问
本地访问
```ruby
http://127.0.0.1:3001/
https://127.0.0.1:8080/
```
外部访问
```ruby
https://xx.xx.xx.xx:8080/
```
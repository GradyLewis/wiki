# Nginx只允许本机访问
```ruby
server {
        listen       8589;
        server_name  localhost;
        allow 127.0.0.1;
        deny all;
        ...
}
```
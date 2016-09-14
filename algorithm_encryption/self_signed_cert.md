# OpenSSL生成自签名证书
## 生成RSA密钥的方法
这个命令会生成一个2048位的密钥，同时有一个 `des3` 方法加密的密码
```ruby
openssl genrsa -des3 -out server.key 2048
```
如果你不想要每次都输入密码，可以改成：
```ruby
openssl genrsa -out server.key 2048
```
建议用2048位密钥，少于此可能会不安全或很快将不安全。
## 生成一个证书
```ruby
openssl req -new -key server.key -out server.csr 
```
你可以拿着这个文件去数字证书颁发机构（即CA）申请一个数字证书。CA会给你一个新的文件 cacert.pem，那才是你的数字证书。<br/>
如果是自己做测试，那么证书的申请机构和颁发机构都是自己。就可以用下面这个命令来生成证书：
```ruby
openssl req -new -x509 -key server.key -out server.pem -days 3650
```
## 使用数字证书和密钥
有了 `server.key` 和 `server.pem` 文件后就可以在自己的程序中使用了，比如，比如 `Nginx` 的 SSL 证书文件，或者做一个加密通讯的服务器。
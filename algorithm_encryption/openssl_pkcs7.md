# OpenSSL::PKCS7加密
证书密钥对
```ruby
pkey = 'server.pem' # 包含 OpenSSL 自签名证书的 key 和 cert
cert = OpenSSL::X509::Certificate.new(File.read(pkey))
key  = OpenSSL::PKey::RSA.new(File.read(pkey))
```
加密和解密（加密用到1个，解密用到2个）
```ruby
enc = OpenSSL::PKCS7.encrypt([cert], data) # 加密
data = enc.decrypt(key, cert) # 解密
```
签名加密和解密（加密用到2个，解密用到1个）
```ruby
# 加密
sign = OpenSSL::PKCS7.sign(cert, key, data, [], 0)
# 解密
store = OpenSSL::X509::Store.new
store.add_cert(cert)
ver = sign.verify([], store)
data = sign.data
```
可以把加密后的内容转换为邮件体格式
```ruby
# 数据转换成邮件体
smime = OpenSSL::PKCS7.write_smime(enc)
# 从邮件体读取数据
enc  = OpenSSL::PKCS7.read_smime(smime) rescue nil
```
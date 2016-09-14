# Digest::SHA256和OpenSSL::Cipher加密
Digest::SHA256加密是不可逆的
```ruby
dig = Digest::SHA256.digest('123')
```
OpenSSL::Cipher加密和解密
```ruby
# 加密
cipher = OpenSSL::Cipher::Cipher.new("aes-256-cbc") # 使用的加密类
cipher.encrypt # 设定为加密模式
cipher.key = token # 设定加密用的密钥
output = cipher.update(code) # code 是需要加密的内容
output << cipher.final
 
# 解密
aes  = OpenSSL::Cipher::Cipher.new("aes-256-cbc") # 使用的加密类
aes.decrypt # 设定为解密模式
aes.key = token # 设定解密用的密钥，和加密时用的要一致
code = aes.update(output) # output 是需要解密的内容
code << aes.final
```
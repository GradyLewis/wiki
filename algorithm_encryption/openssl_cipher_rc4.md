# OpenSSL::Cipher::RC4加密
## 解密
```ruby
encrypted_content = '12f24saf3' # 待解密的内容
 
cipher = OpenSSL::Cipher::RC4.new
cipher.decrypt
cipher.key = token # 解密的密钥，和加密的一致
 
decrypted_module_content = cipher.update(encrypted_content)
decrypted_module_content << cipher.final # 解密得到结果
```
## 加密
```ruby
des = OpenSSL::Cipher::RC4.new
des.encrypt
des.key = token # 加密的密钥，和解密的一致
result = des.update(decrypted_module_content)
result << des.final # 结果
```
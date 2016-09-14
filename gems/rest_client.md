# RestClient
```ruby
# OpenSSL::SSL::VERIFY_NONE 不验证目标网址的 SSL 证书
client = RestClient::Resource.new("https://address", :verify_ssl => OpenSSL::SSL::VERIFY_NONE, :headers => { :client_version => 1 })
# HTTP Basic 验证
client = RestClient::Resource.new('http://address', :user => 'user', :password => 'password')
# 连接超时 open_timeout 和响应超时 read_timeout（秒）
client = RestClient::Resource.new('http://address', :read_timeout => 10, :open_timeout => 10)
# 普通参数
data = {:x1 => 1, :x2 => 2}
# 上传文件
data = {:multipart => true, :file => File.new('/tmp/1.txt')}
# 加上 cookies 的 post 请求
res = client.post data, :content_type => :json, :accept => :json, :cookies => {'sid' => '345sf-324as-sdfg32-sdf342'}
# 响应码
res.code # => 200
# 响应内容
res.body
# 响应 cookies 信息
res.cookies
# 响应 headers 信息
res.headers
```
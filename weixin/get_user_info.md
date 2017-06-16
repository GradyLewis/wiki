# 获取微信access token，用户信息，绑定信息
```
class << self
    # 获取微信接口调用凭据access_token
    def get_access_token(app_id, app_secret)
      url = 'https://api.weixin.qq.com/cgi-bin/token'
      options = {:grant_type => 'client_credential', :appid => app_id, :secret => app_secret}
      response = RestClient.get url, :params => options
      result = JSON.parse(response) rescue {}
      access_token = result['access_token']
      if access_token.present?
        User.redis.setex('access_token', 6000, access_token)
        User.redis.setex('access_token_at', 6000, Time.now.strftime('%Y-%m-%d %H:%M'))
      end
      access_token
    end

    # 获取用户Open ID
    def get_open_id(app_id, app_secret, code)
      options = {:appid => app_id, :secret => app_secret, :code => code, :grant_type => 'authorization_code'}
      url = 'https://api.weixin.qq.com/sns/oauth2/access_token'
      response = RestClient.get url, :params => options
      JSON.parse(response) rescue {}
    end

    # 获取用户绑定信息
    def get_bind_info(access_token, open_id)
      url = 'https://api.weixin.qq.com/cgi-bin/user/info'
      options = {:access_token => access_token, :openid => open_id, :lang => 'zh_CN'}
      response = RestClient.get url, :params => options
      JSON.parse(response) rescue {}
    end
  end
```
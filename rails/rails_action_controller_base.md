# 使用Rails自带的接口类开发接口
```ruby
#encoding:utf-8
# API 接口
class ApiController < ActionController::Base
  before_action :basic_authenticate

  #useage curl -u user:pass -H "Content-Type: application/json" -X POST -d '{"id":"1","keys":["key1", "key2"]}' http://127.0.0.1:3000/api/alarm
  def alarm
    request_data = JSON.parse(request.body.read) rescue nil
    @result = {'err_no' => 0, 'message' => 'success'}
    Rails.logger.info "API `alarm` called: Params #{request_data || 'NULL'}, Return #{@result}"
    render :json => @result
  end

  private

  # HTTP Basic 验证
  def basic_authenticate
    system_config = SystemSetting.find_by_number('api_basic_auth')
    if system_config.present?
      authenticate_or_request_with_http_basic do |id, password|
        id == system_config.user && password == system_config.pass
      end
    else
      render :status => 401, :text => "HTTP Basic: Access denied.\n"
    end
  end

end
```
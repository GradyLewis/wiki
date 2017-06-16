# 微信支付 JS-SDK 模块

后台预支付部分
```
#encoding:utf-8
# 微信支付模块
class WeixinPay
  class << self
    # 预支付
    def prev_pay_for_seller(appId, mchid, order_name, trade_no, fee, notify_url, openid, remote_ip)
      url = 'https://api.mch.weixin.qq.com/pay/unifiedorder'
      options = {}
      options['appid'] = appId
      options['mch_id'] = mchid
      options['nonce_str'] = ('a'..'z').to_a.shuffle[0..10].join('')
      options['body'] = order_name
      options['out_trade_no'] = trade_no
      options['total_fee'] = fee.to_i # 以分为单位
      options['notify_url'] = notify_url
      options['trade_type'] = 'JSAPI'
      options['spbill_create_ip'] = remote_ip
      options['openid'] = openid
      sign = encode(options) # 生成密钥
      options['sign'] = sign
      params = options.to_xml(:dasherize => false, :skip_instruct => true).gsub('hash>', 'xml>')
      result = RestClient.post url, params, :content_type => :xml, :accept => :xml
      if result.present?
        Hash.from_xml(result)['xml']
      else
        {}
      end
    end

    # 参数的加密
    def encode(options = {})
      str_arr = []
      # 待签名参数按照字段名的ascii码从小到大排序后使用QueryString的格式拼接而成，空值不传递，不参与签名。
      options.keys.sort.each do |key|
        str_arr << "#{key}=#{options[key]}"
      end
      url_params = str_arr.join('&')
      # sign = Md5(原字符串&key=商户密钥).upcase
      encode_str = url_params + '&key=' + WEIXIN_PAY[:key] # 商户密钥
      Digest::MD5.hexdigest(encode_str).upcase
    end
  end
end
```
微信支付成功后的反馈，对应 notify_url 中的处理
```
# 微信支付反馈
  def notify_url
    if params['xml'].present? && params['xml']['result_code'].eql?('SUCCESS') &&
        params['xml']['return_code'].eql?('SUCCESS') && # 业务完成
        params['xml']['appid'].eql?(WEIXIN_PAY[:appId]) &&
        params['xml']['mch_id'].eql?(WEIXIN_PAY[:mchid]) # 商户信息
      options = params['xml']
      sign = options.delete('sign')
      origin_sign = WeixinPay.encode(options)
      if origin_sign.eql?(sign) # 验证签名是否正常
        @order = GoodOrder.find_by_trade_no(options['out_trade_no']) # 根据订单号查询
        if @order.present? && @order.status == 0 # 支付订单存在，且目前处于未支付状态，则修改状态，并后续处理
          if @order.update_attribute(:status, 1) # 修改状态为已支付
            # 记录日志
            # 减掉库存
            # 给用户发送站内信
          end
        end
      end
    end
    render :text => 'SUCCESS'
  end
```
支付页面controller
```
def pay
    @ware_order = WareOrder.find_by_id(params[:id])
    # 验证当前用户是否参与此活动，订单未支付，订单正常
    if @ware_order.present? && @ware_order.person_info_id == @person.id
      @wares = @ware_order.wares
      @pay_options = {}
      @pay_options['appId'] = WEIXIN_PAY[:appId]
      @pay_options['timeStamp'] = Time.now.to_i
      @pay_options['nonceStr'] = ('a'..'z').to_a.shuffle[0..10].join('')
      @pay_options['package'] = "prepay_id=#{@ware_order.prepay_id}"
      @pay_options['signType'] = 'MD5'
      @pay_options['paySign'] = WeixinPay.encode(@pay_options)
    else
      redirect_to :controller => 'weixin2/ware/ware', :action => 'index'
    end
  end
```
支付页面views js
```
<script type="text/javascript" defer="defer" for="window" event="onload">
  // 微信支付
  function doPay() {
    try {
      WeixinJSBridge.invoke('getBrandWCPayRequest', {
        'appId': '<%= @pay_options['appId'] %>',
        'timeStamp': '<%= @pay_options['timeStamp'] %>',
        'nonceStr': '<%= @pay_options['nonceStr'] %>',
        'package': '<%= @pay_options['package'] %>',
        'signType': 'MD5',
        'paySign': '<%= @pay_options['paySign'] %>'
      }, function(res){
        if (res.err_msg == 'get_brand_wcpay_request:ok') {
          alert('您的支付成功。');
        } else if (res.err_msg == 'get_brand_wcpay_request:cancel') {
          alert('您取消了支付。');
        } else {
//          alert('您的支付未成功。');
          alert(res.err_msg);
        }
        location.href='/weixin2/ware/order/<%= @ware_order.id %>';
      });
    } catch(err) {
      //在这里处理错误
      alert(err.message);
    }
  }

    $('#do_pay').click(function(){
      doPay();
    });
  });
</script>
```
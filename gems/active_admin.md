# ActiveAdmin
```ruby
ActiveAdmin.register Model do
  # 父菜单以及排序
  menu :parent => '系统管理', :priority => 1
  # 只允许的的默认action
  actions :index, :show, :destroy, :edit, :update
  
  # 快速查询
  scope :all, default: true, :label => '全部'
  scope("等待处理") { |scope| scope.where(tp: 0) }
  
  # 授权提交的表单中允许写入的字段
  permit_params [:title, :name]
  permit_params :title, :name
  
  # 搜索栏
  filter :email, :as => :string, :label => '邮件地址'
  filter :state, :as=> :select, :collection=> ApplyForm::STATE_DATA.invert
  filter :tp, :as=> :select, :collection=> [['认证申请', 0], ['认证通过', 1], ['认证未通过', 2]]
  
  # 改写默认 action
  controller do
    def index
      super
    end
  end
  
  # index 页面
  index do
    column :title
    column :type do |record|
      Model::TYPE[record.type]
    end
    actions
  end
  
   # 表单页面
  form do |f|
    f.inputs '权限管理' do
      f.input :name, :required => true
      f.input :password, :as=> :password
      f.input :start_at, :as => :datepicker # 集成 datepicker 插件，精确到天
      f.input :note, :as => :kindeditor, :simple_mode => true # 集成了 kindeditor 插件
      f.input :type, :as => :select, :collection => Model::TYPE.invert
    end
    f.actions
  end
  
  # 详细页面
  show do
    attributes_table do
      row :name
      row :type do |record|
        Model::TYPE[record.type]
      end
    end
    
    # 获取当前后台用户
    current_admin_user.is_admin?
    
    # 支持直接使用html标签
    hr
    br
    table do
      tr do
        td '测试'
        td :colspan => "5",:align => 'center' do
          '测试'
        end
      end
    end
    
    # 增加内容
    panel '单条记录的信息列表，类似详细页面' do
      attributes_table_for record.certificate do
        row :code
        row :tp do |cert|
          Certificate::TP_DATA["#{cert.tp}"]
        end
        row :is_pay
      end
    end
    
    panel '多条记录列表，类似index页面' do
      table_for(record.orders) do |t|
        t.column("卡号/订单号") {|item| item.trade_no }
        t.column("支付状态") {|item| Order::STATE_DATA["#{item.state}"]  }
        t.column("支付时间") {|item| item.pay_at ? item.pay_at.long_time : "-" }
        t.column("记录人") {|item| item.admin_user ? item.admin_user.email : "-"  }
        t.column("备注") {|item| item.note  }
      end
    end
    
    panel '表单页面' do
      active_admin_form_for(:order, url: {:action=>"add_pay",:id=>resource.id, 
                            :controller => "/#{ActiveAdmin.application.default_namespace.to_s}/apply_forms"}) do |f|
        columns do
          column do
            f.inputs do
              f.input :trade_no,:label=>"卡号/订单号",:input_html => {:name=>"trade_no"}, :required => true
              f.input :bank,:label=>"所属银行",:input_html=>{:name=>"bank"}
              f.input :total_free, :label=>"支付金额", :input_html => {:name=>"total_free"}, :required => true
              f.action :submit, :label=>"添加支付记录"
            end
          end
        end
      end
    end
    
    panel '渲染views层页面' do
      render :partial => '/admin/test/abc', :locals => {:resource => resource}
    end
  end
  
  # 侧边栏
  sidebar "审核", only: :show do
    if resource.present?
      active_admin_form_for(:apply_form, url: {:action=>"apply",:id=>record.id,
                            :controller => "/#{ActiveAdmin.application.default_namespace.to_s}/apply_forms"}) do |f|
        f.inputs do
          f.input :state, :required => true, :as => :select, :collection => ApplyForm::STATE_DATA.invert, 
                  :include_blank=>false, :label=>"状态",
                  :input_html=>{:name=>"state",:style=>"width:150px;"}
          f.input :note,:as=>:text,:label=>"备注",:input_html=>{:name=>"note",:style=>"height:55px;width:150px;"}
        end
        f.actions do
          f.action :submit,:label=>"审核"
        end
      end
    end
  end
  
  # 右上角按钮
  action_item :only => [:show] do
    text_node link_to '生成证书文件', {:action => 'a'}, :method => :post, 
                      :data => {:confirm => '？', :disable_with => '请等待...'}
    text_node link_to '生成证书文件2', {:action => 'b'}, :method => :post, 
                      :data => {:confirm => '？', :disable_with => '请等待...'}
  end
  
  # 增加action
  collection_action :add_functional, :method => :get, :title => '增加权限' do
  end
  
  member_action :update, :method => :post, :title => '更新权限' do
     # resource 即为 member action 中传递的对象
     p resource.id
     # ActiveAdmin.application.default_namespace.to_s 获得后台路径
     redirect_to :action => :show, :controller => "/#{ActiveAdmin.application.default_namespace.to_s}/admin_roles", 
                 :id => resource.id
  end
end
```
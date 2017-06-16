# pothoven-attachment_fu 文件和图片存储使用方法
1.Gem引入
```
gem 'pothoven-attachment_fu'
```
2.图片存储Model
创建 AssetImg model 文件：
```
#encoding:utf-8
require 'mime/types'
class AssetImg < ActiveRecord::Base
  attr_accessible :resource_id, :uploaded_data
  has_many :children, :class_name => 'AssetImg', :foreign_key => 'parent_id'
  belongs_to :resource, :polymorphic => true # 指定图片的类型/对象，多态关联

  has_attachment :content_type => :image,
                 :jpeg_quality => 75,
                 :storage => :file_system,
                 :max_size => 10.megabytes,
                 :thumbnails => {:thumb => '640x480>', :tiny => '320x240>', :middle => '480x320>'}, # 数据库中将生成多条记录，对应不同的尺寸和存放地址
                 :processor => :MiniMagick,
                 :resize_to => '2000x2000>'

  validates_as_attachment # 验证上面的条件是否成立

  def public_filename=(public_filename)
    public_filename
  end
  
  # 针对某些手机上传的图片取不到图片类型的问题
  def swf_uploaded_data=(data)
    c_t = MIME::Types.type_for(data.original_filename).join('')
    c_t = 'image/jpeg' if c_t.blank?
    data.content_type = c_t
    self.uploaded_data = data
  end

  # 旋转图片
  def rotate_image(rotate="90")
    self.rotate_image!(rotate)
    self.thumbnails.each do |image|
      image.rotate_image!(rotate)
    end
  end

  # 图片旋转执行
  def rotate_image!(rotate)
    path = "#{Rails.root}/public#{self.public_filename}"
    if File.exist?(path)
      image = MiniMagick::Image.open(path)
      image.combine_options do |c|
        c.rotate "#{rotate}" # 旋转 90 度
      end
      image.write("#{Rails.root}/public#{self.public_filename}")
    end
  end
end
```
3.AssetImg的Migration文件：
```
class CreateAssetImgs < ActiveRecord::Migration
  def change
    create_table :asset_imgs do |t|
      t.column :filename, :string
      t.column :content_type, :string
      t.string :resource_type, :limit => 50 #类型
      t.integer :resource_id #ID
      t.column :size, :integer
      t.column :width, :integer
      t.column :height, :integer
      t.column :parent_id, :integer
      t.column :thumbnail, :string
      t.column :created_at, :datetime
      t.column :alt, :string
    end
  end
end
```
4.其他Object使用该图片model
```
class Person < ActiveRecord::Base
    # 例如：每个人对应一个头像
    has_one :asset_img, :class_name => 'AssetImg', :as => :resource, :dependent => :destroy
end
```
5.在controller中的使用
```
asset_img = AssetImg.new
asset_img.swf_uploaded_data = params[:file] # 上传的图片
person = Person.new # 创建 Person 对象
person.asset_img = asset_img
person.save
```
# 取出不同尺寸图片的地址
```
person.asset_img.public_filename #=> /asset_imgs/2/file.jpg
person.asset_img.public_filename(:thumb) #=> /asset_imgs/2/file_thumb.jpg
person.asset_img.public_filename(:small) #=> /asset_imgs/2/file_small.jpg
```



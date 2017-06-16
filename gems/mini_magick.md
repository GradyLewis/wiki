# MiniMagick 处理图片（缩放、旋转、图片合并、裁剪）
```bash
# 1.缩放头像
logo_image = MiniMagick::Image.open("#{Rails.root}/public/1.jpg")
logo_image.combine_options do |b|
  b.resize("#{logo_width}x")
end
resize_w = logo_image[:width] # 缩放后宽度
resize_h = logo_image[:height] # 缩放后高度
# 2.旋转头像
logo_image.combine_options do |b|
  b.rotate(degrees)
end
offset_x = (logo_image[:width] - resize_w) / 2 # 头像旋转之后有个偏差，需要减掉
offset_y = (logo_image[:height] - resize_h) / 2 # 头像旋转之后有个偏差，需要减掉
# 3.头像贴到白色背景上
bg_image = MiniMagick::Image.new("#{Rails.root}/public/bg.jpg")
first_image = bg_image.composite(logo_image) do |c| # 后者贴到前者上
  c.compose 'over'
  c.geometry "+#{left - offset_x}+#{top - offset_y}" # 后者在前者上的起始坐标
end
# 再贴上圣诞老人
second_image = MiniMagick::Image.new("#{Rails.root}/public/test.png")
second_image.combine_options do |b|
  b.resize("#{bg_width}x")
end
result = first_image.composite(second_image) do |c|
  c.compose 'over'
end
result.crop("#{second_image[:width]}x#{second_image[:height]}+0+0") # 裁剪，四个参数分别为：宽、高、起点坐标x和y
result_path = "#{Rails.root}/public/final/"
FileUtils.mkdir(result_path) unless File.exist?(result_path)
result.write "#{result_path}result.jpg"

```
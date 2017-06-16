# Nokogiri 解析 HTML
Ubuntu上安装 nokogiri 异常，缺失 libxslt-dev libxml2-dev
```
sudo apt-get install libxslt-dev libxml2-dev
```
如果是centos则是
```
sudo yum install libxslt-devel libxml2-devel
```
Usage:
```
doc = Nokogiri::HTML html_content
# 根据元素名称和class名称获得元素内容
content = doc.css('p a.title').first.to_s
获得页面的title标题
title = doc.at_css("title").text
# 查询所有图片节点
doc.search('img').each do |node|
  # 设置图片高宽
  node['width'] = '100%'
  node.attributes['height'].remove if node.attributes['height'].present?
  # 收集图片地址
  img_path = node.attributes['src'].try(:value) rescue nil
  获得图片地址和图片格式
  node['data-src'], node['data-type']
  # 替换图片地址
  node['src'] = local_path
  # 删除图片节点
  node.remove
  # 替换内容
  node.replace "<body><div id='content'>#{node.inner_html.strip}</div></body>"
end
# 删除元素上不需要的属性，如class,style,id等
doc.xpath('//@class', '//@style', '//@id').remove
# 转换为html string 类型
content = doc.to_s
# 删除HTML注释
content.gsub!(/<!--([\s\S]*?)-->/, '')
# 多个空格替换成一个
content.gsub!(/\s+/, ' ')
# 删除 span 元素
regex1 = /<span\s*[^>]*>/i # 开头
regex2 = /<\/span\s*[^>]*>/i # 结尾
content.gsub!(regex1, '')
content.gsub!(regex2, '')
# 删除 div 元素
regex1 = /<div\s*[^>]*>/i # 开头
regex2 = /<\/div\s*[^>]*>/i # 结尾
content.gsub!(regex1, '')
content.gsub!(regex2, '<br/>')
# 多个换行换成一个
regex1 = /(<br\s*[^>]*>\s*)+/i # <br> <br > <br/> <br /> 等等
content.gsub!(regex1, '<br/>')
# 如果 p 后面有 br 则删除 br
content.gsub!(/<\/p>\s*<br\s*[^>]*>\s*/i, '</p>')
# 如果 br后面有 p 则删除 br
content.gsub!(/<br\s*[^>]*>\s*<\/p\s*[^>]*>\s*/i, '</p>')
content.gsub!(/<br\s*[^>]*>\s*<p\s*[^>]*>\s*/i, '<p>')
# 替换 h1.h2等元素为 strong
%w(h1 h2 h3 h4 h5).each do |element|
  content.gsub!(/<#{element}\s*[^>]*>/i, '<p><strong>')
  content.gsub!(/<\/#{element}\s*[^>]*>/i, '</strong></p>')
end
# 删除换行符
content.gsub!(/[\r\n]+/, '')
# 删除尖括号左右的空格，其中 xC2\xA0 也是空格的一种形势
content.gsub!(/[\s\xC2\xA0]+</i, '<') # 左
content.gsub!(/>[\s\xC2\xA0]+/i, '>') # 右
# 删除没有任何内容的元素
```
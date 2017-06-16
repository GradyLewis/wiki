# 服务器端模拟文件上传
```
mime_type = MIME::Types.type_for(file_path.to_s).first.content_type
tmpfile = ActionDispatch::Http::UploadedFile.new({
                          :tempfile => File.new(file_path),
                          :filename => params[:filename]
                        })
tmpfile.content_type = mime_type
```
tmpfile 就是一个上传的临时文件，可以对其进行保存操作。
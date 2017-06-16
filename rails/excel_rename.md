# Excel导出，重命名文件
```bash
respond_to do |format|
  format.xls {
      headers["Content-Disposition"] = 'attachment; filename=总退费单.xls'
  }
end
```
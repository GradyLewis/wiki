# Linux 挂载光盘 CDROM
```bash
# 将 /dev/cdrom 设备挂载到本地目录 /media/cdrom 文件夹
sudo mount -t auto /dev/cdrom /media/cdrom/ 
>> mount: /dev/sr0 写保护，将以只读方式挂载
```
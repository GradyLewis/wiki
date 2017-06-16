# ECS Linux清理内存缓存的操作步骤
Linux为了性能会将服务器中的目录路径信息、文件节点等保存到内存中，频繁的文件访问会导致内存中的Cache使用量大增。可以通过以下方式进行清理。

清理页缓存:
```
echo 1 > /proc/sys/vm/drop_caches
```
清理目录项缓存与文件结点缓存:
```
echo 2 > /proc/sys/vm/drop_caches
```
清理页缓存、目录项缓存与文件结点缓存:
```
echo 3 > /proc/sys/vm/drop_caches
```
 
一般推荐第三种方式操作
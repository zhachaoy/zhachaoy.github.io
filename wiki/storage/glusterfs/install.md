# GlusterFS

1. **准备块设备**
   > 存储服务器需要两块硬盘, 其中一块负责跑操作系统, 另一块作为存储磁盘

   > 如果没有两块硬盘, 可以使用dd命令虚拟一块硬盘出来

   ```
   假设data目录挂载了500G分区
   cd /data

   创建400G虚拟块设备
   dd if=/dev/zero of=gluster_disk bs=8M count=51200

   授权
   chmod 777 gluster_disk

   声明设备输入输出
   losetup /dev/loop gluster_disk

   可以在/dev目录查看到loop定义

   删除时执行以下命令, 即可释放磁盘空间
   losetup -d /dev/loop
   rm /data/gluster_disk
   reboot
   ```

2. **Heketi**
   > 用于提供glusterfs API接口
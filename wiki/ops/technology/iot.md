# IOT

1. **MQTT**
   > emqx集群部署, 需要先启动leader节点, 其他节点可以采用kubernetes复制方式加入到主节点
   ```
   leader节点指定名称
   EMQX_NAME=emqx

   复制节点指定集群地址
   EMQX_JOIN_CLUSTER=emqx@10.130.2.109
   ```

2. **BLE SIG MESH**
   > 本地设备采用蓝牙组网, 并上报网络信息到云端

   > 设备注册与上报时序图
   
   > 手机APP本地通过mesh网络控制时序图

   > 云平台远程控制时序图
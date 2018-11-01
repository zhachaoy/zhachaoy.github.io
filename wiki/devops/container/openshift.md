# Openshift

1. **租户(namespace)**
   1. 权限
      ```bash 
      build阶段需要root权限执行时,需要为租户开放权限
      oc edit scc privileged
      在users里增加指定租户的builder用户
      
      depoly阶段需要root权限执行时,需要为租户开放权限
      oc edit scc anyuid
      在users里增加指定租户的default用户
      ```
   
   2. 集群管理员(cluster role)
      ```bash
      登录集群管理员
      oc login -u system:admin -n default --config=/etc/origin/master/admin.kubeconfig
      
      登录后必须手动切换用户
      oc login -u system:admin -n xxx
      ```
      
2. **持久化存储(pv)**
   1. nfs服务器
      ```bash 
      安装后主要关闭selinux
      安装后注意防火墙iptables, firewalld
      安装后注意存储目录权限
      ```
      
   2. 创建pv
      ```bash 
      切换到system:admin用户, pv为全局共享
        {
          "apiVersion": "v1",
          "kind": "PersistentVolume",
          "metadata": {
            "name": "iot-dev-paascloud"
          },
          "spec": {
            "capacity": {
              "storage": "10Gi"
            },
            "accessModes": [ "ReadWriteMany" ],
            "nfs": {
              "path": "/export/pv/dev-paascloud",
              "server": "172.16.135.104"
            },
            "persistentVolumeReclaimPolicy": "Retain"
          }
        }   
       
      注意设置label用于pvc选择
      oc label pv iot-dev-paascloud disktype=iot-dev-paascloud
      ```
      
3. **二次构建**
   1. 编译性语言第一次构建可执行程序(比如jar)
      ```bash 
      build镜像一般进行环境初始化, 依赖下载等, 比较占用空间
      ```
      
   2. 第二次构建从第一次构建的镜像中读取二进制程序(比如jar)用于运行时
      ```bash 
      runtime镜像一般执行启动命令
      ```
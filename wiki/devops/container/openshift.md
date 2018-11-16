# OpenShift

1. **Minishift单机安装**
   1. 安装KVM驱动
      ```bash 
      curl -L https://github.com/dhiltgen/docker-machine-kvm/releases/download/v0.7.0/docker-machine-driver-kvm -o /usr/local/bin/docker-machine-driver-kvm
      chmod +x /usr/local/bin/docker-machine-driver-kvm
      yum install libvirt qemu-kvm
      systemctl start virtlogd
      systemctl enable virtlogd
      systemctl start libvirtd
      systemctl enable libvirtd
      ```
   
   2. 配置github token(Minishift会去github上验证，并调用其api，不配置token将没有api权限)
      ```bash
      export MINISHIFT_GITHUB_API_TOKEN=<token_ID>
      ```
      
   3. 下载Minishift安装包，解压并配置到环境变量中(必须)
      ```bash
      export PATH="$(pwd)":$PATH
      ```
   
   4. 执行安装命令
      ```bash
      minishift start
      ```
      
2. **OpenShift-Ansible集群安装**
   1. 安装基础包(必须重启)
      ```bash 
      ssh-keygen
      sudo yum install -y wget git net-tools bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct
      sudo yum update -y
      reboot
      ```
      
   2. 安装docker
      ```bash 
      sudo yum install -y docker-1.13.1
      sudo systemctl enable docker
      ```
      
      配置docker国内代理
      ```bash 
      sudo tee /etc/docker/daemon.json <<-'EOF'
      {
        "registry-mirrors": ["https://31f62odg.mirror.aliyuncs.com"]
      }
      EOF
      
      sudo systemctl daemon-reload
      sudo systemctl restart docker
      ```
      
   3. 安装ansible(版本必须2.6.5, openshift-ansible项目明确说明Ansible >= 2.6.5, Ansible 2.7 is not yet supported and known to fail)
      ```bash 
      sudo yum install -y https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/ansible-2.6.5-1.el7.ans.noarch.rpm
      sudo yum install -y pyOpenSSL
      
      以下安装openshift并没有要求, openshift-ansible有这个要求, 还不清楚是否必须 
      sudo yum install -y python-cryptography python-lxml
      
      Additional requirements:
      sudo yum install -y patch httpd-tools java-1.8.0-openjdk-headless
      ```
      
   4. 在ansible节点配置ssh免密登录, 配置本地hosts
      ```bash 
      sudo ssh-keygen
      ```
      
      给所有节点推送公钥
      ```bash 
      for host in openshift-lb openshift-master001 openshift-master002 openshift-master003 openshift-node001 openshift-node002 openshift-infra001 openshift-infra002
        do ssh-copy-id -i ~/.ssh/id_rsa.pub $host; done
      ```
      
      修改所有节点本地路由记录
      ```bash 
      sudo vi /etc/hosts
      172.16.135.95    openshift-lb
      172.16.135.91    openshift-master001
      172.16.135.93    openshift-master002
      172.16.135.94    openshift-master003
      172.16.135.86    openshift-node001
      172.16.135.87    openshift-node002
      172.16.135.88    openshift-infra001
      172.16.135.92    openshift-infra002
      172.16.135.95    openshift-internal.iot.com
      172.16.135.95    openshift-cluster.iot.com
      ```
   
   5. 执行ansible playbook进行安装(inventory/hosts.*文件请按照需求配置集群节点, 可参考官方文档中的样例)
      ```bash 
      cd~
      git clone https://github.com/openshift/openshift-ansible
      cd openshift-ansible
      git checkout release-3.10
      
      以下执行前先参考官方example列出的不同集群方式, 将文件放在inventory目录下, 并在执行时指定, 默认该目录下有单机版文件hosts.localhost
      sudo ansible-playbook -i inventory/example.localhost playbooks/prerequisites.yml
      sudo ansible-playbook -i inventory/example.localhost playbooks/deploy_cluster.yml
      ```

3. **生产级别硬件需求**
   1. 云平台集群配置(<1000 pod)

      | 节点 | CPU(cores) | 内存(GB) | 存储(GB) | 数量 | 备注 |
      |-----|------------|----------|---------|------|-----|
      | master | 4 | 16 | 160 | 3 | 控制器 |
      | etcd | 2 | 8 | 40 | 3 | 配置管理 |
      | infra | 2 | 8 | 1024 | 2 | 持久化存储GlusterFS, NFS |
      | ndoe | 16 | 64 | 145 | 8 | 预留10%, cpu1:10共享, 内存1:2共享 |
      | lb | 2 | 8 | 40 | 2 | 负载均衡vip, 带宽50M |

4. **基础配置**
   1. openshift-master节点image registry的配置
      ```bash 
      sudo vi /etc/origin/master/master-config.yaml
      
      imagePolicyConfig:
        internalRegistryHostname: docker-registry.default.svc:5000
        allowedRegistriesForImport:
        - domainName: registry.cn-shanghai.aliyuncs.com
        - domainName: docker.io
        - domainName: dockerhub.iot.com:5000
            insecure: true
        - domainName: 172.16.135.152:5000
            insecure: true
      ```
      
   2. 所有节点dns的配置
      ```bash 
      默认所有节点pod都将使用所在的host作为dns地址, 并不会讲host的dns拷贝到容器内, 所以所有节点需要安装dnsmasq, 并默认启动, 外部dns server配置到dnsmasq的配置文件中即可
      sudo vi /etc/dnsmasq.conf
      
      注意server的地址最多三个, 企业业务dns最好仅配置一个
      server=x.x.x.x
      
      systemctl restart dnsmasq
      ```

   3. docker的配置
      ```bash 
      sudo vi /etc/docker/daemon.json
      ```
      
   4. gitlab的配置
      ```bash 
      Generate the PEM or DER encoded public certificate from your private key certificate.
      Copy the public certificate file only into the /etc/gitlab/trusted-certs directory.
      Run gitlab-ctl reconfigure.
      ```
      
   5. 反序列化pod缓存, 如果pod<1000, 建议设置为1000, 默认为50000, 越大越占用master内存
      ```bash 
      sudo vi /etc/origin/master/master-config.yaml
      
      kubernetesMasterConfig:
        apiServerArguments:
          deserialization-cache-size:
          - "1000"
      ```
      
   6. route域名后缀配置
      ```bash 
      sudo vi /etc/origin/master/master-config.yaml
      
      routingConfig:
        subdomain: openshift-cluster.com
      ```   

5. **租户(namespace)**
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
      
6. **持久化存储(pv)**
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
      
7. **链接构建**
   1. 编译型语言第一次构建可执行程序(比如jar), 这个构建过程可能有很多依赖要下载, 很多环境配置要满足, 所以该镜像不建议直接运行
      ```bash 
      build镜像一般进行环境初始化, 依赖下载等, 比较占用空间
      ```
      
   2. 第二次构建从第一次构建的镜像中读取二进制程序(比如jar)用于运行时, 该镜像仅需要安装运行时环境, 所以该镜像作为运行时镜像
      ```bash 
      runtime镜像一般执行启动命令
      ```
      
   3. python, nodejs等解释型语言可以直接在构建环境进行运行

8. **Java项目链接构建**
   1. 构建jar
      ```bash 
      docker pull wildfly14-openshift:latest
      这个镜像建议使用源码编译制作, 以便随时修改编译环境, 比如创建授权某个目录等
      ```
      
   2. 构建runtime
      ```bash 
      docker pull openjdk18-openshift:latest
      这个镜像没有源码, 可以根据日志排查问题, 比如更改JVM参数
      ```
      
   3. 部署时指定jvm参数
      ```bash 
      根据源码shell可以看出, 默认JVM参数会加载pod的memory, 以及default配置, 其中default配置-XX:MaxMetaspaceSize=100m非常小, 如果需要覆盖需要指定该变量JAVA_OPTS_APPEND, 另外从源码中可以看出JAVA_OPTIONS这个变量加载时会被default覆盖, 这里需要注意
      ```
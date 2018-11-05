# Openshift

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
      export MINISHIFT_GITHUB_API_TOKEN = <token_ID>
      ```
      
   3. 下载Minishift安装包，解压并配置到环境变量中(必须)
      ```bash
      export PATH="$(pwd)":$PATH
      ```
   
   4. 执行安装命令
      ```bash
      minishift start
      ```
      
2. **Openshift-ansible集群安装**
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
      sudo yum install -y java-1.8.0-openjdk-headless
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
      
3. **Openshift-cluster基础配置**
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
      
   2. opneshift-master节点dns的配置
      ```bash 
      sudo vi /etc/dnsmasq.d/origin-upstream-dns.conf
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
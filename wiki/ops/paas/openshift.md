# Openshift

1. **Minishift**

   1. 安装KVM驱动
      ```bash 
      # curl -L https://github.com/dhiltgen/docker-machine-kvm/releases/download/v0.7.0/docker-machine-driver-kvm -o /usr/local/bin/docker-machine-driver-kvm
      # chmod +x /usr/local/bin/docker-machine-driver-kvm
      # yum install libvirt qemu-kvm
      # systemctl start virtlogd
      # systemctl enable virtlogd
      # systemctl start libvirtd
      # systemctl enable libvirtd
      ```
   
   2. 配置github token(Minishift会去github上验证，并调用其api，不配置token将没有api权限)
      ```bash
      # export MINISHIFT_GITHUB_API_TOKEN = <token_ID>
      ```
      
   3. 下载Minishift安装包，解压并配置到环境变量中(必须)
      ```bash
      # export PATH="$(pwd)":$PATH
      ```
   
   4. 执行安装命令
      ```bash
      # minishift start
      ```
      
2. **Openshift-ansible**
   1. 安装基础包
      ```bash 
      # ssh-keygen
      # sudo yum install -y wget git net-tools bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct
      # sudo yum update -y
      ```
      
   2. 安装docker
      ```bash 
      # sudo yum install -y docker-1.13.1
      # sudo systemctl enable docker
      ```
      
      ```bash 
      sudo tee /etc/docker/daemon.json <<-'EOF'
      {
        "registry-mirrors": ["https://31f62odg.mirror.aliyuncs.com"]
      }
      EOF
      sudo systemctl daemon-reload
      sudo systemctl restart docker
      ```
      
   3. 安装ansible(版本必须2.6.5，官方项目明确说明Ansible >= 2.6.5, Ansible 2.7 is not yet supported and known to fail)
      ```bash 
      # sudo yum install -y https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/ansible-2.6.5-1.el7.ans.noarch.rpm
      # sudo yum install -y pyOpenSSL python-cryptography python-lxml
      # sudo yum install -y java-1.8.0-openjdk-headless
      # reboot
      ```
   
   4. 执行ansible playbook进行安装
      ```bash 
      # cd~
      # git clone https://github.com/openshift/openshift-ansible
      # cd openshift-ansible
      # git checkout release-3.10
      
      # sudo ansible-playbook -i inventory/hosts.localhost playbooks/prerequisites.yml
      # sudo ansible-playbook -i inventory/hosts.localhost playbooks/deploy_cluster.yml
      ```
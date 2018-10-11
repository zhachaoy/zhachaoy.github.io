# OKD

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
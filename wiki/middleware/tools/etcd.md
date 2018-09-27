# etcd

1. 使用etcdctl管理etcd集群时，OPTIONS的参数需要配置在环境变量中，如果这些参数直接放在命令后面可能会出现某些命令解析失败
```bash
export ETCDCTL_API=3
export ETCDCTL_DIAL_TIMEOUT=3s
export ETCDCTL_CACERT=/tmp/ca.pem
export ETCDCTL_CERT=/tmp/cert.pem
export ETCDCTL_KEY=/tmp/key.pem
```

2. 使用etcdctl get 命令输出时，可以指定格式，但必须小写
```bash
etcdctl get [key] --w json json 
etcdctl get [key] --w json fields
etcdctl get [key] --w json simple
etcdctl get [key] --w json protoful
   ```
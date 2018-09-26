# kubernetes

## etcd

1.使用etcdctl管理etcd集群时，OPTIONS的参数需要配置在环境变量中，如果这些参数直接放在命令后面可能会出现某些命令解析失败
```export
ETCDCTL_API=3
ETCDCTL_DIAL_TIMEOUT=3s
ETCDCTL_CACERT=/tmp/ca.pem
ETCDCTL_CERT=/tmp/cert.pem
ETCDCTL_KEY=/tmp/key.pem
```

2.使用etcdctl get 命令输出时，可以指定格式，但必须小写
```--write-out json
--write-out json json 
--write-out json fields
--write-out json simple
--write-out json protoful
```
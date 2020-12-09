# nginx

1. **场景**
   > 正向代理：一般用于内部节点访问外部服务的代理中转

   > 反响代理：一般用于服务路有，负载均衡

2. **配置**
   ```
   /etc/resolv.conf
   search default.svc.cluster.local svc.cluster.local cluster.local ...
   ```

   > http server location
   > rewrite 重定向
   > proxy_pass 反向代理


3. **错误**
   ```
   错误：failed (13: Permission denied) while connecting to upstream
   关闭selinux
   setenforce 0  
   ```

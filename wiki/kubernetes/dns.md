# DNS

1. **全限定域名FQDN**
   > backend-database.default.svc.cluster.local

2. **POD配置**
   ```
   /etc/resolv.conf
   search default.svc.cluster.local svc.cluster.local cluster.local ...
   ```
3. **虚拟IP**
   > 无法ping通，需要结合端口使用,调试时注意
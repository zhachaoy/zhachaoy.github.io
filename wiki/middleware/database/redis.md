# Redis

1. Lua脚本支持与限制
   > 集群部署时, 确保所有key必须在一个slot上，否则返回错误信息
   ```
   -ERR eval/evalsha command keys must be in same slotrn
   ```
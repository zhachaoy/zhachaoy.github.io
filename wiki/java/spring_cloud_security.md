# spring cloud 安全认证

1.eureka-server配置认证信息, 启用后后服务注册时将会进行验证
```application.yml
security:
  basic:
    enabled: true
  user:
    name: eureka
    password: iot
```
2.config-server配置认证信息
```application.yml
security:
  user:
    name: admin
    password: 'admin'
```

3.client配置认证信息
```application.yml
security:
  user:
    name: admin
    password: 'admin'
```

# spring cloud

1.eureka配置认证信息，启用后后服务注册时将会进行验证
```application.yml
security:
  basic:
    enabled: true
  user:
    name: eureka
    password: iotbull
```
2.config配置认证信息
```application.yml
security:
  user:
    name: admin
    password: 'admin'
```

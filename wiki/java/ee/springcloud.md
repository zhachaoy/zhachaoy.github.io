# SpringCloud

1. 本地bootstrap.yml，引导配置信息，主要用于配置spring.application.name，spring.cloud.config，eureka，加密解密信息等
    ```bootstrap.yml
    spring:
      cloud:
        config:
          fail-fast: true
          discovery:
            service-id: paascloud-config-server
            enabled: true
          label: ${spring.profiles.active}
          profile: ${spring.profiles.active}
          username: admin
          password: admin
          
    eureka:
      client:
        serviceUrl:
          defaultZone: http://eureka:iot@paascloud-eureka:8761/eureka/
      instance:
        preferIpAddress: true
        instance-id: ${spring.application.name}:${spring.cloud.client.ipAddress}:${server.port}
    ```

2. 本地application.yml，定义spring.application.name（可选，一般与bootstrap.yml合并）
    ```application.yml
    server:
      port: 8000

    spring:
      application:
        name: @pom.artifactId@
        
      devtools:
        restart:
          enabled: false    
    ```

3. git仓库application*.yml，为所有客户端共享配置文件
    ```identity-dev.yml
    server:
      tomcat:
        uri-encoding: UTF-8
        remote-ip-header: x-forwarded-for
      use-forward-headers: true
    ```

4. git仓库[application]-[profile].yml，为指定client配置文件，[label]可以指定分支版本，支持复杂场景配置文件加载（多个，优先级）
    ```application.yml
    liquibase:
      change-log: classpath:liquibase/index.xml
      user: ${spring.datasource.username}
      password: ${spring.datasource.password}
      url: ${spring.datasource.url}
    ```

5. 安全认证
    1. eureka-server配置认证信息, 启用后后服务注册时将会进行验证
        ```application.yml
        security:
          basic:
            enabled: true
          user:
            name: eureka
            password: iot
        ```
    2. config-server配置认证信息
        ```application.yml
        security:
          user:
            name: admin
            password: 'admin'
        ```

    3. client配置认证信息
        ```application.yml
        security:
          user:
            name: admin
            password: 'admin'
        ```

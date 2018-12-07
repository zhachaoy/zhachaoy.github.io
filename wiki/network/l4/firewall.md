# Firewall

1. **Firewalld**
   1. 概念
      > Filewalld（动态防火墙）作为redhat7系统中变更对于netfilter内核模块的管理工具；
      
      > iptables service 管理防火墙规则的模式（静态）：用户将新的防火墙规则添加进 /etc/sysconfig/iptables 配置文件当中，再执行命令 /etc/init.d/iptables reload 使变更的规则生效。在这整个过程的背后，iptables service 首先对旧的防火墙规则进行了清空，然后重新完整地加载所有新的防火墙规则，如果加载了防火墙的模块，需要在重新加载后进行手动加载防火墙的模块；

      > firewalld 管理防火墙规则的模式（动态）:任何规则的变更都不需要对整个防火墙规则列表进行重新加载，只需要将变更部分保存并更新到运行中的 iptables 即可。还有命令行和图形界面配置工具，它仅仅是替代了 iptables service 部分，其底层还是使用 iptables 作为防火墙规则管理入口。firewalld 使用 python 语言开发，在新版本中已经计划使用 c++ 重写 daemon 部分。

   2. 配置文件地址
      > /etc/firewalld/

      ```
      文件： 
      firewalld.conf：主配置文件（只有5个配置项） 
        DefaultZone：默认使用的zone;
        MinimalMark：使用标记的最小值;
        CleanuupOnExit：退出后是否清除防火墙规则;
        Lockdown：是否限制别的程序通过D-BUS接口直接操作firewalld；
        IPv6_rpfilter：判断所接受到的包是否是伪造的
      
      lockdown-whitelist.xml：当Lockdown设置为yes时，规定哪些程序可以对firewalld进行操作
      
      direct.xml：直接使用类似iptables的语法来进行规则的增删
      
      目录： 
      zones：保存zone配置文件
      services：保存service配置文件
      icmptypes：保存和icmp类型相关的配置文件
      ```

      zone配置样例:
      ```
      <?xml version="1.0" encoding="utf-8"?>
      <zone>
      <short>Public</short>
      <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
      <service name="ssh"/>
      <service name="dhcpv6-client"/>
      </zone>
      ```

      service配置样例:
      ```
      <?xml version="1.0" encoding="utf-8"?>
      <service>
      <short>SSH</short>
      <description>Secure Shell (SSH) is a protocol for logging into and executing commands on remote machines. It provides secure encrypted communications. If you plan on accessing your machine remotely via SSH over a firewalled interface, enable this option. You need the openssh-server package installed for this option to be useful.</description>
      <port protocol="tcp" port="22"/>
      </service>
      ```

   3. 什么是区域zone
      ```
      所谓的区域就是一个信赖等级，某一等级下对应有一套规则集。划分方法包括：网络接口、IP地址、端口号等等。一般情况下，会有如下的这些默认区域：
      drop：丢弃所有进入的数据包
      block：拒绝所有进入的数据包
      public：只接受部分选定的数据包
      external：应用在NAT设定时的对外网络
      dmz：非军事区
      work：使用在公司环境
      home：使用在家庭环境
      internal：应用在NAT设定时的对内网络
      trusted：接受所有的数据包
      ```
   
   4. 什么是服务service
      > iptables时代习惯使用端口号来匹配规则，但是如果某一个服务的端口号改变了，那就要同时更改iptables的规则，十分不方便，同时也不方便阅读理解。
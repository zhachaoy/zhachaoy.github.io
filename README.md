# devops

it's a full featured wiki powered by git, github pages and pull-requests!

It means: 

* Improvements in the cooperative aspect: forks, pull-requests and roles.
* You can customize your wiki as you want with style sheets and even changing the layout.
* No databases! Only static files that can be downloaded in a few seconds.
* Markdown and html mixed together!
* History, revision comparison and everything you need from a wiki platform.
* You can edit your pages with the standard git editor, prose.io (integrated) or any kind of editor you prefer.
* Non-existent wiki page links are 

You can fork/copy the master branch now and start your wiki in just 1 minute.

**Note:**
You can even include the github wiki as a submodule and enable the conf, but it's an experimental feature and it implies less advantages and greater disadvantages for now.

## basic build

- [packer](wiki/java/spring_cloud_config.md)

- [vagrant](wiki/java/spring_cloud_config.md)

## iac

provider provision env (org-buss-app)
- [terraform](wiki/java/spring_cloud_config.md)

- [cloudformation](wiki/java/spring_cloud_config.md)

- [ros](wiki/java/spring_cloud_config.md)

**Note:**

We suggest the creation of a /wiki/ subfolder that collects all your .md pages (except index.md)

## container

* You can't use the wiki internal link format: [[example]]. Please, use gh-pages links instead: \[example\](example) . It's a known jekyll limitation: <https://jekyllrb.com/docs/templates/>

- [docker](wiki/java/spring_cloud_config.md)

- [mesos](wiki/java/spring_cloud_config.md)

- [kubernetes](wiki/java/spring_cloud_config.md)

## env config

You can create following files in _includes folder to costumize git-wiki without patching original code:

* head.html  -> this file will be included in <head> tag allowing you to add **css/js** and any kind of head tags
* sidebar.html -> this file will be included in left sidebar allowing you to create your widgets
* comments.html -> this is mostly used to integrate social comments under page contents
* footer.html -> this file will be included in left side of the footer.

- [cloud-init](wiki/java/spring_cloud_config.md)

- [ansible](wiki/java/spring_cloud_config.md)

- [chef](wiki/java/spring_cloud_config.md)

- [puppet](wiki/java/spring_cloud_config.md)

## jenkins

# 运维

## 运维体系

文档, 流程
- [标准化](wiki/java/spring_cloud_config.md)

脚本库, 工具库
- [工具化](wiki/java/spring_cloud_config.md)

统一web管理控制，提供标准API
- [平台化](wiki/java/spring_cloud_config.md)

按照场景智能管理, 态势感知
- [智能化](wiki/java/spring_cloud_config.md)

## 运维模式

宠物模式, 随着服务增加管理成本也会增加
- [pet](wiki/java/spring_cloud_config.md)

牲口模式, 很好的处理服务增加成本
- [cattle](wiki/java/spring_cloud_config.md)

## iaas

- [openstack](wiki/java/spring_cloud_config.md)

- [vmware vsphere](wiki/java/spring_cloud_config.md)

- [公有云](wiki/java/spring_cloud_config.md)

## paas

# 开发

## 架构

- [servicemesh](wiki/java/spring_cloud_config.md)

- [微服务](wiki/java/spring_cloud_config.md)

## 编码规范

## 版本控制

- [git](wiki/java/spring_cloud_config.md)

## 单元测试

# linux

## cpu

- [运行级别](wiki/java/spring_cloud_config.md)

- [上下文](wiki/java/spring_cloud_config.md)

- [运行队列](wiki/java/spring_cloud_config.md)

- [进程调度](wiki/java/spring_cloud_config.md)

- [系统调用](wiki/java/spring_cloud_config.md)

- [cpu管理](wiki/java/spring_cloud_config.md)

## 内存

- [虚拟内存](wiki/java/spring_cloud_config.md)

- [swap换入换出](wiki/java/spring_cloud_config.md)

- [内存寻址](wiki/java/spring_cloud_config.md)

- [内存管理](wiki/java/spring_cloud_config.md)

## i/o(磁盘)

## i/o(网络)

- [tpc/ip](wiki/java/spring_cloud_config.md)

- [io模型](wiki/java/spring_cloud_config.md)

## 内核/shell

# 网络

## l2

## l4

## dns

## proxy

## qos

## cdn

# 中间件

## db

## mq 

## slb

# python

## keystone

## netbox

# java

## spring cloud

- [配置文件](wiki/java/spring_cloud_config.md)

- [安全认证](wiki/java/spring_cloud_security.md)

## mybatis



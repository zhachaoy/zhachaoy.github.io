# devops

This is a [demo](wiki/Demo.md) of git-wiki theme for jekyll.

it's a full featured wiki powered by git, github pages and pull-requests!

It means: 

* Improvements in the cooperative aspect: forks, pull-requests and roles.
* You can customize your wiki as you want with style sheets and even changing the layout.
* No databases! Only static files that can be downloaded in a few seconds.
* Markdown and html mixed together!
* History, revision comparison and everything you need from a wiki platform.
* You can edit your pages with the standard git editor, prose.io (integrated) or any kind of editor you prefer.
* Non-existent wiki page links are "[red](wiki/red.md)"

You can fork/copy the master branch now and start your wiki in just 1 minute.

**Note:**
You can even include the github wiki as a submodule and enable the conf, but it's an experimental feature and it implies less advantages and greater disadvantages for now.

## basic build

[List of git-wiki installations](wiki/examples.md)

[List of forked repository](https://github.com/Drassil/git-wiki/network/members)

### packer

### vagrant

## iac

### terraform
provider provision env (org-buss-app)

### cloudformation

### ros

**Note:**

We suggest the creation of a /wiki/ subfolder that collects all your .md pages (except index.md)

## container

* You can't use the wiki internal link format: [[example]]. Please, use gh-pages links instead: \[example\](example) . It's a known jekyll limitation: <https://jekyllrb.com/docs/templates/>

### docker

### mesos

### kubernetes

## env config

You can create following files in _includes folder to costumize git-wiki without patching original code:

* head.html  -> this file will be included in <head> tag allowing you to add **css/js** and any kind of head tags
* sidebar.html -> this file will be included in left sidebar allowing you to create your widgets
* comments.html -> this is mostly used to integrate social comments under page contents
* footer.html -> this file will be included in left side of the footer.

### cloud-init

### ansible

### chef

### puppet

## jenkins

# 运维体系
Do you like this project? then, contact us via [chat](https://gitter.im/Drassil/general?utm_source=share-link&utm_medium=link&utm_campaign=share-link) , <a href="mailto:staff-drassil@googlegroups.com">email</a>  or send us a PR to improve it.

Thank you!

## 标准化
文档, 流程

## 工具化
脚本库, 工具库

## 平台化
统一web管理控制
提供标准API

## 智能化
按照场景智能管理, 态势感知

# iaas

- [jekyll-table-of-contents](https://github.com/ghiculescu/jekyll-table-of-contents)

- [jQuery](https://jquery.com/)


[MIT LICENSE](LICENSE)

## openstack

## vmware vsphere

## 公有云

# paas

# linux

# network

## l2

## l4

## load balance

## proxy

# python

## keystone

## netbox

# java

## spring cloud
### 认证相关
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

## mybatis



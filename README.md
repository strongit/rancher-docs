# Rancher 实战红宝书

Rancher 是非常优秀的容器管理平台，本书旨在沉淀和积累相关实战文档。本书通过 GitHub 和 GitBook 平台共同承载，并实现参与者的协作。本书使用开源社区的方式，由Rancher 用户社区合作维护。

参考源： http://docs.rancher.com

本书在线访问网址：http://rancher.hidocker.io   

本书的初始化版本，保持了和 Rancher 文档官网同步的目录结构；为了方便您的翻译，建议直接下载 https://github.com/rancher/rancher.github.io 英文源站到本地，在 Forke 下载本书源码 https://github.com/martinliu/rancher-docs 到本地，您会看到本书的很多章节还都是空白文章；然后您就可以可以开始工作了。本书的第一个阶段性目标是：对官方英文文档做 1：1的翻译。然后是收集各位高手的原创好文。目标是打造出一份很实用和全面的参考资料库。

如果你没有 GitHub 的使用经验，请查看这个视频，否则忽略此段，继续仔细阅读本网页；视频会教会您纯网页版的操作和协作流程，其中示例了如何把 6.1 访问控制 做英文原文的初始化，和翻译提交。视频点这里。[http://v.youku.com/v_show/id_XMTU1NDk4Mzc2OA](http://v.youku.com/v_show/id_XMTU1NDk4Mzc2OA)

<iframe height=498 width=510 src="http://player.youku.com/embed/XMTU1NDk4Mzc2OA==" frameborder=0 allowfullscreen></iframe>

Github 是一个协作的平台，pull request 是解决冲突的方式，你提交的 pull request 上来以后，如果有冲突，在这种情况下电子书源码的管理员需要解决这个冲突，从两个冲突的 pull request 中选择出一个更好的。为了快速响应所有文档参与者的贡献，现在征召电子书源码仓库管理员合作者（Collaborators）；如果您提交的 PR 被三次接受，你就可以申请成为 Collaborators ；请发一个 Issue 做出申请，没有人反对的话，2天内就不会成为 Collaborators。

## 贡献者
本书源码开源托管在Github，欢迎参与维护：https://github.com/martinliu/rancher-docs， 贡献者名单见[ GitHub contributors](https://github.com/martinliu/rancher-docs/graphs/contributors) 页面。

欢迎参与线上讨论。

* QQ 群1 
![314700162](q1.png)
* 群名称：Rancher实战红宝书群1
* 群号：314700162

为了您能顺利领取到相关纪念品（首批为 Rancher logo 定制版 Tshirt），请加入以上 QQ 群，并用 Github ID 备注自己的姓名。


## 参与协作

为了提高参与的效率，并省去在 Gitbook 上的操作的步骤。请直接加入本书在 GitHub 上的源代码协作来参与到文档的维护中来。

参与步骤：注册 Github 账号，克隆本书源码，下载到本机，编辑认领的文章，推送到自己的仓库，提交 pull request 到本书源码，PR 通过之后，您的贡献自动跟新到线上版本。

当前的工作目标：翻译并优化 Rancher 官方文档为中文版本。

参与者就有机会赢取各种纪念品。详情见 http://hidocker.io/book/

### 具体操作步骤

在 GitHub 上 fork 到自己的仓库，如 docker_user/rancher-docs，然后 clone 到本地，并设置用户信息。

```
$ git clone git@github.com:martinliu/rancher-docs.git
$ cd rancher-docs
$ git config user.name "yourname"
$ git config user.email "your email"

```
修改代码后提交，并推送到自己的仓库。

```
$ #do some change on the content 修改您参与的文章
$ git commit -am "Fix issue #1: change helo to hello"
$ git push

```
在 GitHub 网站上提交 pull request。

定期使用项目仓库内容更新自己仓库内容。

```
$ git remote add upstream https://github.com/martinliu/rancher-docs
$ git fetch upstream
$ git checkout master
$ git rebase upstream/master
$ git push -f origin master

```
或者使用 GitHub Desktop 图形化客户端完成以上所有操作，推荐使用 [Atom](http://atom.io) 作为本地 .mk 文件编辑器。


### 添砖加瓦

目前的章节目录源于 Rancher 官方文档网站。如果您希望增加你原创实战文章，请在 github 上提交一个 Issue ,提交网址  https://github.com/martinliu/rancher-docs/issues ; 在提交 Issue 的内容中说明所加入文章的内容梗概，标题，和希望加入的位置。2天内无人反对，您就可以在源码中看到您的空白文章已经生成。更新到您本地，编辑并上传，最后完成 RP 即可。

本书为开放、开源的编辑和协作，如果您希望加入在 GitBook 中本书的协作，请在 Issue 出提交加入 GitBook 协作的申请，并在内容中填写，您的 Gitbook ID。2天内无人反对，您就会收到 Gitbook 的邀请。


## 文档协作

文档章节数目前是： 126 行。

请在您所贡献的章节处 [ X.X.X 章节名称 ] 新建表格的一行，来标记您参与了这篇文章的工作。

请用表格的Markdown 语法更新以下文字，目标是认领所要贡献的章节，并汇报当前的进展。如果您改写了某篇文章，并没有人反对您的更新，您可以在贡献者这一列，加入您的姓名。 

| 章节编号 | 贡献者 | 完成度% |
| -- | -- | -- |
| 1 Rancher 概述 | Martin Liu | 75% |
| 2 快速安装指南 | Martin Liu | 5% |
| 3 Rancher 安装 |Xiaobao Zhang | 5% |
| 3.1 单节点服务器 |Xiaobao Zhang | 5% |
| 3.2 多节点高可用 |Xiaobao Zhang | 5% |
| 3.3 基本 SSL 配置 |Xiaobao Zhang | 5% |
| 3.4 无互联网访问 |Xiaobao Zhang | 5% |
| 3.5 添加主机 |Xiaobao Zhang | 5% |
| 4 基本概念 | Jitao Hou | 100% |
| 5 Rancher 基础服务 | Sammy Lin | 0% |
| 5.1 健康检查 | Sammy Lin | 0% |
| 5.2 内部 DNS 服务 | Sammy Lin | 0% |
| 5.3 使用 Route 53 做外部 DNS | Sammy Lin | 0% |
| 5.4 元数据服务 | Sammy Lin | 0% |
| 5.5 存储服务 | Sammy Lin | 0% |
* 6 系统配置
* 6.1 访问控制
* 6.2 设置
* 6.3 账户
* 6.4 环境
* 6.5 API & Keys
* 6.6 镜像仓库
* 7 使用 Docker 原生命令行
* 8 使用 Rancher-Compose
* 8.1 使用 Rancher Compose
* 8.2 命令和选项
* 8.3 Rancher 服务
* 8.4 调度
* 8.5 升级服务
* 8.6 环境插值
* 8.7 构建使用 AWS S3存储
* 9 Rancher 图形界面
* 9.1 调度服务
* 9.2 服务/堆栈
* 9.2.1 增加服务
* 9.2.2 增加服务别名
* 9.2.3 增加负载均衡
* 9.2.4 增加外部服务
* 9.2.5 堆栈选项
* 9.3 基础架构/主机
* 9.3.1 从主机开始
* 9.3.2 添加自定义主机
* 9.3.3 Amazon EC2
* 9.3.4 Azure
* 9.3.5 Digital Ocean
* 9.3.6 Exoscale
* 9.3.7 Packet
* 9.3.8 Rackspace
* 9.3.9 其它驱动
* 9.4 基础架构/容器
* 9.5 基础架构/证书
* 10 Rancher 目录
* 11 Kubernets
* 12 Swarm
* 13 使用标签
* 14 升级 Rancher
* 15 为 Rancher 做贡献
| 16 使用 API | wangweihong | 5% |
| 16.1 如何使用 API | wangweihong | 5% |
| 16.2 常用资源字段 | wangweihong | 5% |
| 16.3 API 资源 | wangweihong | 5% |
* 18 Rancher OS
* 18.1 快速安装指南
* 18.2 运行 Rancher OS
* 18.2.1 工作站
* 18.2.1.1 Docker Machine
* 18.2.1.2 Vagrant
* 18.2.1.3 从 ISO 启动
* 18.2.2 公有云
* 18.2.2.1 AWS
* 18.2.2.2 GCE
* 18.2.2.3 Azure
* 18.2.3 裸金属机和虚拟机
* 18.2.3.1 iPXE
* 18.2.3.2 PXE
* 18.2.3.3 安装到硬盘
* 18.3 配置
* 18.3.1 配置存储
* 18.3.2 网络
* 18.3.3 用户
* 18.3.4 SSH Keys
* 18.3.5 控制台输出
* 18.3.6 系统服务
* 18.3.7 设置 Docker TLS
* 18.3.8 加载内核模块
* 18.3.9 安装内核模块
* 18.3.10 DKMS
* 18.3.11 自定义内核
* 18.3.12 构建自定义 RancherOS ISO
* 18.3.13 Docker
* 18.3.14 定制 Docker
* 18.3.15 预打包 Docker 镜像
* 18.4 公有云配置参考
* 18.5 升级
* 18.6 ROS 工具
* 18.6.1 ROS 简介
* 18.6.2 Config
* 18.6.3 TLS
* 18.6.4 OS
* 18.6.5 Service
* 18.6.6 ENV
* 18.6.7 Install
* 18.7 Amazon ECS
* 18.8 底层技术
* 18.8.1 目录加载
* 18.9 为 RancherOS 做贡献
* 18.10 Rancher 配合Rancher OS 的使用技巧
* 17 常见问题
* 17.1 Rancher Server
* 17.2 Rancher Agent/Host
* RancherOS
* 17.3 排错



## 主要版本历史

* v 0.5 完成了第一版全目录，章节条目为 126 行，更新了 Readme
* 
* 



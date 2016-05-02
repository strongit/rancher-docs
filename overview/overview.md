## Rancher 概述
---


Rancher 是以在生产环境中运行容器为目标而构建的开源软件平台。随着 Docker 容器这种类型的应用工作负载的逐渐流行，它催生了很多与之相应的基础架构服务，如网络服务、存储服务、负载均衡，安全，服务发现和资源管理。

### 计算资源

Rancher 使用的是来自于公有云或私有云上 Linux 主机的裸计算资源。每一个 Linux 主机既可以是虚拟机，也可以是物理机。Rancher 对每一个主机的期望不会多于 CPU，内存，磁盘存储和网络连接。从 Rancher 的角度看来，一个来自云服务商的云主机和私有数据中心的物理机是没多大差异。

### 关键功能

Rancher 产品的关键功能包括： 

1. 扩主机网络： Rancher 为每个环境生成一个软件定义网络，为扩主机和云的容器之间提供了安全的网络通讯。

2. 容器的负载均衡。Rancher 提供的内置、弹性负载均衡能在容器之间或者服务之间分发流量。负载均衡服务可以跨多个云工作。

3. 持久化存储服务： Rancher 对 Docker 提供持久化存储服务的编排，让开发者在部署容器化应用的同时可靠地部署与之相应的存储。这项新功能基于 Docker 1.9的卷插件功能，这让开发人员可以更加方便地运行需要有状态数据库和持久存储的应用。

4.	服务发现：Rancher 实现了分布式服务发现功能，具有内置的健康检查功能，并使容器自动地注册自己到相应至相应服务，并且各种服务之间可以在网络上动态地彼此发现。

5.	服务升级：通过使用服务克隆和请求重定向功能，Rancher 使用户能更加容易地升级以及存在的容器服务。这让新版本的服务在处理生产流量前，有机会在其所依赖的生产环境中被校验和确认。 

6.	资源管理：Rancher 支持 Docker Machine，这个强大的工具可以直接地对各种云提供商做主机部署。然后 Rancher 在对其做资源监控和容器部署管理。

7. 多租户和用户环境：Rancher 为多用户而设计，企业各个部门间可以跨应用生命周期协作。通过与已有目录服务的集成，Rancher 的用户可以创建独立的开发，测试和生产环境，然后邀请相关人员一起协作地管理资源和应用。

8. 多编排引擎支持：Rancher 用户在创建环境的时候，可以为他们的容器选择不同的容器编排引擎，默认是 Cattle，或者是 Kubernets 和 Docker Swarm。这让用户可以选择任意市场领先的调度框架的同时，依然能利用到Ranher 的其它所有功能，如：应用商店/目录，企业级用户管理，容器网络，和存储技术。

### 主要使用接口

用户有三种方式和 Rancher 交互：

1. Users can interact with Rancher through native Docker CLI or API. Rancher is not another orchestration or management layer that shields users from the native Docker experience. As Docker platform grows over time, a wrapper layer will likely be superseded by native Docker features. Rancher instead works in the background so that users can continue to use native Docker CLI and Docker Compose templates. Rancher uses Docker labels--a Docker 1.6 feature contributed by Rancher Labs--to pass additional information through the native Docker CLI.  Because Rancher supports native Docker CLI and API, third-party tools like Kubernetes work on Rancher automatically.
2. Users can interact with Rancher using a command-line tool called `rancher-compose`. The `rancher-compose` tool enables users to stand up multiple containers and services based on the Docker Compose templates on Rancher infrastructure. The `rancher-compose` tool supports the standard `docker-compose.yml` file format. An optional `rancher-compose.yml` file can be used to extend and overwrite service definitions in `docker-compose.yml`.
3. Users can interact with Rancher using the Rancher UI. Rancher UI is required for one-time configuration tasks such as setting up access control, managing environments, and adding Docker registries. Rancher UI additionally provides a simple and intuitive experience for managing infrastructure and services.

The following figure illustrates Rancher's major features, its ability to run any clouds, and the three primary ways to interact with Rancher.

<img src="{{site.baseurl}}/images/rancher_overview.png" width="800" alt="Rancher Overview">

### 本文档的线索

It is easy to get Rancher up and running. If you have access to a Linux VM on your laptop or in a cloud, go to the [Quick Start Guide]({{site.baseurl}}/rancher/quick-start-guide/) to get started right away.

If you are ready to set up a production-grade Rancher installation, follow the instructions in the [Installing Rancher]({{site.baseurl}}/rancher/installing-rancher/installing-server/) to setup a Rancher server and add hosts into the Rancher installation.

Before you start using Rancher, make sure to read through the [Concepts]({{site.baseurl}}/rancher/concepts/) section to understand how Rancher works.

The Configuration section documents how you perform various one-time tasks after you complete installation of Rancher and start using Rancher.

The next three sections--[Using Rancher Through Native Docker CLI]({{site.baseurl}}/rancher/native-docker/), [Rancher Compose]({{site.baseurl}}/rancher/rancher-compose), and [Rancher UI]({{site.baseurl}}/rancher/rancher-ui/applications/stacks/adding-services/)--covers three primary ways you can consume Rancher features.

The [Upgrading Rancher]({{site.baseurl}}/rancher/upgrading) section is essential if you run Rancher in production.

The [Contributing to Rancher]({{site.baseurl}}/rancher/contributing) section contains information on how you can participate in the Rancher open source community.

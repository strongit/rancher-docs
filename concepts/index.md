# 基本概念

在本章节，我们将介绍Rancher的基本概念。在使用Rancher之前，我们建议你对这些概念应该非常熟悉。  

## 用户

“用户”定义了什么角色在一个环境里能够拥有查看或管理Rancher资源的权限。Rancher缺省会允许单一租户的访问权限，当然，管理员可以定义多个用户的访问权限。 

在你激活鉴权（authenticaiton）之前，可参照 [访问控制]({{site.baseurl}}/configuration/access-control.html)  .


## 环境

所有的主机和任意的Rancher资源，例如容器、负载均衡器等，都创建并归属于一个环境。 用户对这些资源查看和管理的权限，由环境的拥有者来定义。Rancher目前支持每个用户来管理和邀请其它用户进入他的环境，并且能够为多种工作负载来定义不同的环境。 例如，你可以创建一个“Dev”环境， 另外再创建一个隔离的“生产”环境，每个环境里定义不同的资源，从而限制不同用户对不同环境的访问权限。 

在你希望和其它用户[共享环境]({{site.baseurl}}/configuration/environments.html/) 之前，请设置[访问控制]({{site.baseurl}}/configuration/access-control.html/)。

<a id="host"></a>

## 主机

在rancher环境里，主机是一个最基本的资源单元，可以是任意的虚拟或物理的Linux服务器，对这些服务器的要求如下:

* 任意的Linux 发行版，只要它支持Docker 1.10.3。
* 能够和Rancher服务器通过预定义的端口号，一般是8080， 以http或https的协议进行通讯。 
* 和同环境里的其它主机路由可达，从而能够利用Rancher的跨节点网络进行通讯。

Rancher也支持Docker Machine命令，从而允许你通过任意Rancher支持的接口来添加主机。 

在Rancher环境里添加主机操作之前，可参考[添加第一台主机]({{site.baseurl}}/rancher-ui/infrastructure/hosts.html)。

## 网络

Rancher通过使用IPsec隧道技术，构建了一个简单和安全的覆盖（Overlay）网络，来支持跨主机节点的容器通讯。如果希望使用该网络，容器在通过Rancher启动时，必须将其网络模式设为“Managed”，或着在Docker命令行启动时，提供额外的标签 "--label io.rancher.container.network=true"。大多数的Rancher网络功能，例如负载均衡或DNS服务，都需要容器设为“Managed”网络模式。 

在Rancher网络里， 一个容器将被分配一个Docker的桥接IP地址(172.17.0.0/16) 和一个Rancher管理的IP地址 (10.42.0.0/16) ，该地址配置在缺省的docker0桥上.  通过这样的设置， 在同一个环境里的所有容器将都能够通过“managed”网络实现路由可达和互相访问。

**_备注:_** _Rancher的管理IP地址在Docker meta-data
里是不存在的，所以通过Docker的“inspect”指令是看不到的。有些时候这一点可能会和一些需要Docker桥接IP地址的工具不兼容。我们正在和Docker社区共同合作，确保Docker的未来版本更清晰地支持Overlay网络。 

## 服务发现

Rancher采用标准的Docker Compose规范来描述服务，一个或多个来自于同一个Docker镜像的容器可被定义为一个基本服务。在同一个应用栈里，当一个服务（消费者）被链接到另外一个服务（生产者）时，会自动产生一条DNS记录，来匹配每个容器的实例。该记录使得服务（生产者）能够被访问到。在Rancher中创建服务的其它益处包括： 

* 服务高可用 (HA) - 可以使Rancher来自动监控容器的状态，从而确保服务内的启动的容器维持在预定的数量。
* 健康检查 - 设定阈值，来监控容器的健康状况。 
* 添加负载均衡器 - 为服务添加一个基本的负载均衡器，该负载均衡器使用HAProxy。
* 添加外部服务 - 可以添加任意的IP地址做为可以使用和访问到的服务。 
* 添加服务别名 - 可以添加一条DNS记录，使得服务可以被访问到。 

更多信息，请参考 [添加服务]({{site.baseurl}}/rancher-ui/applications/stacks/adding-services.html/), [添加负载均衡器]({{site.baseurl}}/rancher-ui/applications/stacks/adding-balancers.html/), [添加外部服务]({{site.baseurl}}/rancher-ui/applications/stacks/adding-external-services.html/) 或者 [添加服务别名]({{site.baseurl}}/rancher-ui/applications/stacks/adding-service-alias.html/).

## 负载均衡器

Rancher使用HAProxy来构建一个被管理的负载均衡器，该负载均衡可以被定义部署到多台主机上。一个负载均衡器可用来将网络和应用流量分配到多个单个容器里，只需要将这些容器添加到与负载均衡器相链接到服务里。 这个链接到负载均衡器的服务，可以由Rancher自动注册为负载均衡的目标服务。 

## 分布式DNS服务 

Rancher本身内置具备高可用控制引擎的轻量级DNS服务，利用该服务，Rancher构建了一个分布式DNS服务。每个健康的容器被链接到一个服务或者被添加到一个服务别名的时候，该容器会被自动添加到DNS记录里的该服务中。当查询该服务名时，DNS服务会返回该服务中所有容器的随机IP列表。

* 缺省情况下, 在同一应用栈里的所有服务不需要显式的链接定义，就会被添加到DNS服务中。  
    * 你可以通过服务名称来解析同一应用栈的容器地址。  
    * 如果你的服务需要一个自定义的DNS名称，该名称与原服务名称不同，你将需要一个链接来得到这个自定义的DNS名称。  
    * 对负载均衡来说，仍旧需要链接定义才能访问到目标服务。 
    * 如果定义了服务别名，链接就仍旧时必须定义的。  
* 如果想使得在不同应用栈的服务可以被解析，你需要显式地定义链接。 

因为Rancher的覆盖（overlay）网络为每个容器定义了一个明确的IP地址，你不需要再操心处理端口映射，也不需要处理类似同一服务下的容器使用不同的端口这样的复杂情况。这样，利用一个基本的DNS服务就足可以实现服务发现。  

## 健康检查

Rancher构建了一个完善的容器健康状况监测系统。该系统通过在多个主机上运行的网络代理，来实施对容器和服务的分布式健康状况检查。这些网络代理使用内置的HAProxy来验证应用的健康状态。当一个容器或一个服务定义了健康检查时，每个容器就会被最多三个网络代理程序来监控，这些网络代理程序运行在不同于该容器父节点的其它三个物理节点上。只有当至少一个HAProxy的实例表明健康检查“通过”时，该容器才会被认为是健康的。  

> **备注:** 一个例外情况是，当你的环境里只包含了一个单一的主机时，健康检查就会被同一个主机节点上的网络代理来执行。 

Rancher的去中心化的健康检查模式比客户端模式要有效得多，另外，使用HAProxy来进行健康检查，那些原先只能在负载均衡器定义的应用级别负载均衡策略，都可以在服务里进行定义，从而实现在各层次上去监控应用和服务的健康状况。  

更多信息，例如健康检查失败的不同场景以及Rancher如何显示服务，请参考 [健康检查]({{site.baseurl}}／rancher-services/health-checks.html/). 你也可以获取更多信息关于如何通过使用 [rancher-compose]({{site.baseurl}}/rancher-compose/rancher-services/#health-check-for-services.html) 或 在[UI]({{site.baseurl}}/rancher-ui/applications/stacks/adding-services/#health-checks.html)里来设置健康检查.

## 服务高可用

Rancher会持续地监控服务里容器的状态，并且主动地进行管理，确保服务保持在期望的规模。当健康的容器数量比服务里原先定义的数量少（或者甚至多）的情况下，系统会自动触发动作，启动（或关闭）容器。 通常这种情况是由于一个节点不可用，一个容器失败或者容器／服务没有通过健康检查。

## 服务升级

Rancher支持服务的部分（notion）升级，该功能是通过允许用户为某个给定的服务来使用负载均衡或者应用服务别名来实现的。这两种方式，都可以为需要该服务的现有负载关键一个静态的目标。一旦该设定建立，某个底层的服务可以被Clone为一个新服务，通过隔离的测试来进行校验，当就绪时，通过负载均衡或者定义服务别名的方式加入到应用栈。 而原有到服务当废弃时可以在系统中删除。最终，所有的网络或应用流量都将被分流到新的服务。  

## Rancher Compose

Rancher实施和发布了一个命令行工具，该工具称为“rancher-compose”，与docker-compose的命名类似. 该工具可以引用同样的docker-compose.yml模版将应用栈在Rancher中部署。Rancher-compose的工具还支持rancher-compose.yml文件，该文件扩展了docker-compose.yml的定义，允许定义更多的属性，如容器规模, 负载均衡策略，健康检查策略以及外部链接等，这些定义目前在docker-compose里还不能定义.

更多信息, 请参见 [rancher-compose]({{site.baseurl}}/rancher-compose.html/).

## 应用栈（Stacks）

Rancher应用栈与docker－compose所定义的项目概念类似。 它代表着一组服务，来构成一个典型的应用或工作负载。  

## 容器调度

Rancher支持容器调度策略，采用与Docker Swarm兼容的模型。 包括如下策略： 

* 端口冲突 
* 共享存储卷 
* 主机标签
* 共享网络栈: --net=container:dependency
* 严格的和软性定义的亲和／反－亲和规则，这些规则通过使用env变量(Swarm)和 label(Rancher)来定义。

另外， Rancher支持调度服务触发器，允许用户定义特定的规则，如“添加主机”或“主机标签” ，从而实现自动地将服务扩展到具有特定标签到主机上。  

关于如何调度容器到更多信息，请参考如何使用[UI的labels 和 调度规则]({{site.baseurl}}/rancher-ui/scheduling.html/) 或者如何使用[labels in rancher-compose]({{site.baseurl}}/rancher-compose/scheduling.html/).

## Sidekicks

Rancher允许用户通过定义Sidekicks来定义一组容器，这组容器总是运行在同一主机节点上、同时被调度以及同时被伸缩。 一个服务拥有一个或多个sidekick容器，一个典型的例子是用来在多个容器里支持存储卷 (即`--volumes_from`) 和网络 (即 `--net=container`) 的定义.

更多信息, 请参考[sidekicks with rancher-compose]({{site.baseurl}}/rancher-compose/#sidekicks.html).


## 基本概念（Concepts）
---

在这个章节，我们将介绍Rancher中的重要概念。在尝试使用Rancher之前你需熟练掌握这些概念：

*In this section, we introduce the key concepts in Rancher. You should be familiar with these concepts before attempting to use Rancher.*

####  用户（USERS）

用户管理指的是在特定环境内拥有查看和管理Rancher资源的访问权限，Rancher默认允许单用户，当然也支持多用户模式。

*Users govern who has the access rights to view and manage Rancher resources within their Environment. Rancher allows access for a single tenant by default. However, multi-user support can also be enabled.*

在启用身份验证之前，请详读访问控制章节。

*See access control before you enable authentication.*


####  环境变量（ENVIRONMENTS）

所有的主机、Rancher资源，包括容器、负载均衡器等，均需要在创建时拥有环境变量。查看和管理这些资源的访问控制权限，取决于环境变量的所有者。目前，Rancher支持为每一个用户管理环境变量和邀请其他用户到自身变量中，支持为不同的工作负载创建不同的环境变量。例如，您可能希望创建一个“dev”的环境，和一个拥有自身资源及限制性权限的隔离应用开发环境。

*All hosts and any Rancher resources, such as containers, load balancers, and so on are created in and belong to an environment. Access control permissions for viewing and managing these resources are then defined by the owner of the environment. Rancher currently supports the capability for each user to manage and invite other users to their environment and allows for the ability to create multiple environments for different workloads. For example, you may want to create a “dev” environment and a separate “production” environment with its own set of resources and limited user access for your application deployment.*

请在与其他用户共享环境变量之前设置访问权限。

*Set up access control before you share environments with users.*


####  主机（HOSTS）

主机资源是Rancher最基本的资源单位，表现为任何Linux服务器，虚拟机或物理机，需要以下最低要求：

*Hosts are the most basic unit of resource within Rancher and is represented as any Linux server, virtual or physical, with the following minimum requirements:*

> **任何现有Linux发行版都要能支持Docker1.10.3**

> **能够以http或https的方式通过前期配置好的端口与Rancher通讯，默认为8080.**

> **能够利用Rancher专为Docker容器提供的跨主机网络，路由到其他任何相同环境的主机**

> **Rancher也支持Docker Machine机制，可以让你通过任何支持的驱动程序添加主机**


> *Any modern Linux distribution that supports Docker 1.10.3.*

> *Ability to communicate with a Rancher server via http or https through the pre-configured port. Default is 8080.*

> *Ability to be routed to any other hosts under the same environment to leverage Rancher’s cross-host networking for Docker containers.*

> *Rancher also supports Docker Machine and allows you to add your host via any of its supported drivers.*


在添加主机到Rancher之前先加入你的第一台主机。

*See add your first host before adding your first host to Rancher.*

####  网络（NETWORKING）

Rancher支持跨宿主机容器通信，需利用IPSec隧道实现简单安全的大二层网络。为了利用这种特性，通过Rancher启动的容器必须选择"Managed"的网络模式，或者通过Docker启动时提供参数"--label io.rancher.container.network=true"。多数Rancher的网络功能，如负载均衡器、DNS服务，都要求容器必须在管理的网络下工作。

*Rancher supports cross-host container communication by implementing a simple and secure overlay network using IPsec tunneling. To leverage this capability, a container launched through Rancher must select “Managed” for its network mode or if launched through Docker, provide an extra label “–label io.rancher.container.network=true”. Most of Rancher’s network features, such as load balancer or DNS service, require the container to be in the managed network.*

Rancher网络下，容器将被分配到一个容器桥接IP(172.17.0.0/16)，而Rancher的管理IP(10.42.0.0/16)默认在docker0桥接网卡上。同一环境的容器则可以通过管理网络路由可达。

*Under Rancher’s network, a container will be assigned both a Docker bridge IP (172.17.0.0/16) and a Rancher managed IP (10.42.0.0/16) on the default docker0 bridge. Containers within the same environment are then routable and reachable via the managed network.*

> 注：Rancher管理IP地址将不会写入Docker元数据，因此不会出现单独的Docker巡检的结果。这有时会导致某些不兼容的工具需要Docker桥接地址。我们已经和Docker社区协商，确保未来版本的Docker可以更洁净地覆盖全网络。

> *Note: The Rancher managed IP address will not be present in Docker meta-data and as such will not appear in the result of a Docker “inspect.” This sometimes causes incompatibilities with certain tools that require a Docker bridge IP. We are already working with the Docker community to make sure a future version of Docker can handle overlay networks more cleanly.*


####  服务发现（SERVICE DISCOVERY）

Rancher采用标准Docker Compose术语提供服务，并从同一Docker镜像获取多个容器从而自定义基础服务。

*Rancher adopts the standard Docker Compose terminology for services and defines a basic service as one or more containers created from the same Docker image. Once a service (consumer) is linked to another service (producer) within the same stack, a DNS record mapped to each container instance is automatically created and discoverable by containers from the “consuming” service. Other benefits of creating a service under Rancher include:*

> **服务的高可用（HA）- 拥有Rancher自动监控容器状态和服务维护所需规模的能力**

> **健康度监测 - 拥有设置容器健康基本监测阈值的能力**

> **添加负载均衡器 - 拥有添加简单的负载均衡器而使你的服务能够使用HAProxy的能力**

> **添加外部服务 - 拥有增加任何IP作为对外服务的能力**

> **添加服务别名 - 拥有为你的服务提供对外的DNS记录的能力**



> *Service High Availability (HA) - the ability to have Rancher automatically monitor container states and maintain a service’s desired scale.*

> *Health Monitoring - the ability to set basic monitoring thresholds for container health.*

> *Add Load Balancers - the ability to add a simple load balancer for your services using HAProxy.*

> *Add External Services - the ability to add any-IP as a service to be discovered.*

> *Add Service Alias - the ability to add a DNS record for your services to be discovered.*

> **有关更多信息，参见添加服务，增加负载均衡器，增加外部服务或增加服务的别名搜索**

> *For more information, see adding services, adding load balancers, adding external services or adding service alias.*


####  负载均衡器（LOAD BALANCER）

Rancher使用HAProxy实现托管负载均衡功能，也可手动地缩放到多个主机。负载均衡器可用来分发网络和应用流量，后者经常来源于被直接添加或链接到基础服务的独立容器。而被链接的基础服务，将其所有基本容器自动被认为是Rancher的负载平衡目标。

*Rancher implements a managed load balancer using HAProxy that can be manually scaled to multiple hosts. A load balancer can be used to distribute network and application traffic to individual containers by directly adding them or “linked” to a basic service. A basic service that is “linked” will have all its underlying containers automatically registered as load balancer targets by Rancher.*


####  分布式DNS服务（DISTRIBUTED DNS SERVICE）

Rancher利用自身轻量级的DNS服务器加上一个高可用的控制面板，实现了一个分布式DNS服务。每个健康的容器是自动添加到DNS服务时，链接到另一服务或添加到服务的别名。当服务名受到质疑时，DNS服务返回一个健康的容器实现服务IP地址随机列表。

*Rancher implements a distributed DNS service by using its own light-weight DNS server coupled with a highly available control plane. Each healthy container is automatically added to the DNS service when linked to another service or added to a Service Alias. When queried by the service name, the DNS service returns a randomized list of IP addresses of the healthy containers implementing that service.*

> **默认情况下，同一堆栈的所有服务添加为DNS服务，无需显式链接。**

> **您可以通过服务名解析同一堆栈中的容器。**

> **如果需要为您的服务自定义DNS域名，与服务名不同，您将需要使用一个链接到自定义DNS域名。**

> **链接仍是负载均衡所需要实现服务的功能。**

> **即使使用了服务别名，仍需要链接功能。**

> **为使不同堆栈中的服务得以解析，您需要很明确地将服务链接。**

> **因为Rancher的大二层网络为每个容器提供不同IP，您不需要进行端口映射，不需要将镜像服务监听到不同的端口。很简单，DNS服务能够胜任这些服务发现功能。**


> *By default, all services within the same stack are added to the DNS service without requiring explicit links.*

> *You can resolve containers within the same stacks by the service names.*

> *If you need a custom DNS name for your service, that is different from your service name, you will be required to use a link to get the custom DNS name.*

> *Links are still required for load balancers to target services.*

> *Links are still required if a Service Alias is used.*

> *To make services resolvable that are in different stacks, you will need to link them explicitly.*

> *Because Rancher’s overlay networking provides each container with a distinct IP address, you do not need to deal with port mappings and do not need to handle situations like duplicated services listening on different ports. As a result, a simple DNS service is adequate for handling service discovery.*

####  健康度监测（HEALTH CHECKS）

Rancher通过运行管理网络代理的方式，在主机进行容器和服务的分布式监控检查，以实现健康度监测。代理则在内部网络利用HAProxy来校验程序的健康状况，当单个容器或服务上启用了健康度监测，则可以被多达三个代理监测主机状态。如有至少一个HAProxy实例通过了健康度监测，则该容器被认为是健康的。

*Rancher implements a health monitoring system by running managed network agent’s across it’s hosts to co-ordinate the distributed health checking of containers and services. These network agents internally utilize HAProxy to validate the health status of your applications. When health checks are enabled either on an individual container or a service, each container is then monitored by up to three network agents running on hosts separate to that containers parent host. The container is considered healthy if at least one HAProxy instance reports a “passed” health check.*

> **注：**

> **唯一的例外是你的环境只有一台主机，这种情况，健康度将由本机监测。**

> **Rancher会处理网络分片，会进行比宿主机更有效的健康监测。利用HAProxy进行健康监测，Rancher可以让用户在跨应用程序和负载均衡器中指定相同的健康监测策略。**


> *NOTE:*

> *The only exception to this model is when your environment contains a single host. In such instances the health checks will be performed by the same host.*

> *Rancher handles network partitions and is more efficient than client-based health checks. By using HAProxy to perform health checks, Rancher enables users to specify the same health check policy across applications and load balancers.*

获取更多的信息，如实例停止服务，Rancher如何显示服务状态，请进行健康度监测。您可以通过Rancher-Compose或UI获取更多健康度监测。

*For more information such as including example failure scenarios and how Rancher displays services, see Health Checks. You can also read more about setting up health checks by using rancher-compose or in the UI.*

####  服务高可用（SERVICE HA）

Rancher不断监视您的容器服务状态，并积极保证服务所需要的资源。当资源比服务所需的变得更少时，主机将被触发成为不可用，容器将无法满足健康度监测而无法启动。

*Rancher constantly monitors the state of your containers within a service and actively manages to ensure the desired scale of the service. This can be triggered when there are fewer (or even more) healthy containers than the desired scale of your service, a host becomes unavailable, a container fails, or is unable to meet a health check.*

####  服务升级（SERVICE UPGRADE）

Rancher所支持的服务升级指的是，允许用户通过负载均衡或者服务别名的方式来对既有服务进行升级。据此，Rancher将为服务所需的负载均衡器创建静态资源，一旦建立，基础服务通过独立测试验证并添加至负载均衡器或服务别名时，就可以直接从Rancher克隆为新服务了。现有服务如果过期可以移除，随后的所有服务或应用程序流量将被自动分配到新的服务。

*Rancher supports the notion of service upgrades by allowing users to either load balance or apply a service alias for a given service. By leveraging either Rancher features, it creates a static destination for existing workloads that require that service. Once this is established, the underlying service can be cloned from Rancher as a new service, validated through isolated testing, and added to either the load balancer or service alias when ready. The existing service can be removed when obsolete. Subsequently, all the network or application traffic are automatically distributed to the new service.*

####  Rancher-Compose工具（RANCHER COMPOSE）

Rancher-Compose是构造Rancher的命令行工具，概念类似于Docker-Compose。Docker需要利用Docker-compose.yml文件在Rancher上构造堆栈，同样地Rancher-compose.yml文件需要有这些属性：表规格、负载均衡规则、健康度监测策略以及Docker-compose目前不支持的外部链接。

*Rancher implements and ships a command-line tool called rancher-compose that is modeled after docker-compose. It takes in the same docker-compose.yml templates and deploys the Stacks onto Rancher. The rancher-compose tool additionally takes in a rancher-compose.yml file which extends docker-compose.yml to allow specifications of attributes such as scale, load balancing rules, health check policies, and external links not yet currently supported by docker-compose.*

更多信息，请查看Rancher-compose章节。

*For more information, see rancher-compose.*

####  栈（STACKS）

Rancher栈的概念类似Docker-compose项目，需要一组服务，从而构成一个典型的应用程序或负载。

*A Rancher stack mirrors the same concept as a docker-compose project. It represents a group of services that make up a typical application or workload.*


#### 容器调度（CONTAINER SCHEDULING）

Rancher支持的容器调度策略仿照Docker Swarm，均是基于：

*Rancher supports container scheduling policies that are modeled closely after Docker Swarm. They include scheduling based on:*

> **端口冲突**

> **共享卷**

> **主机标签**

> **共享网络协议栈：–net=container:dependency**

> **同时使用Swarm和Rancher严格或温和的亲和力或反亲和力**


> *port conflicts*

> *shared volumes*

> *host tagging*

> *shared network stack: –net=container:dependency*

> *strict and soft affinity/anti-affinity rules by using both env var (Swarm) and labels (Rancher)*

此外，Rancher支持的服务调度触发器包括用户指定的规则，如“添加主机”或“主机标签” ，可自动缩放到服务具有特定标签的主机。

*In addition, Rancher supports scheduling service triggers that allow users to specify rules, such as on “host add” or “host label”, to automatically scale services onto hosts with specific labels.*


有关如何在Rancher调度容器的更多信息，请参阅如何使用标签和调度规则的用户界面或我们如何在Rancher-compose中使用标签。

*For more information on how to schedule containers in Rancher, see how we use labels and scheduling rules in the UI or how we use labels in rancher-compose.*

####  Sidekicks（SIDEKICKS）

Rancher通过Sidekicks的概念允许用户将这些服务分组，并支持服务托管，调度和锁定缩放。拥有多个Sidekicks的服务通常是支持容器间的共享卷（即--volumes_from ）和网络（即--net=container）。

*Rancher supports the colocation, scheduling, and lock step scaling of a set of services by allowing users to group these services by using the notion of sidekicks. A service with one or more sidekicks is typically created to support shared volumes (i.e. --volumes_from) and networking (i.e. --net=container) between containers.*

获取更多信息，请详阅rancher-compose中的sidekicks章节。

*For more information, see sidekicks with rancher-compose.*

####  元数据服务（METADATA SERVICES）
Rancher为服务和容器提供数据，这些数据可用于直接通过基于HTTP API访问的元数据服务的形式来管理运行的容器实例。创建Docker容器时，和Rancher服务运行时产生的数据，如对同一服务中容器之间的交互信息，这些数据均包括静态信息。

*Rancher offers data for both your services and containers. This data can be used to manage your running Docker instances in the form of a metadata service accessed directly through a HTTP based API. These data can include static information when creating your Docker containers, Rancher Services, or runtime data such as discovery information about peer containers within the same service.*

获取更多信息，请详阅元数据服务章节。

*For more information, see metadata service.*

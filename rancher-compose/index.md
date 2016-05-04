# 使用 Rancher-Compose

Jitao Hou 正在翻译首稿，欢迎之后Review

`Rancher-compose` 工具就像一个多主机版本的 `docker-compose`. 它直接操作Rancher图形界面中的 [应用栈（stack）]({{site.baseurl}}/rancher/rancher-ui/applications/stacks/) , 应用栈属于一个[环境（environment）]({{site.baseurl}}/rancher/configuration/access-control/)，并且包含很多[主机（hosts）]({{site.baseurl}}/rancher/rancher-ui/infrastructure/hosts/). 由`rancher-compose`启动的容器将会被部署在所属环境内的任意满足[调度规则（scheduling rules）]({{site.baseurl}}/rancher/rancher-compose/scheduling/)的主机节点上. 如果事先没有定义调度规则， 服务中的容器将会在已启动容器数目较少的节点上来启动。 因为使用相同的API调用， 使用ancher-compose就和用户界面中启动服务的效果一样，服务中的容器将会被拉起。  

`Rancher-compose` 工具就像大家熟知的 `docker-compose`一样， 支持任意的`docker-compose.yml` 文件. 另外，它还支持 `rancher-compose.yml`文件，该文件扩充和覆写了 `docker-compose.yml`的定义，包含了很多只有在Rancher环境才被支持的属性，如， 服务中容器的启动数量等。  

如需深入掌握本章关于`rancher-compose`的知识，用户需要对`docker-compose`有基本的了解。请在开始使用`rancher-compose`之前，通读[docker-compose documentation](https://docs.docker.com/compose/) 。

### 安装

该命令的二进制文件可以直接在用户界面（UI）中下载，下载的链接位于UI的右下角，其中包含支持Windows, Mac和 Linux的命令行文件.

你也可以参考[releases page for rancher-compose](https://github.com/rancher/rancher-compose/releases) 来直接下载该工具。 

### 在Rancher服务器中设置Rancher-Compose

为了使得`rancher-compose`能够在Rancher的实例中启动服务，你需要设置一些环境变量，或者在`rancher-compose` 命令行中以选项的方式传递这些变量。这些所需要的环境变量是`RANCHER_URL`, `RANCHER_ACCESS_KEY`, 和`RANCHER_SECRET_KEY`. Access key和Secret key属于[API key]({{site.baseurl}}/rancher/configuration/api-keys/). 

```
bash
# Set the url that Rancher is on
$ export RANCHER_URL=http://server_ip:8080/
# Set the access key, i.e. username
$ export RANCHER_ACCESS_KEY=<username_of_key>
# Set the secret key, i.e. password
$ export RANCHER_SECRET_KEY=<password_of_key>
```

如果你选择不设置这些环境变量，你将会需要在`rancher-compose` 命令行中以选项的方式传递这些变量。 

```
bash
$ rancher-compose --url http://server_ip:8080 --access-key <username_of_key> --secret-key <password_of_key> up
```

现在, 你可以利用`rancher-compose` 搭配任意的 `docker-compose.yml`文件来启动服务. 该服务将会在API Key所在的[environment]({{site.baseurl}}/rancher/configuration/environments/)中的相关Rancher 实例里自动被启动。  

### 命令

如需了解关于命令和选项的更多信息，请参考 [rancher-compose command]({{site.baseurl}}/rancher/rancher-compose/commands/) 文档. 

#### 命令举例

```
bash
# Creating and starting a service without environment variables and picking a stack
$ rancher-compose --url URL_of_Rancher --access-key username_of_API_key --secret-key password_of_API_key -p stack1 up
# To change the scale of an existing service
$ rancher-compose -p stack1 scale web=3
```

#### 删除服务／容器

在缺省情况下, `rancher-compose` 不会删除服务／容器。这意味着如果你在一行内有两个`up`命令，第二个`up`将不起作用。这是因为第一个up指令将会创建每个对象，并且让它运行。 即使你不传递 `-d` 选项给 `up`指令, `rancher-compose` 也不会删除你的服务。你必须使用 `rm`来删除一个服务。

### Build

有两种方式来支持 docker build。第一种是设置 `build:` 到一个与[Docker Remote API](https://docs.docker.com/reference/api/docker_remote_api_v1.18/#build-image-from-a-dockerfile) 中的远程参数相兼容的 git 地址或 HTTP URL。第二种方法是设置`build:` 到一个本地目录，build的上下文将会被上传到S3，然后在每个节点上按指令构建。

为了基于S3 的build正常工作，你必须[设置AWS credentials](https://github.com/aws/aws-sdk-go/#configuring-credentials)。 我们提供了如何在rancher-compose使用S3的[详细样例]({{site.baseurl}}/rancher/rancher-compose/build/) 

### Sidekicks

使用服务时， 你可能需要你所定义的服务使用`volumes_from` 和 `net`来指向另外一个服务. 为了达到这个目的，你需要设置sidekick关系。 Sidekick关系使得Rancher能够像一个单元一样来伸缩和调度一组服务。举例: B是A的sidekick, 这两个服务将会被始终成对部署，服务中的容器数量也将始终是一样的。   

另外你需要定义sidekick的情形是，你需要确保多个服务始终部署在同一个主机节点上。  

为了设置sidekick关系, 你需要为其中一个服务加上标签（label）。标签的关键字是 `io.rancher.sidekicks` ，其赋值是一个服务或多个服务名称。如果你需要添加多个服务作为sidekick，这些服务名称需要用逗号隔开。举例: `io.rancher.sidekicks: sidekick1, sidekick2, sidekick3`

为一个服务定义了sidekick之后，你将不需要在`rancher-compose`里设置link，因为这些这些在同一环境的服务将会自动被DNS解析到。

#### 主服务

包含sidekick的服务被称之为主服务,其sidekick将被认为是从服务。主服务的尺寸（容器多少）将会被应用于所有在sidekick的从服务。 如果你所有服务的尺寸不一致，主服务的尺寸将会被用在所有服务上。

当为定义了sidekick的服务配置[负载均衡器]({{site.baseurl}}/rancher/rancher-compose/rancher-services/#load-balancer) 时，你只能为主服务设置目标服务。Sidekick服务 **不能够** 被定义为目标服务。

#### Rancher-Compose中的Sidekick举例:

Sample configuration `docker-compose.yml` 

```
yaml
test:
  tty: true
  image: ubuntu:14.04.2
  stdin_open: true
  volumes_from:
  - test-data
  labels:
    io.rancher.sidekicks: test-data
test-data:
  tty: true
  command:
  - cat
  image: ubuntu:14.04.2
  stdin_open: true
```

<br>
Sample `rancher-compose.yml`

```yaml
test:
  scale: 2
test-data:
  scale: 2
```

#### Rancher-Compose中的Sidekick举例：多个服务使用同一个`volumes_from`

如果你有多个服务使用同一个容器来做`volumes_from`, 你可以为主服务增加一个定义为sidekick的从服务，来使用同一个数据容器。因为只有一个容器能够被作为负载均衡的目标，所以请确保选择一个正确的服务作为主服务(即定义了sidekick标签的服务)。

```
yaml
test-data:
  tty: true
  command:
  - cat
  image: ubuntu:14.04.2
  stdin_open: true
test1:
  tty: true
  image: ubuntu:14.04.2
  stdin_open: true
  labels:
    io.rancher.sidekicks: test-data, test2
  volumes_from:
  - test-data
test2:
  tty: true
  image: ubuntu:14.04.2
  stdin_open: true
  volumes_from:
  - test-data
```

### 为服务添加链接（link）

在Rancher中，同一个环境里的所有服务都是可以被DNS解析到的，所以并不需要为服务显示地添加链接（link），除非你需要使用一个指定的别名来做DNS解析。 

> **Note:** 目前我们不支持将sidekick链接到主服务或者主服务连接到sidekick。更多信息，请参考[Rancher内置DNS的工作原理]({{site.baseurl}}/rancher/rancher-services/internal-dns-service/).

属于同一个应用栈的服务，任何服务可以按照自己原有的 `服务名称`被DNS解析到, 当然，你可以定义链接，如果你希望为某一个服务定义别名的话。  


`docker-compose.yml`的一个例子

```
wordpress:
  image: wordpress
  links:
  - db:mysql
db:
  image: mysql
```
在这个例子中, `db` 将被解析为 `mysql`. 如果没有定义链接（link）, `db` 将只能按`db`解析.


在其它应用栈中的服务，服务将会按照`service_name.stack_name`的格式被DNS解析。 如果你需要使用一个指定的别名做DNS解析，你需要在`docker-compose.yml`中定义 `external_links`。

 `docker-compose.yml`的一个例子

```
wordpress:
  image: wordpress
  external_links:
    - alldbs/db1:mysql
```
<br>

在这个例子中, `alldbs` 应用栈中有一个 `db1` 服务，wordpress将会链接到它。 在 `wordpress`服务中, `db1` 将会被解析为 `mysql`. 如果没有定义external link, `db1` 将按`db1.alldbs`解析.

> **备注:** 跨应用栈的服务发现只限于同一个环境之内(by design)，不支持跨环境的服务发现。






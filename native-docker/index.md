# 通过 Docker 原生命令行使用Rancher

Rancher integrates with the native docker CLI so that it can be used alongside other DevOps and Docker tools. At a high level, this means that if you start, stop, or destroy containers outside of Rancher, Rancher will detect those changes and update accordingly.

Rancher与Docker原生命令行的集成，使它能够和其他DevOps和Docker工具一起结合使用。在另一个层面来说，在Rancher的外部去启动，停止或者销毁容器，Rancher也会检测到这些容器的所发生的变化并将其更新到相应的状态


### Docker Event Monitoring
### Docker 事件监控

Rancher updates in real time by monitoring Docker events on all hosts. So when a container is started, stopped, or destroyed outside of Rancher (for example by executing `docker stop sad_einstein` directly on a host), Rancher will detect that change and update its states accordingly. 

Rancher通过监控所有主机的Docker事件来实时更新容器的状态。所以在Rancher平台之外的启动，停止或者销毁容器（比如直接在主机执行`docker stop sad_einstein`），Rancher将会检测到这些容器的所发生的变化并将其更新到相应的状态。


> **Note:** One current limitation is that we wait until containers are started (not created) to import them to Rancher. Running `docker create ubuntu` will not cause the container to appear in the Rancher UI, but running `docker start ubuntu` or `docker run ubuntu` will.

> **注意：** 当前的一个限制是：Rancher会等待容器已经启动了（不是创建）才会将其导入到Rancher，执行`docker create ubuntu`会导致这个容器在Rancher UI上消失，但是执行`docker start ubuntu`或者`docker run ubuntu`则不会。

You can observe the same Docker event stream that Rancher is monitoring by executing `docker events` on the command line of a host.

Rancher通过`docker events`命令去监控容器的变化，你也可以使用这个命令去观察Docker的事件流。

### Joining natively started containers to the Rancher network
### 把已经启动的容器加入到Rancher中

You can start containers outside of Rancher and still have them join the Rancher managed network. This means that these containers can participate in cross-host networking. To enable this feature, add the `io.rancher.container.network` label with a value of `true` to the container when you create it. Here's an example:

你可以在Rancher之外启动容器，并让这些容器受控于Rancher。这意味着容器可以运行在跨主机的网络中。在创建容器的时候添加一个值为`true`的`io.rancher.container.network`标签来启用这个特性。

```bash
$ docker run -l io.rancher.container.network=true -itd ubuntu bash
```

To read more about the Rancher managed network and cross-host networking, please read about Rancher [Concepts]({{site.baseurl}}/rancher/concepts/).

如需了解更多关于Rancher管理网络和跨主机网络，请阅读Rancher[基本概念]({{site.baseurl}}/concepts/).



### Importing Existing Containers
### 导入已存在的容器

Rancher also supports importing existing container upon host registration. When you register a host through the [custom command]({{site.baseurl}}/rancher/rancher-ui/infrastructure/hosts/custom/), any containers currently on the host will be detected and imported into Rancher.

Rancher还支持对已经存在容器的导入。当你通过[添加自己的主机]({{site.baseurl}}/rancher-ui/infrastructure/custom.html)添加一个主机时，主机上所有的容器都会被Rancher检测到并导入到Rancher中。


### Periodically Syncing State
### 定期同步状态

In addition to monitoring docker events in real time, Rancher periodically syncs state with the hosts. Every five seconds, hosts report all containers to Rancher to ensure the expected state in Rancher matches the actual state on the host. This protects against things like network outages or server restarts that might cause Rancher to miss Docker events. When syncing state in this fashion, the state of the container on the host will always be the source of truth. So, for example, if Rancher thinks a container is running, but it is actually stopped on the host, Rancher will update the container's state to stopped. It will not attempt to restart the container.

除了实时监听Docker事件外，Rancher还定期的去同步主机的状态。每隔5秒钟，主机上报所有的容器信息到Rancher上，以确保Rancher上展示的状态和主机上实际的状态相符。防止由于网络中断或者服务器重启导致Rancher没接受到Docker事件信息。通过这种方式去同步状态，Rancher上的容器状态将会一直和主机上实际状态保持一致。比如说，Rancher觉得一个容器正在运行中，但它在主机上被停止了，Rancher将会把该容器的状态更新成停止状态，而不是去尝试重启这个容器。


---
title: Rancher Catalog
layout: rancher-default
---

## Rancher目录
---

为了轻松的部署复杂的Stacks（应用栈），Rancher为我们设计了称为Catalog（应用程序模板“目录”）的功能。访问**Catalog**页, 将会看到有两个子页：ALL以及LIBRARY。ALL是所有[已启用的应用程序模板集]({{site.baseurl}}/rancher/configuration/settings/#catalog)（包括LIBRARY下的项目和自建项），而**Library**页下面包含了[Rancher certified catalog](https://github.com/rancher/rancher-catalog)和[community-catalog](https://github.com/rancher/community-catalog)。前者是Rancher Labs公司官方认证的项目，后者是由社区维护的项目。Rancher Labs公司仅仅只对通过认证的项目进行维护与支持。两者的不同点可以从项目缩略图顶部是否带有“Certified”进行区分。

Rancher系统的[admin]({{site.baseurl}}/rancher/configuration/access-control/#admin)角色可以对目录下的项目进行添加或删除操作，在**Admin** -> **Settings**页我们可以选择在系统里显示（启用）哪些目录项。添加一个目录项并不复杂，只需要给定一个名字和一个URL。对于URL的内容要求是它必须能被`git clone`[处理](https://git-scm.com/docs/git-clone#_git_urls_a_id_urls_a)。当添加一个目录项后，它会立即显示在目录页并处于可操作状态。

URL示例：https://github.com/rancher/rancher-catalog.git （下面会提到如何准备正确的文件及目录结构并提交至git服务器）

如果Rancher Server处于http proxy代理服务器之后，那么需要[修改Docker守护进程的启动参数]({{site.baseurl}}/rancher/installing-rancher/installing-server/#http-proxy)，否则catalog将无法正常运作。  

### 启动模板 

首先找到需要的模板，可以进行关键字搜索或直接进行类型过滤。找到模板之后就可以点击View Details按钮进去，填写必须的信息之后就可以点**Launch**按钮启用模板了。

1. 对于模板项的版本，系统默认采用的是当前最新的**版本**，如果需要的话，仍然可以选择存在的其它老版本。 
2. 给定一个新的**stack**名字，必要时填写stack的**描述信息**。 
3. 选择或填写**Configuration Options**里的项目, 这些项目因不同的目录特性而不同，如果勾选**Start Services after creating**，那么一旦点击**Launch**，服务将会自动启动。 
4. 点击**Launch**以创建基于模板的应用栈(stack)。点击**Preview**则可以以展开方式查阅`docker-compose.yml`和`rancher-compose.yml`等文件内容，这些正是用以创建应用栈(stacks)的模板文件。 

### 升级模板

Rancher里有个很棒的功能是当新版本的模板上载到目录之后，它会提示你有新版本可用。当点击**Upgrade Available**时，当选择了某个版本之后，需要查阅一下**Configuration Options**是否也需要做出相应的改变，确认无误之后即可点击**Save**保存变更。 

当所有的服务被升级之后，服务service和应用栈stack的状态会显示为**Upgraded**。如果对升级的结果表示满意，那么最后就可以点击stack下拉菜单里的**Finish Upgrade**以确认升级。**注意：一旦确认完成升级过程，就没法再回退到旧的版本了。**

#### 版本回退 

如果在升级过程中遇到错误，需要回退至升级前的正常状态，可以在stack的下拉菜单里选择**Rollback**回退至之前的版本。 

## 创建私有目录
---

Rancher catalog 服务定义了一套配置文件的格式，只需按照格式来添加配置文件，catalog服务就能通过git自动把它加到rancher-server的容器的模板目录下。

### 目录结构

目录模板在Rancher里的显示内容基于环境的集群管理类型。 

* _Cattle_ 环境: UI里的条目来自`templates`文件夹 
* _Kubernetes_ 环境: UI里的条目来自`kubernetes-templates`文件夹 
* _Swarm_ 环境: UI里的条目来自`swarm-templates`文件夹 

```
-- templates OR kubernetes-templates OR swarm-templates
  |-- cloudflare
  |   |-- 0
  |   |   |-- docker-compose.yml
  |   |   |-- rancher-compose.yml
  |   |-- 1
  |   |   |-- docker-compose.yml
  |   |   |-- rancher-compose.yml
  |   |-- catalogIcon-cloudflare.svg
  |   |-- config.yml
...
```
<br>

在主目录里，需要有一个`templates`文件夹，`templates`文件夹里包含了已创建好的每个catalog条目。我们推荐对每一个catalog条目使用一个简单的模板名，并和文件夹名称保持一致。 

在catalog条目文件内（例如`cloudflare`）对应于创建的每一个不同版本都有一个对应的文件夹。第一个版本对应的文件夹名是`0`，后续版本则依次增加，例如第二个版本对应于文件夹`1`。提供了一个新版本对应的文件夹内容，就提供了对stack升级其模板至新版本的渠道。作为一种可选方案，还可以直接对`0`文件夹也就是第一版本文件夹内容直接更新然后再重新部署这个条目

> **注意：** 每个catalog条目必须使用独立单词，所以请在较长的完整名称中使用连字符 `-` 代替空格。而在`config.yml`的`name`部分内你可以使用空格。

#### Rancher目录里显示的目录文件

在catalog条目文件夹里有两个文件，其细节决定了条目如何在Rancher Catalog内具体显示。

* 第一个配置文件`config.yml`包含了条目细节。

     ```yaml
     name: # Name of the Catalog Entry 
     description: |
        # Description of the Catalog Entry
     version: # Version of the Catalog to be used 
     category: # Category to be used for searching catalog entries
     maintainer: # The maintainer of the catalog entry
     license: # The license 
     projectURL: # A URL related to the catalog entry
     ```
<br>
* 第二个文件是catalog条目的图标文件，其文件名必须以`catalogIcon-`作为开头。 

对于每一个catalog条目文件夹，至少需要三项，其中包含两个文件和一个文件夹，它们是`config.yml`，`catalogIcon-entry.svg`和`0`文件夹（第一个版本），如果有多个版本，则包含更多的文件及文件夹。

#### Rancher 目录模板

`docker-compose.yml`和`rancher-compose.yml`在Rancher里是**必需的**文件，被[rancher-compose]({{site.baseurl}}/rancher/rancher-compose/)工具调用以启动服务，它们位于版本号对应的文件夹内（例如`0`，`1`等）。

`docker-compose.yml`同时应该是一个能够被`docker-compose`工具载入并启动的文件，其格式遵循docker-compose规范。

`rancher-compose.yml`则包含了附加的信息用于自定义catalog条目。在`.catalog`节，有一些字段是为了正确执行catalog条目所必需的项目。

`README.md`作为可选项也可能被创建，其主要作用在于提供详尽的描述说明或注意事项。


**`rancher-compose.yml`**

```yaml
.catalog
  name: # Name of the versioned template of the Catalog Entry 
  version: # Version of the versioned template of the Catalog Entry 
  description: # Description of the versioned template of the Catalog Entry
  uuid: # Unique identifier to be used for upgrades. Please see note. 
  minimum_rancher_version: # The minimum version of Rancher that supports the template
  questions: #Used to request user input for configuration options
```
<br>

> **注意：** `uuid`是一个必选参数用于升级更新版本的场景。每个`uuid`都必须是唯一的并且随着版本变化而增加。推荐的格式是使用catalog条目名称并添加一个版本号前缀`-0`，如前所述对版本所做的说明，下一个版本前缀则是`-1`。

#### `rancher-compose.yml`内的Questions

位于`.catalog`里的`questions`小节允许用户修改服务的配置选项。而文件`answers.txt`则对应地设置这些配置项的具体值。

每一个配置项都在rancher-compose.yml的`questions`小节内列出。

```yaml
.catalog
  questions:
    - variable: # A single word that is used to pair the question and answer.
      label: # The "question" to be answered.
      description: | # The description of the question to show the user how to answer the question.
      default: # (Optional) A default value that will be pre-populated into the UI
      required: # (Optional) Whether or not an answer is required. By default, it's considered `false`.
      type: # How the questions are formatted and types of response expected 
```
<br>

**_Type_**

`type` 小节用于服务配置项的问题及对应答案以何种方式显示于UI界面。 

合适的格式有：

`string`

显示一个文本框，用于存放字符串型的在`answer.txt`里定义的值。

`int`

显示一个文本框，用于存放被整形化的在`answer.txt`里定义的值。UI会对其数值在模板启用前做合法性检测。

`boolean`

显示一个单选按钮，用于存放其在`answer.txt`里定义的布尔变量值，如果布尔值为`真`，则单选框被勾选，否则被置空。

`password`

显示一个文本框，用于存放其在answer.txt里定义的密码字符串内容。

`service`

显示一个下拉框，其内容为环境(environment)里的所有服务(services)。 

`enum`

显示一个下拉框，其内容为自定义的`options`条目。

```
.catalog
  questions:
    - variable:
      label:
      description: |
      type: enum   
      options: # List of options if using type of `enum`
        - Option 1
        - Option 2
```
<br>
`multiline`

显示一个多行文本框。 

```
.catalog
  questions:
    - variable:
      label:
      description: |
      type: multiline
      default: |
        Each line
        would be shown
        on a separate 
        line.
```

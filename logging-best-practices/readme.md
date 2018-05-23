---
type: reference
version: 1.0.1
title: “Docker 参考架构：Docker 日志记录设计与最佳实践"
redirectfrom:
  - "Docker_Reference_Architecture-_Docker_Logging_Design_and_Best_Practices"
  - docker-reference-architecture-docker-logging-design-and-best-practices
  - logging_best_practices
summary: “此参考架构提供了关于 Docker 日志记录功能如何工作的概述，说明了 Docker 日志的两大类别，然后讨论了 Docker 日志记录最佳实践。"
dateCreated: “Tue, 25 Jul 2017 22:16:49 GMT"
dateModified: "Wed, 16 Aug 2017 18:42:24 GMT"
dateModified_unix: 1502908944
author: "ahromis"
platform:
testedon:
  - ee-17.03.0-ee-5
  - ee-17.06.1-ee
tags:
  - logging
  - deploy
  - management
product:
  - EE
---

## 简介

在传统上，设计和实现集中的日志记录总是成为马后炮。要等到各种问题出现，集中的日志记录成为优先事项，人们才会想到用这样的解决方案查询、查看和分析日志，以找到问题的根本原因。但是到了容器时代，在设计使用 Docker 企业版 (Docker EE) 的容器即服务 (CaaS) 平台时，优先解决集中式日志记录是至关重要的。随着在容器中部署的微服务数量不断增多，它们以日志（或事件）形式产生的数据量会发生指数增长。

## 您将学到的知识

此参考架构提供了关于 Docker 日志记录功能如何工作的概述，说明了 Docker 日志的两大类别，然后讨论了 Docker 日志记录最佳实践。

## 了解 Docker 日志记录

在研究设计要素之前，先了解 Docker 日志记录的基本知识很重要。

Docker 支持不同的日志记录驱动，用于存储和/或流式传输主容器进程 (`pid 1`) 的**容器** `stdout` 和 `stderr` 日志。默认情况下，Docker 使用 `json-file` 日志记录驱动，但也可以配置它使用[许多其他驱动](https://docs.docker.com/engine/admin/logging/overview/#supported-logging-drivers)，方法是在 `/etc/docker/daemon.json` 中设置 `log-driver` 的值，然后重启 Docker 守护进程以重新加载其配置。

日志记录驱动设置会应用于重新配置守护进程之后启动的**所有**容器（在重新配置日志记录驱动之后重启现有容器并不会导致容器使用更新过的配置）。要覆盖默认的容器日志记录驱动，应使用 `--log-driver` 和 `--log-opt` 选项运行容器。另一方面，可以使用 `docker service update --log-driver <DRIVER_NAME> --log-opt <LIST OF OPTIONS> <SERVICE NAME>` 对 swarm mode 服务进行运行中更新，使其改用不同的日志记录驱动。

那么 Docker 引擎日志呢？这些日志通常由默认的系统管理节点日志记录器处理。现代的大多数发行版（CentOS 7、RHEL 7、Ubuntu 16 等）都使用 `systemd`，后者使用 `journald` 记录日志，使用 `journalctl` 访问日志。要访问引擎日志，可使用 `journalctl -u docker.service`。

## Docker 日志类别和源

说过 Docker 日志记录的基础知识之后，本节将说明它们的**类别**和**源**。

Docker 日志通常可分为两种类别：**基础架构管理**日志或**应用**日志。大多数日志根据需访问日志者的角色自然地归入这两种类别。

* 运维人员最关心平台的稳定性以及服务的可用性。

* 开发人员比较关心其应用代码以及服务的性能。

为了实现自助服务平台，运维人员和开发人员都应该有权访问他们为了履行职责而需要访问的日志。开发运维的实践表明，在服务可用性和性能方面，大家要共同承担总体责任。但是，不应该让每一个人都有权访问平台上的每个日志。例如，开发人员应该只需要访问关于其服务和集成点的日志。运维人员则更关心 Docker 守护进程日志、UCP 和 DTR 可用性，以及服务可用性。两者的访问范围有一些重叠，因为开发人员和运维人员都应该了解服务可用性。让每个角色都能访问需要的日志，可以在发生问题时简化故障排除，并缩短解决故障平均时间 (MTTR)。

### 基础架构管理日志

基础架构管理日志包括 Docker 引擎、运行 UCP 或 DTR 的容器以及任何已部署的容器化基础架构服务（例如容器化的监控代理程序）的日志。

#### Docker 引擎日志

前文已经提到，默认情况下由 OS 的系统管理节点记录 Docker 引擎日志。可以将这些日志发送到集中的日志记录服务器。

#### UCP 和 DTR 系统日志

UCP 和 DTR 是作为 Docker 容器部署的。它们的所有日志都记录在容器的 `STDOUT`/`STDERR` 中。Docker 引擎的默认日志记录驱动记录这些日志。

可以配置 UCP 使用远程系统日志记录。此操作可以在安装后从 UCP UI 对其所有容器执行。

> 注：建议在安装 UCP 和 DTR **之前**配置 Docker 引擎默认日志记录驱动，使其日志由所选的日志驱动记录。这是因为容器的日志记录驱动一旦创建就无法更改。唯一的例外是 `ucp-agent`，它是 UCP 的一个组件，作为 Swarm 服务而部署。

#### 基础架构服务

基础架构运维团队部署容器化的基础架构服务，用于各种基础架构操作，例如监控、审核、报告、配置部署等。这些服务也会产生需要记录的重要日志。通常，它们的日志仅限于其容器的 `STDOUT`/`STDERR`，因此也会由 Docker 引擎默认日志记录驱动记录下来。否则，就需要另外处理。

### 应用日志

应用产生的日志可能是定制应用日志和应用主进程的 `STDOUT`/`STDERR` 日志的组合。前文已经说过，所有容器的 `STDOUT`/`STDERR` 日志都由 Docker 引擎默认日志记录驱动记录。所以不需要执行任何定制配置就能记录它们。如果应用有定制的日志记录（例如在容器中将日志写入 `/var/log/myapp.log`），那么就必须将其考虑在内。

## Docker 日志记录设计注意事项

了解 Docker 日志的类型很重要。同样重要的是，定义哪些实体最适合使用和拥有它们。

### 分类 Docker 日志

主要有两种类别：基础架构日志和应用日志。

### 定义组织所有权

根据组织的结构和政策，确定这些类别是否与现有团队有直接对应关系。如果没有，则必须定义负责这些日志类别的合适组织或团队：

| 类别                                     | 团队
|---------------------------------------|----------------------------|
| 系统和管理日志                    | 基础架构运维          |
| 应用日志                              | 应用运维                 |

如果组织是更大组织的一部分，那么这些类别可能过于宽泛。应将它们细分为更具体的负责团队：

| 类别                                     | 团队
|-----------------------------------------------|------------------------------|
| Docker 引擎日志                            | 基础架构运维    |
| 基础架构服务                       | 基础架构运维    |
| UCP 和 DTR 日志                      | UCP/DTR 运维 |
| 应用 A 日志                            | 应用 A 运维     |
| 应用 B 日志                            | 应用 B 运维     |

有些组织并不区分基础架构运维和应用运维，因此他们可以将这两种类别合并，让一个运维团队来负责。

| 类别                                     | 团队                         |
|-----------------------------------------------|------------------------------|
| 系统和管理日志                    | 基础架构运维          |
| 应用日志                              | 基础架构运维                 |

选择合适的模式来明确定义每种日志类型的相应所有权，从而缩短解决故障平均时间 (MTTR)。确定各种日志类型的组织所有权之后，就应该开始考察适合部署的日志记录解决方案。

### 选择日志记录基础架构

Docker 可以方便地与现有日志记录工具和解决方案集成。日志记录生态系统中的主要日志记录实用程序大多已经开发出 Docker 日志记录功能，或者提供了与 Docker 集成的相应文档。

选择的日志解决方案应该符合下列条件：

1. 允许实现上一节定义的组织所有权模式。例如，有些组织可以选择将所有日志发送到单一的日志记录基础架构，然后向职能团队提供合适的访问级别。
2. 组织对其极为熟悉！这是必要条件！
3. 具有 Docker 集成：预配置的仪表板、稳定的 Docker 插件、合适的文档，等等。

## Docker 企业版日志

如果使用 Docker 企业版，为了历史记录和系统维护的目的而存储所有容器日志是个好主意。建议您以可加索引的方式收集部分容器的输出，主要是出于政策原因和快速了解集群事件的目的。在下面几节中，我们将详细说明一些 Docker EE 组件和某些可能对组织有用的日志。

### UCP

| 容器名称      |  日志中的信息                                                                             |
|---------------------|--------------------------------------------------------------------------------------------------|
| ucp-controller      | UCP API 日志、用户和登录                                                                  |
| ucp-auth-api        | UCP 和 DTR 使用的身份和身份验证的集中服务                         |
| ucp-auth-store      | 存储用户、组织和团队的身份验证配置及数据         |
| ucp-auth-worker     | 执行调度的 LDAP 同步并清除身份验证和授权数据        |
| ucp-client-root-ca  | 签发客户端证书包的认证中心                                                     |
| ucp-cluster-root-ca | 签发客户端证书包的认证中心                                                     |
| ucp-metrics         | 用于指标收集                                                                       |
| ucp-kv              | 用于存储 UCP 配置的 etcd 服务                                                |
| ucp-proxy           | 允许从本地 Docker 引擎安全访问 UCP 组件的 TLS 代理               |
| ucp-swarm-manager   | Docker Swarm（经典版）的管理节点容器，提供向后兼容性                   |

注：

1. **ucp-controller** - 此容器记录所有登录尝试和集群的常规使用情况。就审核目的而言，这些信息中包含集群的日志使用方面的最重要日志。
2. **ucp-kv** - 此容器适合用于监控，以确保集群中未失去法定多数。如果失去法定多数，设置警报是个好做法。

### DTR

| 名称                           | 日志中的信息                                                                                                                  |
|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| dtr-api-<replica_id>           | 执行 DTR 业务逻辑并为 DTR Web 应用和 API 提供服务                                                           |
| dtr-garant-<replica_id>        | 管理 DTR 身份验证                                                                                                           |
| dtr-jobrunner-<replica_id>     | 在后台运行清理作业                                                                                                  |
| dtr-nautilusstore-<replica_id> | 存储安全扫描数据                                                                                                        |
| dtr-nginx-<replica_id>         | 检索 HTTP 和 HTTPS 请求，并将它们代理至其他 DTR 组件，默认情况下监听主机的端口 80 和 443        |
| dtr-notary-server-<replica_id> | 接收、验证和提供内容信任元数据；启用内容信任的情况下，在向 DTR 推送或拉取时用于相关查询   |
| dtr-notary-signer-<replica_id> | 为内容信任元数据执行服务器端时间戳和快照签名                                                       |
| dtr-registry-<replica_id>      | 实现拉取和推送 Docker 镜像的功能，也处理镜像的存储                               |
| dtr-rethinkdb-<replica_id>      | 在数据库中存储持久的镜像仓库元数据                                                                                 |

一些可以设置正则表达式的重要日志：

1. **dtr-registry-<replica_id>** - 解析此容器将显示客户端 IP、用户和集群的一般使用情况。
2. **dtr-nginx-<replica_id>** - 此容器记录对于集群的所有推送、拉取和 API 调用。
3. **dtr-rethinkdb-<replica_id>** - 此容器中的日志包含关于 RethinkDB 的法定多数状态的信息。它适合用于监控，在发生任何失去法定多数的情况时可发出警报。

#### HTTP 网格路由

默认情况下，HTTP 网格路由不会将任何请求记录到 `STDOUT`。要记录来自 HRM 的日志，请运行下列命令：

    docker service update --env-add DEBUG=1 ucp-hrm

然后，日志就会从 HTTP 网格路由输出。如果在引擎级别配置日志驱动，则日志输出遵循引擎级别的配置。

## 应用日志驱动

Docker 提供了多种可用于管理应用日志的日志记录驱动。请查看 [Docker 文档](https://docs.docker.com/engine/admin/logging/overview/#supported-logging-drivers)获取完整列表，以及关于如何使用它们的详细信息。

一般而言，如果您已经设置了日志记录基础架构，就应该对此现成基础架构使用日志记录驱动。如果您尚没有现成的日志记录系统，那么需要在此处强调某些方面的优点和缺点。

| 驱动      | 优点                                                                                                   | 缺点                                                                                                                  |
|-------------|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| 无        | 超级安全，因为不记录任何信息                                                                     | 没有日志会大大增加故障排除的难度                                                                                |
| [json-file](https://docs.docker.com/engine/admin/logging/json-file/)   | 默认驱动，`docker logs` 可使用它，支持标签                                                 | 日志驻留在本地，并不聚合，如果没有限制措施，日志可能会占满本地磁盘                               |
| [syslog](https://docs.docker.com/engine/admin/logging/syslog/)      | 大多数机器都附带 syslog，它是唯一支持 TLS 以实现加密日志传输的驱动，支持标签      | 需要设置为具有高度可用性 (HA)，否则如果其不可用，容器启动时可能发生问题 |
| [journald](https://docs.docker.com/engine/admin/logging/journald/)    | `docker logs` 也可使用此驱动，因为它在本地记录日志 - 日志聚合停止运行也不会造成影响，它还会收集 Docker 守护进程日志 | 由于日志是二进制格式的，需要执行附加步骤才能将其传输到日志收集器，不支持标签   |
| [gelf](https://docs.docker.com/engine/admin/logging/gelf/)        | 默认情况下提供可加索引的字段（container id、host、container name 等），支持标签                | 仅支持 UDP 协议，不支持 `docker logs`                                          |
| [fluentd](https://docs.docker.com/engine/admin/logging/fluentd/)     | fluentd 默认提供 `container_name` 和 `container_id` 字段，支持多路输出           | 对于本地日志不支持 `docker logs`                                             |
| [awslogs](https://docs.docker.com/engine/admin/logging/awslogs/)     | 使用 Amazon Web Services 时可方便地集成，减少了需要维护的基础架构，支持标签                                                 | 用于混合云配置或本地部署安装时并非最理想的选择，不支持 `docker logs`                        |
| [splunk](https://docs.docker.com/engine/admin/logging/splunk/)      | 可方便地与 Splunk 集成，支持 TLS，高度可配置，支持标签，附加指标              | Splunk 需要有高可用性，否则容器启动时可能发生问题，不支持 `docker logs`                             |
| [etwlogs](https://docs.docker.com/engine/admin/logging/etwlogs/)     | 用于在 Windows 上进行日志记录的通用框架，默认可索引值                                            | 仅可在 Windows 上工作，必须使用其他实用程序将这些日志从 Windows 机器转移到日志聚合   |
| [gcplogs](https://docs.docker.com/engine/admin/logging/gcplogs/)     | 可简单地与 Google Compute 集成，减少了需要维护的基础架构，支持标签                                                          | 用于混合云配置或本地部署安装时并非最理想的选择，不支持 `docker logs`                        |


### 应用日志驱动注意事项

选择应用日志驱动时，请考虑下列因素：

* 如果日志数据高度敏感，则 **syslog** 和 **splunk** 是很好的选择，因为可以配置他们使用 TLS 来传输日志。
* **journald** 日志驱动非常适合用于保留 `docker logs` 的使用情况以及记录 Docker 守护进程日志。此驱动既方便故障排除，又实现日志可移植性。此驱动的另一个优点是，日志将首先在本地写入，因此减少了对日志记录基础架构的依赖。
* 如果 Docker EE 集群仅在一个云提供商处存在，则可以使用 **awslogs** 或 **gcplogs**。
* 如果存在现有的 Splunk 安装，则应使用 **splunk** 日志驱动。
* 如果环境中存在可以存储日志的 NoSQL 数据库，那么 **gelf** 和 **fluentd** 日志驱动都是好选择。
* 对于开发或测试环境，如果查看日志流比查看索引并搜索日志更为有效，那么使用 **json-file** 或 **journald** 可能很有帮助。(如果使用 **json-file**，请考虑传递 `max-size` 和 `max-file` 选项，以免日志填满文件系统。)

## 日志记录驱动设置导引

要实现全系统的日志记录，请在 `/etc/docker/daemon.json` 中创建一个条目。例如，使用下列代码来启用 `gelf` 输出插件：

    {
        “log-driver":“gelf",
        “log-opts":{
         "gelf-address":“udp://1.2.3.4:12201",
         “tag":"{{.ImageName}}/{{.Name}}/{{.ID}}"
        }
    }

然后重启 Docker 守护进程。所有日志记录驱动都可以通过使用 `/etc/docker/daemon.json` 文件以类似的方式配置。在上面的示例中使用了 `gelf` 日志驱动，`tag` 字段设置在收集日志时可以搜索和索引的附加数据。请参阅每种日志记录驱动的文档，了解可以从日志驱动设置的其他字段。

使用 `/etc/docker/daemon.json` 文件设置日志将会分别对每个节点设置默认日志记录行为。这些设置可以被每个服务或每个容器级别的设置覆盖。覆盖默认日志记录行为可能对故障排除很有用，因为可以实时查看日志。

如果在配置 `daemon.json` 文件使用 `gelf` 日志驱动的系统上创建服务，则*所有*在该主机上运行的容器日志都将转至 `gelf-address` 配置设置的地址。

如果希望使用其他日志记录驱动，例如用于查看来自容器的 `stdout` 的日志流，则可以临时覆盖默认日志记录行为。

    docker service create \
          --log-driver json-file --log-opt max-size=10m \
          nginx:alpine

然后可以配合 Docker 服务日志，更轻松地识别服务的问题。

## Docker 服务日志

`docker service logs` 是在 Docker EE 的 17.05 版和 17.06 版中推出的。当服务有多个从节点任务时，它可提供多路复用的日志流。只要输入 `docker service logs <service_id>`，这些日志就会在第一列显示发起任务名称，然后在右侧的列中显示每个从节点的实时日志。例如：

    $ docker service create -d --name ping --replicas=3 alpine:latest ping 8.8.8.8
    5x3enwyyr1re3hg1u2nogs40z

    $ docker service logs ping
    ping.2.n0bg40kksu8e@m00    | 64 bytes from 8.8.8.8: seq=43 ttl=43 time=24.791 ms
    ping.3.pofxdol20p51@w01    | 64 bytes from 8.8.8.8: seq=44 ttl=43 time=34.161 ms
    ping.1.o07dvxfx2ou2@w00    | 64 bytes from 8.8.8.8: seq=44 ttl=43 time=30.111 ms
    ping.2.n0bg40kksu8e@m00    | 64 bytes from 8.8.8.8: seq=44 ttl=43 time=25.276 ms
    ping.3.pofxdol20p51@w01    | 64 bytes from 8.8.8.8: seq=45 ttl=43 time=24.239 ms
    ping.1.o07dvxfx2ou2@w00    | 64 bytes from 8.8.8.8: seq=45 ttl=43 time=26.403 ms

在尝试查看包含多个从节点的服务的日志输出时，此命令很有帮助。通过实时查看跨多个从节点流式传输的日志，可以即时了解跨整个集群的服务问题，并进行故障排除。

## ELK 设置

请参阅 [swarm-elk 镜像仓库](https://github.com/ahromis/swarm-elk)获取关于在 Docker Swarm 上如何向 ELK 发送日志的信息。此镜像仓库包含一个 Docker Compose 文件，它会设置完整的 ELK 技术栈。该镜像仓库是试验使用 ELK 的 Docker 日志记录的良好起点，但是在将它用于生产之前，也要考虑高可用性和排队系统。

## Splunk 设置

Splunk 是另一种流行的日志记录实用程序。要设置 Splunk，请使用此 [swarm-splunk](https://github.com/ahromis/swarm-splunk) 镜像仓库中提供的 Docker Compose 文件，按相应步骤操作。

每个 Splunk 转发程序都连接到本地 Docker 套接字，因此在守护进程级别不需要其他日志驱动配置。连接本地 Docker 套接字也使 Splunk 在拉取日志之外还能拉取 Docker 容器统计信息。

## 传统应用现代化

没有理由认为只有现代化的应用才能使用容器记录日志。我们可以在不更改任何应用代码的情况下，使传统应用现代化，同时仍然保留现代化的日志记录的优点。

在理想情况下，应用会将日志发送到 `stdout`/`stderr`，而 Docker 将这些日志发送到配置的日志记录目标。有时某些应用配置为将日志发送到多个位置。如何捕获这些日志，而又不更改任何应用源代码？

可以创建一个 Docker 存储卷（或多个存储卷），指向应用中日志文件驻留的位置。利用 Docker 模板功能，可以给每个存储卷加上服务 ID（一个整数）后缀。如果有多个服务任务在同一个主机上运行，给存储卷名称加上后缀可以避免任何与日志记录相关的冲突。需要创建一个全局服务，使用目录通配符支持运行日志记录代理程序。最后，可以通过日志记录实用程序设置附加的正则表达式，将文件的源目录转变为带索引的值。

下面的示例显示了如何使用官方的 [Tomcat](https://store.docker.com/images/tomcat) 镜像实现此目的。官方的 Tomcat 镜像在 `/usr/local/tomcat/logs` 中记录多个文件，这与大多数 Java 应用的行为非常相似。在该路径下，可以找到 `catalina.2017-07-06.log`、`host-manager.2017-07-06.log`、`localhost.2017-07-06.log`、`localhost_access_log.2017-07-06.txt` 和 `manager.2017-07-06.log` 等文件。

1. 为日志记录实用程序创建一个挂载 `/var/lib/docker/volumes:/logs/volumes` 的全局服务。
2. 为使用与此一般示例类似的规则记录日志的日志记录代理程序创建日志记录规则：`“/log/volumes/*/_data/*.log"`。

3. 启动对存储卷使用基于 go 的模板操作的服务：

    启动该服务时，使用这些参数：

        docker service create \
        -d \
        --name prod-tomcat \
        --label version=1.5 \
        --label environment=prod \
        --mount type=volume,src="{{.Task.Name}}",dst=/usr/local/tomcat/logs \
        --replicas 3 \
        tomcat:latest

    如果两个从节点在同一个节点上调度，则将在主机 `prod-tomcat.1.oro7h0e5yow69p9yumaetor3l` 和 `prod-tomcat.2.ez0jpuqe2mkl6ppqnuffxqagl` 上创建两个包含日志的存储卷。

4. 只要日志记录代理程序支持通配符，并且通过检查索引节点（而不是文件）来处理所有日志轮替，那么应该就会收集日志。
5. 如果应用将日志记录到多个位置，则尝试建立从日志到单个目录的符号链接，或向存储卷添加描述性名称。如果已将描述性名称添加到存储卷名称，则任何类型的提取都需要经过更新才能容纳该更改。(即使用 `grok`)
6. 大多数日志记录器应该会收集文件路径以及日志内容。通过将日志文件驻留的存储卷转为可加索引的字段，就可以从这些类型的应用搜索和聚合信息。下面是一个示例，它使用 `grok` 模式，并创建了两个新的可索引字段 `CONTAINER_NAME` 和 `FILENAME`。

        match => { "path" => "/log/volumes/%{DATA:CONTAINER_NAME}/_data/%{GREEDYDATA:FILE_NAME}" }
7. `CONTAINER_NAME` 将匹配来自容器的 `stdout` 流输出，从而方便基于容器的日志进行过滤。

> 在 [swarm-elk 镜像仓库](https://github.com/ahromis/swarm-elk/tree/master/logstash)中可以找到更多关于某镜像仓库的信息和一个工作示例。

## Windows 日志记录

ETW 日志记录驱动将容器日志作为 ETW 事件转发。ETW 代表 Event Tracing in Windows（Windows 中的事件跟踪），是用于在 Windows 中跟踪应用的通用框架。每个 ETW 事件都包含一条消息，其中含有日志及其上下文信息。然后客户端可以创建 ETW 侦听器来侦听这些事件，并将它们转发到可以收集和分析日志的位置。

要在 Windows 上使用 ETW 驱动，请使用标志 `--log-driver=etwlogs` 创建服务或运行容器：

下面是一条示例事件消息：

    container_name: backstabbing_spence,
    image_name: windowsservercore,
    container_id: f14bb55aa862d7596b03a33251c1be7dbbec8056bbdead1da8ec5ecebbe29731,
    image_id: sha256:2f9e19bd998d3565b4f345ac9aaf6e3fc555406239a4fb1b1ba879673713824b,
    source: stdout,
    log:Hello world!

## 总结

Docker 在日志记录方面提供了很多选项，在采用平台时设计日志记录策略会很有帮助。对大多数系统来说，将日志数据留在主机上是不够的。如果具有索引和搜索的能力，以及自助服务平台，就可以让运维人员和开发人员获得更顺利的体验。

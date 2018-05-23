---
type: reference
version: 1
title: “Docker 参考架构：成规模地运行 Docker 企业版"
summary: “本指南帮助您规划和架设大规模 Docker 企业版部署"
redirectfrom:
  - running_docker_ee_at_scale
author:
  - friism
platform:
  - linux
  - "windows server"
testedon:
  - "ee-17.06.2-ee-6"
  - "ucp-2.2.5"
  - "dtr-2.4.1"
tags:
  - scaling
  - deploy
product:
  - EE
dateModified: “2018-01-22T21:59:46+00:00"
dateModified_unix: 1516658386
uniqueid: KB000578
---

## 简介

此参考架构将帮助您规划大规模 Docker 企业版部署。它涉及核心 Docker EE 平台、Universal Control Plane 以及 Docker Trusted Registry。请使用本指南来帮助确定 Docker EE 部署的硬件和基础架构规模，并确定针对您的具体工作负载的最佳配置。

## 您将学到的知识

对于 Docker EE、Universal Control Plane 和 Docker Trusted Registry，本指南涵盖了：

 * 哪些应用场景参数很可能会影响规模要求
 * 基于实际测试的已知规模限制
 * 确保良好性能和未来增长余地的最佳实践

## Docker EE 与 Docker Universal Control Plane

本节论述实现优化性能和增长潜力的基础 Docker EE 平台和 Universal Control Plane 配置。

### 管理节点的数量

对于生产集群，建议的管理节点数量是 3 个或 5 个。3 管理节点集群可以容许损失 1 个管理节点，而 5 管理节点集群可以容许 2 个管理节点暂时故障。具有更多管理节点的集群可以容许更多管理节点发生故障，但是增加管理节点也会增加维护开销和按 Docker Swarm Raft 法定多数提交集群状态的开销。在某些情况下，拥有较多管理节点（例如 5 个或 7 个）的集群可能（在集群更新延迟和吞吐量方面）不如只有 3 个管理节点但其他规格类似的集群。

一般而言，增加管理节点数量_不_会加快集群操作速度（在某些情况下，反而会使速度变慢），_不_会增加集群最大更新操作吞吐量，也_不_会增强集群能够管理的工作节点总数。

即使在管理节点发生故障，失去法定多数的情况下，集群上的服务和任务也会继续运行，并且保持稳态稳定（只不过在没有法定多数的情况下，无法更新集群状态）。因此，Docker 建议将投资花在个别管理节点故障后快速恢复的能力上（例如用于快速添加替换管理节点的自动化/脚本），而不要规划具有大量管理节点的集群。

1 个管理节点的集群应该仅用于测试和试验，因为损失管理节点就会导致集群损失。

> 请查看文档来获取[关于 Swarm 管理节点和工作节点配置的详细信息](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/)。

### 管理节点大小和类型

生产集群中的管理节点最好拥有至少 16GB 的 RAM 和 4 个 vCPU。Docker 进行的测试表明，拥有 16GB RAM 的管理节点即使用在有 100 个工作节点和众多服务、网络及其他元数据的集群中，也不会受限于内存大小。

生产集群中的管理节点应该_始终_对 `/var/lib/docker/swarm` 挂载点使用 SSD。Docker 在此目录中存储 swarm 集群状态，并且会在集群状态更新时进行众多小更新的读写。SSD 将确保以尽可能小的延迟提交更新。建议在用于测试和试验的集群中也使用 SSD 来确保良好性能。

提高 CPU 速度和数量，以及改善管理节点之间的网络延迟，也会提高集群性能。

### 工作节点大小和数量

对于工作节点而言，Docker 组件和代理程序的开销并不大 – 通常小于 1GB 内存。确定工作节点大小和数量的过程与您当前确定应用或 VM 环境大小的过程类似。例如，您可以确定负载下的应用内存工作集，并考虑每个应用需要多少从节点（确保任务失败时的耐用性和/或吞吐量）。这样一来，您对集群中各工作节点需要的总内存量就有了一个认识。

请记住，在工作节点发生故障时（或者您清空某个节点以进行升级或维修时），Docker Swarm 会自动重新调度任务，所以别忘了留出余地，使任务能够再平衡到其他节点。

还要记住的是，Docker 容器与虚拟机不同，与在容器外运行相比，在容器中运行应用所增加的内存或 CPU 开销极小或为零。如果将应用从各个 VM 移动到容器中，或者将许多应用合并到一个 Docker EE 集群中，那么您为此使用的资源将比当前使用的少得多。

### 任务分割和限制资源使用

在生产集群中，切勿在管理节点上运行工作负载。这是 Docker Universal Control Plane (UCP) 中的一种[可配置的管理节点设置](https://docs.docker.com/datacenter/ucp/2.2/guides/admin/configure/restrict-services-to-worker-nodes/)。

如果在集群上部署的任务和服务具有差异很大的资源概要，而且您希望对不同的任务（例如具有不同的磁盘、内存或 CPU 特征）使用不同的节点类型，可以使用[节点标记和服务约束](https://docs.docker.com/datacenter/ucp/2.2/guides/admin/configure/add-labels-to-cluster-nodes/)来控制 Swarm 对特定服务的任务调度。

您还可以[将节点加入集合](https://docs.docker.com/datacenter/ucp/2.2/guides/access-control/manage-access-with-collections/)以及[基于用户帐户和团队控制访问](https://docs.docker.com/datacenter/ucp/2.2/guides/access-control/isolate-nodes-between-teams/)。如果某些团队或个人经常要部署的应用会消耗许多资源，或者具有对其他团队运行的任务产生不利影响的扰邻特征，用这种方法可以有效隔离他们所管理的任务。请参见 [RBAC 知识库文章](https://success.docker.com/article/RBAC_Example-Overview)了解关于如何使用 Docker 企业版设计团队和项目结构的示例。

### 资源约束

Docker EE 支持对容器和服务任务应用资源限制。Docker 建议在创建服务时使用 `--reserve-memory=<value>` 和 `--limit-memory=<value>` 参数。这些参数让 Docker EE 可以根据预期的内存消耗，更好地在工作节点上打包任务。

此外，分配一个全局（每节点 1 个实例）“幽灵”服务也许是个好主意，它可以在每个节点上保留一部分（例如 2GB）内存，供非 Docker 系统服务使用。因为 Docker Swarm 当前不会考虑非 Docker 管理的工作负载所消耗的工作节点内存，所以这个方法很有意义：

    docker service create --name system-reservation --reserve-memory 2G --limit-memory 2G --reserve-cpu 1 --mode global nginx:latest

(`nginx` 在此服务中实际上不执行任何工作。)（可以使用任何不会消耗大量内存或 CPU 的小镜像取代 `nginx`）。

> 请查看关于[容器资源约束](https://docs.docker.com/engine/admin/resource_constraints/)和[为服务保留内存或 CPU](https://docs.docker.com/engine/swarm/services/#reserve-memory-or-cpus-for-a-service) 的文档。

### 磁盘空间

对于生产集群，您需要关注几个影响工作节点磁盘空间使用的因素：

 * 工作节点上使用中的 Docker 容器镜像
 * 为容器创建的本地 Docker 存储卷
 * 工作节点上存储的容器日志
 * 工作节点上存储的 Docker 引擎日志
 * 容器写入的临时数据

### 工作节点上的容器镜像

要确定该为使用中的镜像分配多少空间，请尝试将一些应用放入容器，然后查看产生的镜像有多大。请注意，Docker 镜像包括多个层，如果有多个容器使用同一个层（对于 `ubuntu` 之类的 OS 层或 `openjdk` 之类的语言框架层来说很常见），在任何一个节点或 Docker Trusted Registry 上只会存储和使用该层的一个副本。层共享也意味着部署应用的新版本通常只会使节点上被占用的空间增加相对较小的幅度（因为只有容纳应用的最上数层发生更改）。

> 请注意，Docker Windows 容器镜像往往会变得比 Linux 容器镜像大。

为了持续监视使用中的容器镜像存储，应该尽量确保应用镜像从通用的基础镜像衍生。还可考虑运行定期脚本或 Cron 作业来修剪不使用的镜像，特别是在节点要处理许多镜像更新的情况下（例如比较频繁地发生部署的构建服务器或测试系统）。请参见[关于镜像修剪的文档获取详细信息](https://docs.docker.com/engine/admin/pruning/#prune-images)。

### 日志

对于生产集群，Docker 建议使用日志记录驱动或其他第三方服务聚合容器日志。只有 `json-file`（可能还包括 `journald`）日志驱动会导致容器日志在节点上累积，在这种情况下，要注意轮替或删除旧的容器日志。请参见[日志记录设计与最佳实践](https://success.docker.com/article/Docker_Reference_Architecture-\_Docker_Logging_Design_and_Best_Practices)获取详细信息。

Docker 引擎日志存储在工作节点和管理节点上。产生的引擎日志数量根据工作负载和引擎设置而变。例如，`debug` 日志级别会导致系统写入更多日志。应该使用 [logrotate](http://manpages.ubuntu.com/manpages/xenial/man8/logrotate.8.html) 之类的实用程序管理引擎日志（压缩并最终删除）。

### Overlay 网络和网格路由

Docker EE 附带受支持的内置 Overlay 网络驱动，用于实现配合 Docker Swarm 使用的多主机网络。Overlay 网络会造成与封装网络流量和管理 IP 地址及其他跟踪网络任务和服务的元数据有关的开销。

Docker EE 客户如果有网络吞吐量要求非常高或者工作负载动态性极高（集群或服务的更新频率很高）的应用，应该考虑尽量降低对开箱即用的 Docker Overlay 网络和网格路由的依赖。要达到这一目的，有多种方法：

 * 使用[主机模式发布](https://docs.docker.com/engine/swarm/services/#publish-a-services-ports-directly-on-the-swarm-node)取代网格路由
 * 使用 [`macvlan` 驱动](https://success.docker.com/article/Docker_Reference_Architecture-_Designing_Scalable,_Portable_Docker_Container_Networks#macvlan)，它的性能可能好于默认驱动
 * 使用非 Docker 服务发现机制（例如 Consul）
 * 考虑使用 `dnsrr` 取代 `vip` 服务端点
 
如果 Overlay 网络由使用基于 VIP 的端点模式创建（默认）的服务所使用，则网络大小不应该超过 `/24` 个含 256 个 IP 地址的区块（默认）。用户不应该通过增加 IP 区块大小来绕过此限制，而应该使用 `dnsrr` 端点模式或使用多个较小的 Overlay 网络。

还应注意的是，如果对一个 Overlay 网络分配了大量任务，例如有许多任务关联到该网络或者网络上的服务扩展到许多从节点，那么 Docker EE 可能会遇到 IP 枯竭的问题。在因为节点故障而重新调度任务时，也可能出现该问题。当前如果发生节点故障，Docker 会等待 24 小时以释放 Overlay IP 地址。可通过在 Docker 守护进程日志中查找 `failed to allocate network IP for task` 消息来诊断该问题。

### HTTP 网格路由

含有 Universal Control Plane 的 Docker 企业版附带内置的 [HTTP 网格路由](https://docs.docker.com/datacenter/ucp/2.2/guides/user/services/use-domain-names-to-access-services/)功能。HTTP 网格路由因为增加了网络跳数和路由控制，会增加一些开销，应该仅用于管理对外部暴露的服务的网络。对于在 Docker 上托管的服务之间的网络和路由，只须使用标准的内置 Docker Overlay 网络即可获得最佳性能。

## Docker Trusted Registry

本节论述用于实现扩展和性能的 Docker Trusted Registry 配置。

### 存储驱动

Docker Trusted Registry 支持[种类繁多的存储后端](https://docs.docker.com/datacenter/dtr/2.4/guides/admin/configure/external-storage/)。就扩展而言，后端类型可以分为基于文件系统（NFS、绑定式挂载、存储卷）和基于云/blob（AWS S3、Swift、Azure Blob Storage、Google Cloud Storage）。

对于某些用途而言，基于云/blob 的存储比基于文件系统的存储更有利于提高性能。这是因为 DTR 可以将层 `GET` 请求从客户端直接重定向到后备存储。通过这一操作，由 Docker 客户端拉取的实际镜像内容就不必通过 DTR 传输，而是可由 Docker 客户端从后备存储直接获取（只要 DTR 已经获取元数据并检查凭证）。

在使用基于文件系统的存储（例如 NFS）时，要确保 DTR 性能不受基础架构约束。常见的瓶颈包括主机网络接口卡、随 DTR 部署的负载均衡器、后端存储系统的吞吐量 (IOPS) 和延迟，以及 DTR 从节点主机的 CPU/内存。

Docker 已经测试过 DTR 性能，确定它可以使用带有 3 个从节点的 NFS 后备存储，处理 1 GB 容器镜像的 1400 多个并发拉取。

### 总计存储

了解未来镜像存储总计要求的最佳办法是收集和分析下列数据：

 * 镜像平均大小
 * 镜像更新/推送的频率
 * 镜像更新大小的平均大小（要考虑许多镜像可能共享通用基础层）
 * 存储旧镜像工件的保留策略和要求

使用 Docker Trusted Registry [垃圾回收](https://docs.docker.com/datacenter/dtr/2.4/guides/admin/configure/garbage-collection/)结合（使用 [DTR API](https://docs.docker.com/apidocs/overview/)）删除旧镜像的脚本或其他自动化工具，保持对存储使用的控制。

### 存储性能

当许多开发人员或构建机器同时向 Docker Trusted Registry 推送镜像时，DTR 写负载很可能会高涨。 

当新的镜像版本推送到 DTR，然后部署到有许多拉取更新镜像的实例的大型 Docker EE 集群，读负载很可能会高涨。

如果同样的 DTR 集群实例既用于开发人员/构建服务器工件存储，也用于大型生产 Docker EE UCP 集群的生产镜像工件存储，那么 DTR 集群实例就将同时遇到高涨的写负载和读负载。对于规模非常大的部署，请考虑使用两个（或更多）DTR 集群 - 一个专门用于支持写入镜像的开发人员和构建服务器，另一个可以处理由生产部署造成的非常高的瞬时读负载。

在估算 DTR 性能要求，请考虑平均镜像大小和镜像更新大小，将对 DTR 设置进行推送和拉取的开发人员和构建服务器数量，以及将在部署期间并发拉取更新镜像的生产节点数量。确保您有足够的 DTR 实例，而且您的后备存储有足以处理峰值负载的读写吞吐量。

要增加镜像拉取吞吐量，请考虑使用 [DTR 缓存](https://docs.docker.com/datacenter/dtr/2.4/guides/admin/configure/deploy-caches/#deploy-a-simple-cache)作为添加更多从节点的替代方法。

### 从节点数量

Docker Trusted Registry 维持法定多数的从节点，它们存储关于镜像仓库、镜像、标签和其他 DTR 对象的元数据。3 个从节点是实现高可用性部署的最低从节点数。1 个从节点的部署应该仅用于测试和试验。

当使用多个 DTR 从节点时，要[配置负载均衡器](https://docs.docker.com/datacenter/dtr/2.4/guides/admin/configure/use-a-load-balancer/)，使请求能够分布到所有 DTR 从节点。

如果一个 DTR 集群有 5 个或 7 个从节点，它提交元数据更新（例如镜像推送或标签更新）所花的时间可能长于有 3 个从节点的集群，因为它要花更多时间使更新传播到更大的法定多数。

如果使用 [DTR 安全扫描](https://docs.docker.com/datacenter/dtr/2.4/guides/admin/configure/set-up-vulnerability-scans/)，请注意 DTR 将对每个 DTR 从节点最多运行一个并发扫描。添加更多 DTR 从节点（或更改到配有速度更快的硬件的从节点）将增加 DTR 扫描吞吐量。请注意，当前在更新漏洞数据库之后，DTR 不会重新扫描存储的镜像。如果更新大量镜像，极有可能导致队列中积压众多扫描。

总的来说，您可能需要考虑使用 3 个以上 DTR 从节点以实现：

 * 在基于 NFS 的设置上超过 3 个从节点处理能力的峰值镜像推送/拉取吞吐量
 * 超过 3 个并发镜像漏洞扫描
 * 发生 1 个以上瞬时 DTR 从节点故障时的耐用性

### 元数据大小和集群操作

Docker Trusted Registry 将关于镜像仓库、镜像、标签和其他对象的元数据存储在数据库中（用户数据由 Universal Control Plane 维护）。您可以通过检查 `dtr-rethink` 容器中 `/data` 目录的大小来确定 DTR 数据库的大小。

完成从节点连接、备份和还原等 DTR 集群操作所需的时间由 DTR 保存的元数据数量决定。

## 集群大小

如果您正在规划将由多个团体或业务单位使用的大型 Docker EE 部署，应该考虑是运行单个集群还是多个集群（例如每个业务单位一个集群）。两种选项都是可行的，但是通常来说，使用一个或少数几个集群可以让您获得更大的整合效益。

Docker 企业版拥有强大的[基于团队的多租户控制功能](https://docs.docker.com/datacenter/ucp/2.2/guides/access-control/isolate-nodes-between-teams/)，包括指定工作节点仅运行由特定团队创建的任务和服务。如果使用这些功能配合单个或少数几个集群，就可以让多个业务单位或团体使用 Docker 企业版，而不必承担配置和运行多个集群的开销。

即便如此，使用多个集群仍有很好的理由：

 * **存储**：即使对规模较小的部署而言，将非生产集群与生产集群分隔也是个好主意。这样就可以先对关键更改和更新进行隔离测试，然后再部署到生产中。如果存在许多阶段（测试、QA、数据移动、生产等），还可以进行更细的划分。
 * **团队或应用分隔**：Docker EE [基于团队的多租户控制](https://docs.docker.com/datacenter/ucp/2.2/guides/access-control/isolate-nodes-between-teams/)允许多个应用安全地在同一个集群中运行，但如果安全要求较为严格，可能就必须分隔集群。
 * **区域**：区域冗余性、合规法律或延迟都可以成为将工作负载分到多个集群中的理由。

同样的考虑也适用于规划要使用的 DTR 集群数量。请注意，含有 Universal Control Plane 和 DTR 的 Docker EE 当前[仅限于在 UCP 和 DTR 集群实例之间进行一对一映射](https://success.docker.com/article/Can_multiple_UCP_clusters_share_one_DTR)，但多个 UCP 集群可以共享一个 DTR 集群，只是有一些功能限制。

## 总结

在规划 Docker EE 部署时考虑扩展需求，将有助于在工作负载增长时维持最佳性能、充足的磁盘空间，等等。而且您还能够以零或极少的停机时间执行更新。使用本指南进行大规模 Docker 企业版部署的规划和架构设计时，还应考虑 [Docker EE 最佳实践与设计注意事项](/article/Docker_Reference_Architecture-_Docker_EE_Best_Practices_and_Design_Considerations)中的建议。

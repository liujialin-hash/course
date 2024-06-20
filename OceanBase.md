## 新建Schemas

在 OceanBase 集群的 Oracle 模式下创建数据库的过程类似于在 Oracle 数据库中创建数据库的过程，但需要注意一些 OceanBase 特有的配置和步骤。以下是详细的步骤教程：

### 步骤 1：准备工作

1. **安装和配置 OceanBase 集群**：确保你的 OceanBase 集群已经正确安装并配置。
2. **连接到 OceanBase 集群**：使用客户端工具（如 OBClient）连接到 OceanBase 集群。

### 步骤 2：创建租户

在 OceanBase 中，数据库实例被称为租户（Tenant）。首先需要创建一个租户。

```sql
CREATE TENANT your_tenant_name
CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci
SET resource_pool_list='your_resource_pool_list';
```

- `your_tenant_name`：替换为你想要创建的租户名称。
- `your_resource_pool_list`：替换为你为这个租户分配的资源池列表。

### 步骤 3：切换到新租户

使用 OBClient 连接到新创建的租户。

```shell
obclient -h your_host -P your_port -u root@your_tenant_name -p
```

- `your_host`：OceanBase 集群的主机地址。
- `your_port`：OceanBase 集群的端口号。
- `your_tenant_name`：新创建的租户名称。

### 步骤 4：创建用户和授权

在新租户中创建一个用户，并赋予必要的权限。

```sql
CREATE USER your_user IDENTIFIED BY your_password;

GRANT ALL PRIVILEGES TO your_user;
```

- `your_user`：替换为你想要创建的用户名称。===>**用户名大写**
- `your_password`：替换为用户的密码。===>**密码暂时不支持特殊字符**

### 步骤 5：创建数据库

在 OceanBase 的 Oracle 模式下，数据库的概念对应于 Oracle 的 Schema。你可以通过创建 Schema 来模拟数据库。

```sql
CREATE SCHEMA your_schema AUTHORIZATION your_user;
```

- `your_schema`：替换为你想要创建的 Schema 名称。
- `your_user`：替换为你在步骤 4 中创建的用户名称。

### 步骤 6：验证创建

切换到新创建的 Schema，验证数据库的创建。

```sql
ALTER SESSION SET CURRENT_SCHEMA = your_schema;

SELECT * FROM all_users;
```

### 示例总结

以下是一个完整的示例，包括所有步骤的具体命令：

```
-- Step 2: Create a tenant
CREATE TENANT my_tenant CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci SET resource_pool_list='pool1';

-- Step 3: Connect to the new tenant (run this in your terminal)
obclient -h localhost -P 2881 -u root@my_tenant -p

-- Step 4: Create a user and grant privileges
CREATE USER my_user IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES TO my_user;

-- Step 5: Create a schema
CREATE SCHEMA my_schema AUTHORIZATION my_user;

-- Step 6: Verify creation
ALTER SESSION SET CURRENT_SCHEMA = my_schema;
SELECT * FROM all_tables;
```

通过以上步骤，你可以在 OceanBase 集群的 Oracle 模式下成功创建一个数据库（Schema）。确保每一步的参数和配置符合你的实际环境和需求。



## 存储过程删除全部表

```sql
BEGIN
    FOR r IN (SELECT table_name AS table_name FROM all_tables) LOOP
        EXECUTE IMMEDIATE 'DROP TABLE "' || LOWER(r.table_name) || '" CASCADE CONSTRAINTS';
    END LOOP;
END;	
```



## 序列问题

### 查询序列

```sql
select * from user_sequences
```

### 新建序列

```sql
create sequence sequence_name start with 1 minvalue 1 maxval 999 increment by 1 cycle noorder cache 20;
```

### 跳转序列

```sql
--查询当前序列号
select sequence_name.currval from dual;
--如果没启用
select sequence_name.nextbal from dual;
--计算需要调整步长
declare current_value number; target_value :='目标序列'; increment number; begin select sequence_name.currval into current_vallue from dual; increment :=target_value - current_value - 1 ; dbms_output.put_line('计算结结果为' || increment); end;
--调整步长
alter sequence sequence_name increment by 'increment';
--生成新的序列
select sequence.nextval from dual;
--还原步长
alter sequence sequence_name increment by '原长度'
```

### 删除序列

```sql
drop sequence sequence_name;
```

## OceanBase的服务

| 服务名     |                | 作用                                                         |
| ---------- | -------------- | ------------------------------------------------------------ |
| observer   | ob服务         | 是 OceanBase 的核心组件之一，用于管理和监控整个 OceanBase 集群的状态和性能。它负责监视各个节点的运行状况，并收集性能指标和日志信息。 |
| obagent    | ob代理监控     | 是 OceanBase 的代理工具，用于收集和传输监控数据到监控系统（如 Prometheus）。它通常与 Observer 配合使用，帮助监控整个集群的健康状态 |
| obproxy    | ob代理负载均衡 | 是 OceanBase 的代理服务器，用于负载均衡和路由查询请求。它通常用于在客户端和 OceanBase 集群之间建立连接，提高性能和安全性。 |
| prometheus | 报警工具       | 是一种开源的监控和警报工具，用于收集和存储时间序列数据。它通常与 `obagent` 结合使用，用于监控 OceanBase 集群的性能指标 |
| grafana    | 可视化         | 是一种开源的数据可视化工具，通常与 Prometheus 结合使用，用于创建仪表板和可视化监控数据。它可以与 `obagent` 和 `Prometheus` 集成，帮助用户实时监控 OceanBase 集群的性能和状态 |

## 组件介绍

- OBD

  OceanBase Deployer，OceanBase 安装部署工具，简称为 OBD。详细信息请参考 [OBD 文档](https://www.oceanbase.com/docs/obd-cn)。

- ODP

  OceanBase Database Proxy，OceanBase 数据库代理，是 OceanBase 数据库专用的代理服务器，简称为 ODP（又称为 OBProxy）。详细信息请参考 [ODP 文档](https://www.oceanbase.com/docs/odp-cn)。

- OCP Express

  基于 Web 的 OceanBase 数据库 4.x 管理工具，融合在 OceanBase 数据库集群中，支持对数据库集群关键性能及基本数据库管理功能。详细信息请参考 [OceanBase 云平台 Express (OCP Express)](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818967)。

- OBAgent

  OBAgent 是 OceanBase 数据库监控采集框架，支持推、拉两种数据采集模式，可以满足不同的应用场景。

- Grafana

  Grafana 是一款开源的数据可视化工具，它可以将数据源中的各种指标数据进行可视化展示，以便更直观地了解系统运行状态和性能指标。详细信息可参见 [Grafana 官网](https://grafana.com/)。

- Prometheus

  Prometheus 是一个开源的服务监控系统和时序数据库，其提供了通用的数据模型以及快捷数据采集、存储和查询接口。详细信息可参见 [Prometheus 官网](https://prometheus.io/)。

## 方案介绍

为了助力您快速上手体验 OceanBase 数据库，我们提供了如下三种不同的方案实现快速部署 Oceanbase 数据，您可以根据自身环境情况灵活选择。

- 方案一：部署 OceanBase 演示环境

  此方案适用于仅有一台机器时，快速搭建一个可用的 OceanBase 数据库环境。部署的 OceanBase 数据库环境具备数据库的基本功能，可以有效地帮助您了解 OceanBase 数据库；但是该环境不具备任何分布式能力及高可用特性，不建议长期使用。具体操作步骤请参考 **方案一：部署 OceanBase 演示环境**。

- 方案二：部署 OceanBase 集群环境

  此方案适用于需要深入了解 OceanBase 分布式数据库架构原理及功能特性的用户。部署的 OceanBase 集群具备数据库完整能力及分布式高可用的特性。该方案需要您至少准备三台可用资源为 4vCPU、10 GB 内存、50 GB 磁盘的主机。具体操作步骤请参考 **方案二：部署 OceanBase 集群环境**。

- 方案三：部署 OceanBase 容器环境

  此方案适用于希望通过容器实现部署、管理 OceanBase 数据库的用户。该方案未经过规模化的验证，建议谨慎使用。操作步骤请参考 **方案三：部署 OceanBase 容器环境**。

## 前提条件

在参考本文安装 OceanBase 数据库之前，确保您的软硬件环境满足以下要求：

| 项目              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| 系统              | Alibaba Cloud Linux 2/3 版本（内核 Linux 3.10.0 版本及以上）Anolis OS 8.X 版本（内核 Linux 3.10.0 版本及以上）Red Hat Enterprise Linux Server 7.X 版本、8.X 版本（内核 Linux 3.10.0 版本及以上）CentOS Linux 7.X 版本、8.X 版本（内核 Linux 3.10.0 版本及以上）Debian 9.X 版本及以上版本（内核 Linux 3.10.0 版本及以上）Ubuntu 20.X 版本及以上版本（内核 Linux 3.10.0 版本及以上）SUSE / OpenSUSE 15.X 版本及以上版本（内核 Linux 3.10.0 版本及以上）KylinOS V10 版本统信 UOS 1020a/1021a/1021e/1001c 版本中科方德 NFSChina 4.0 版本及以上浪潮 Inspur kos 5.8 版本 |
| CPU               | 最低要求 2 核，推荐 4 核及以上。                             |
| 内存              | 最低要求 8 GB，推荐设置在 16 GB 至 1024 GB 范围内。          |
| 磁盘类型          | 使用 SSD 存储。                                              |
| 磁盘存储空间      | 最低要求 19 GB。                                             |
| 文件系统          | EXT4 戓 XFS，当数据超过 16T 时，使用 XFS。                   |
| all-in-one 安装包 | all-in-one 安装包需选择 V4.1.0 及以上版本。                  |
| Docker            | 使用 Docker 部署 OceanBase 数据库时需提前安装 Docker 并启动 Docker 服务，详细操作请参考 [Docker 文档](https://docs.docker.com/get-docker/)。 |

#### 说明

以下内容以 x86 架构的 CentOS Linux 7.9 镜像作为环境，其他环境可能略有不同。

## 方案一：部署 OceanBase 演示环境

当您仅拥有一台可用机器时，您可参考本节内容使用 `obd demo` 命令快速部署单机 OceanBase 数据库。

### 步骤一：下载并安装 all-in-one 安装包

1. 请从 [OceanBase 软件下载中心](https://www.oceanbase.com/softwarecenter) 下载最新版本 all-in-one 安装包，并将其上传到机器任一目录下。

2. 在安装包所在目录下执行如下命令解压安装包并安装。

   ```shell
   [admin@test001 ~]$ tar -xzf oceanbase-all-in-one-*.tar.gz
   [admin@test001 ~]$ cd oceanbase-all-in-one/bin/
   [admin@test001 bin]$ ./install.sh
   [admin@test001 bin]$ source ~/.oceanbase-all-in-one/bin/env.sh
   ```

### 步骤二：单机部署 OceanBase 数据库

1. 执行以下命令，快速部署 OceanBase 数据库

   ```shell
   [admin@test001 ~]$ obd demo
   ```

   `obd demo` 命令默认在当前用户家目录下以最小规格部署并启动 OceanBase 数据库及相关组件（包括 ODP、OBAgent、Grafana 和 Prometheus），固定部署名为 `demo`。如需更多定制化的部署形式，参见 [快速部署命令](https://www.oceanbase.com/docs/community-obd-cn-10000000001690698)。

   您也可以使用 OBD 命令管理该 OceanBase 数据库，详细命令介绍请参考 [集群命令组](https://www.oceanbase.com/docs/community-obd-cn-10000000001690699)。

   #### 说明

   当安装 grafana 或 prometheus 时，会输出 grafana 或 prometheus 的访问地址。在阿里云或其他云环境下，可能出现因无法获取公网 IP 而输出内网地址的情况，此 IP 非公网地址，您需要使用正确的地址。

2. 执行输出中的连接命令连接数据库

   `obd demo` 命令成功执行后会输出通过 OBClient 连接 OceanBase 数据库的命令，示例如下。

   - 通过 2881 端口直连数据库

     ```shell
     [admin@test001 ~]$ obclient -h127.0.0.1 -P2881 -uroot@sys -Doceanbase -A
     ```

   - 通过 ODP 代理访问数据库

     ```shell
     [admin@test001 ~]$ obclient -h127.0.0.1 -P2883 -uroot@sys -Doceanbase -A
     ```

   使用 OBClient 客户端连接 OceanBase 集群的详细操作可参见 [通过 OBClient 连接 OceanBase 租户](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818872)。连接 OceanBase 数据库更多方法请参见 [连接方式概述](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818874)。

3. （可选）配置密码

   使用 `obd demo` 命令快速部署 OceanBase 数据库后，您可参考如下步骤为 demo 集群配置密码。

   1. 修改配置文件

      ```shell
      obd cluster edit-config demo
      ```

      执行上述命令打开配置文件后，在配置文件中 oceanbase-ce（社区版）/oceanbase（企业版） 组件下添加 `root_password: xxxx`，添加完成后保存退出。示例如下：

      ```yaml
      oceanbase-ce:
        servers:
          - 127.0.0.1
        global:
          home_path: /home/admin/oceanbase-ce
          ... # 省略部分配置项
          log_disk_size: 13G
          root_password: ******
      ```

   2. 重启集群

      修改并保存配置文件后，OBD 会输出待执行的重启命令，直接复制执行即可，示例如下。

      ```shell
      [admin@test001 ~]$ obd cluster edit-config demo
      Search param plugin and load ok
      Search param plugin and load ok
      Parameter check ok
      Save deploy "demo" configuration
      Use `obd cluster reload demo` to make changes take effect.
      Trace ID: 29dd12fa-3d73-11ee-91bc-00163e01cd7a
      If you want to view detailed obd logs, please run: obd display-trace 29dd12fa-3d73-11ee-91bc-00163e01cd7a
      ```

      从输出可以看出，修改配置文件中 root@sys 用户密码后，需执行 `obd cluster reload demo` 命令重启 demo 集群。

   #### 说明

   您也可使用 root 用户登录数据库的 sys 租户后，通过 `ALTER USER` 命令修改用户密码。详细操作可参见 [ALTER USER](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821988)。

## 方案二：部署 OceanBase 集群环境

当您拥有多台可用机器时，可参考本节内容使用 obd web 命令启动白屏，在白屏界面部署分布式 OceanBase 集群。

#### 说明

本节仅介绍简单的操作，帮助您快速部署一个分布式 OceanBase 集群。更多配置介绍可参考 [通过 OBD 白屏部署 OceanBase 集群](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821384) 一文。

### 步骤一：下载并安装 all-in-one 安装包

1. 请从 [OceanBase 软件下载中心](https://www.oceanbase.com/softwarecenter) 下载最新版本 all-in-one 安装包，并将其上传到机器任一目录下。

2. 在安装包所在目录下执行如下命令解压安装包并安装。

   ```shell
   [admin@test001 ~]$ tar -xzf oceanbase-all-in-one-*.tar.gz
   [admin@test001 ~]$ cd oceanbase-all-in-one/bin/
   [admin@test001 bin]$ ./install.sh
   [admin@test001 bin]$ source ~/.oceanbase-all-in-one/bin/env.sh
   ```

### 步骤二：使用白屏部署 OceanBase 数据库

1. 命令行执行 `obd web` 命令启动白屏界面，根据输出地址登录白屏界面并单击 **开启体验之旅** 开始部署。

   ```shell
   [admin@test001 ~]$ obd web
   start OBD WEB in 0.0.0.0:8680
   please open http://172.xx.xxx.233:8680
   ```

   #### 说明

   - 白屏界面默认使用 8680 端口，您可使用 `obd web -p <PORT>` 命令指定端口。
   - 在阿里云或其他云环境下，可能出现程序无法获取公网 IP，从而输出内网地址的情况，此 IP 非公网地址，您需要使用正确的地址访问白屏界面。
   - `obd web` 命令绑定在 0.0.0.0 上，在多网卡部署的情况下，您可通过任一一个可访问的 IP 访问白屏界面。

2. 在 **部署配置** 界面修改 **集群名称** 并勾选待部署组件，也可不做修改，使用默认配置（默认部署所有组件）。单击 **下一步** 进入 **节点配置** 页面。

   

3. 在 **节点配置** 页面输入节点 IP 和用户密码，单击 **下一步** 进入 **集群配置** 页面。

   

4. 在 **集群配置** 页面配置集群的部署模式、密码、目录、端口以及更多配置，您也可不做修改，使用默认配置。单击 **下一步** 进入 **预检查** 页面。

   

5. 在 **预检查** 页面查看配置信息，无误后单击 **预检查** 进行检查。若预检查报错，您可根据页面建议选择 **自动修复** 或者单击 了**解更多方案** 跳转至错误码文档，参考文档自行修改。所有报错修改后，可单击 **重新检查** 再次进行预检查。

   

6. 预检查通过后，单击 **部署** 开始 OceanBase 集群的部署。部署成功会输出各个组件的连接方式，您可复制进行访问。

   

7. 单击 **完成**，结束部署流程。

8. 使用 OBClient 客户端连接 OceanBase 数据库，或者登录 OCP Express 白屏界面管理集群。

   使用 OBClient 客户端连接 OceanBase 集群的详细操作可参见 [通过 OBClient 连接 OceanBase 租户](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818872)。连接 OceanBase 数据库更多方法请参见 [连接方式概述](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818874)。

   - 通过 2881 端口直连数据库，以直连 10.10.10.1 节点为例

     ```shell
     [admin@test001 ~]$ obclient -h10.10.10.1 -P2881 -uroot@sys -p -Doceanbase -A
     ```

   - 通过 ODP 代理访问数据库，以 ODP 所在节点为 10.10.10.1 为例

     ```shell
     [admin@test001 ~]$ obclient -h10.10.10.1 -P2883 -uroot@sys -p -Doceanbase -A
     ```

## 方案三：部署 OceanBase 容器环境

您可参考本节内容在 Docker 容器里启动 OceanBase 数据库，帮助您快速了解 OceanBase 数据库。

### （可选）步骤一：拉取 OceanBase 数据库镜像

运行如下命令，拉取 OceanBase 数据库所需镜像。

- 搜索 OceanBase 数据库相关镜像

  ```shell
  [admin@test001 ~]$ docker search oceanbase
  ```

- 拉取 OceanBase 数据库最新镜像

  ```shell
  [admin@test001 ~]$ docker pull oceanbase/oceanbase-ce
  ```

  #### 说明

  上述命令默认拉取最新版本，可根据实际需求在 [Docker 镜像](https://hub.docker.com/r/oceanbase/oceanbase-ce/tags) 中选择版本。

### 步骤二：启动 OceanBase 数据库实例

运行如下命令，启动 OceanBase 数据库实例。

- 根据当前容器部署最大规格实例

  ```shell
  [admin@test001 ~]$ docker run -p 2881:2881 --name obstandalone -e MINI_MODE=0 -d oceanbase/oceanbase-ce
  ```

- 部署 mini 的独立实例

  ```shell
  [admin@test001 ~]$ docker run -p 2881:2881 --name obstandalone -e MINI_MODE=1 -d oceanbase/oceanbase-ce
  ```

启动预计需要 2~5 分钟。执行以下命令，如果返回 `boot success!`，则表示启动成功。

```shell
[admin@test001 ~]$ docker logs obstandalone | tail -1
boot success!
```

### 步骤三：连接 OceanBase 数据库实例

oceanbase-ce 镜像安装了 OceanBase 数据库客户端 OBClient，并提供了默认连接脚本 ob-mysql。

```shell
# 使用 root 用户登录集群的 sys 租户
[admin@test001 ~]$ docker exec -it obstandalone ob-mysql sys

# 使用 root 用户登录集群的 test 租户
[admin@test001 ~]$ docker exec -it obstandalone ob-mysql root

# 使用 test 用户登录集群的 test 租户
[admin@test001 ~]$ docker exec -it obstandalone ob-mysql test
```

您也可以运行以下命令，使用您本机的 OBClient 或者 MySQL 客户端连接实例。

#### 说明

在 Docker 容器中启动 OceanBase 数据库后，您可登录集群执行 `ALTER USER` 命令修改用户密码，命令详细介绍请参见 [ALTER USER](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821988)。

```shell
[admin@test001 ~]$ obclient -uroot@sys -h127.1 -P2881
```

连接成功后，终端将显示如下内容：

```shell
[admin@test001 ~]$ docker exec -it obstandalone ob-mysql sys

login as root@sys
Command is: obclient -h127.1 -uroot@sys -A -Doceanbase -P2881
Welcome to the OceanBase.  Commands end with ; or \g.
Your OceanBase connection id is 3221487727
Server version: OceanBase_CE 4.1.0.0 (r100000192023032010-0265dfc6d00ff4f0ff4ad2710504a18962abaef6) (Built Mar 20 2023 10:12:57)

Copyright (c) 2000, 2018, OceanBase and/or its affiliates. All rights reserved.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

obclient [oceanbase]>
```


















































---
title: 使用 Striim 将数据迁移到 Azure 宇宙数据库 SQL API 帐户
description: 了解如何使用 Striim 将数据从 Oracle 数据库迁移到 Azure Cosmos DB SQL API 帐户。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "71003276"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>使用 Striim 将数据迁移到 Azure 宇宙数据库 SQL API 帐户
 
Azure 应用商店中的 Striim 映像提供从数据仓库和数据库到 Azure 的持续实时数据移动。 移动数据时，可以执行在线非规范化、数据转换、启用实时分析和数据报告方案。 使用 Striim 可以立即开始将企业数据移动到 Azure Cosmos DB SQL API 是很容易的。 Azure 提供了一个应用商店产品，使部署 Striim 和将数据迁移到 Azure Cosmos DB 变得容易。 

本文演示如何使用 Striim 将数据从**Oracle 数据库**迁移到 Azure **Cosmos DB SQL API 帐户**。

## <a name="prerequisites"></a>先决条件

* 如果没有[Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，请先创建[一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。然后开始。

* 本地运行的 Oracle 数据库，其中有一些数据。

## <a name="deploy-the-striim-marketplace-solution"></a>部署斯特里姆市场解决方案

1. 登录到[Azure 门户](https://portal.azure.com/)。

1. 选择 **"创建资源并在**Azure 应用商店中搜索**Striim"。** 选择第一个选项并**创建**。

   ![查找斯特里姆市场项目](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. 接下来，输入 Striim 实例的配置属性。 斯特里姆环境部署在虚拟机中。 在 **"基本"** 窗格中，输入**VM 用户名**VM**密码**（此密码用于将 SSH 放入 VM）。 选择要部署 Striim 的**订阅**、**资源组**和**位置详细信息**。 完成后，选择 **"确定**"。

   ![配置斯特里伊姆的基本设置](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. 在 **"斯特里姆群集设置"** 窗格中，选择斯特里姆部署的类型和虚拟机大小。

   |设置 | “值” | 描述 |
   | ---| ---| ---|
   |斯特里姆部署类型 |独立 | Striim 可以在**独立**部署类型或**群集**部署类型中运行。 独立模式将在单个虚拟机上部署 Striim 服务器，您可以根据数据量选择 VM 的大小。 群集模式将在具有所选大小的两个或多个 VM 上部署 Striim 服务器。 具有 2 个以上节点的群集环境提供自动高可用性和故障转移。</br></br> 在本教程中，您可以选择"独立"选项。 使用默认的"Standard_F4s"大小 VM。  | 
   | 斯特里姆群集的名称|    <Striim_cluster_Name>|  斯特里姆群集的名称。|
   | 斯特里姆群集密码|   <Striim_cluster_password>|  群集的密码。|

   填写表单后，选择 **"确定"** 以继续。

1. 在 **"Striim 访问设置"** 窗格中，配置**公共 IP 地址**（选择默认值 **）、Striim 的域名**、要用于登录 Striim UI 的**管理员密码**。 配置 VNET 和子网（选择默认值）。 填写详细信息后，选择 **"确定"** 以继续。

   ![斯特里姆访问设置](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure 将验证部署并确保一切看起来良好;验证需要几分钟才能完成。 验证完成后，选择 **"确定**"。
  
1. 最后，查看使用条款并选择 **"创建**"以创建 Striim 实例。 

## <a name="configure-the-source-database"></a>配置源数据库 

在本节中，您将 Oracle 数据库配置为数据移动的源。  您需要[Oracle JDBC 驱动程序](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)才能连接到 Oracle。 要从源 Oracle 数据库读取更改，可以使用[LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html)或[XStream API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)。 Oracle JDBC 驱动程序必须存在于 Striim 的 Java 类路径中，才能读取、写入或持久化来自 Oracle 数据库的数据。

将[ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)驱动程序下载到本地计算机上。 稍后将将其安装在 Striim 群集中。

## <a name="configure-the-target-database"></a>配置目标数据库

在本节中，您将将 Azure Cosmos DB SQL API 帐户配置为数据移动的目标。

1. 使用[Azure](create-cosmosdb-resources-portal.md)门户创建 Azure 宇宙 DB SQL API 帐户。

1. 导航到 Azure Cosmos 帐户中**的数据资源管理器**窗格。 选择 **"新容器"** 以创建新容器。 假设您正在迁移*产品*并*订购*数据从 Oracle 数据库到 Azure Cosmos DB。 使用名为 **"订单**"的容器创建名为**StriimDemo**的新数据库。 使用**1000 个 RA（** 此示例使用 1000 个 R，但应使用为工作负载估计的吞吐量）和 **/ORDER_ID**作为分区键。" 这些值将因源数据而异。 

   ![创建 SQL API 帐户](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>将 Oracle 配置为 Azure 宇宙数据库数据流

1. 现在，让我们回到斯特里姆。 在与 Striim 交互之前，请安装您之前下载的 Oracle JDBC 驱动程序。

1. 导航到在 Azure 门户中部署的 Striim 实例。 选择上菜单栏中的 **"连接**"按钮，然后从**SSH**选项卡中，**使用 VM 本地帐户**字段在登录中复制 URL。

   ![获取 SSH URL](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. 打开新的终端窗口并运行从 Azure 门户复制的 SSH 命令。 本文在 MacOS 中使用终端，您可以在 Windows 计算机上使用 PuTTY 或不同的 SSH 客户端遵循类似的说明。 当出现提示时，键入 **"是**"以继续并输入在上一步骤中为虚拟机设置的**密码**。

   ![连接到斯特里姆 VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. 现在，打开一个新的终端选项卡来复制您以前下载的**ojdbc8.jar**文件。 使用以下 SCP 命令将 jar 文件从本地计算机复制到在 Azure 中运行的 Striim 实例的 tmp 文件夹：

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![将 Jar 文件从位置计算机复制到斯特里姆](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. 接下来，导航回您执行 SSH 的窗口到 Striim 实例，然后作为 sudo 登录。 使用以下命令将**ojdbc8.jar**文件从 **/tmp**目录移动到 Striim 实例的**lib**目录中：

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![将 Jar 文件移动到 lib 文件夹](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. 从同一终端窗口，通过执行以下命令重新启动 Striim 服务器：

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. 斯特里姆需要一分钟才能启动。 如果要查看状态，运行以下命令： 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 现在，导航回 Azure 并复制 Striim VM 的公共 IP 地址。 

   ![复制斯特里姆 VM IP 地址](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. 要导航到 Striim 的 Web UI，请在浏览器中打开一个新选项卡，然后复制公共 IP，然后复制：9080。 使用**管理员**用户名以及您在 Azure 门户中指定的管理员密码登录。

   ![登录到斯特里姆](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. 现在，您将到达斯特里姆的主页。 有三个不同的窗格 -**仪表板**、**应用**和**源预览**。 "仪表板"窗格允许您实时移动数据并可视化数据。 "应用"窗格包含流数据管道或数据流。 页面右侧是 SourcePreview，您可以在其中预览数据，然后再移动数据。

1. 选择"**应用"** 窗格，我们将立即关注此窗格。 有多种示例应用可用于了解 Striim，但在本文中，您将创建自己的应用。 选择右上角的 **"添加应用"** 按钮。

   ![添加斯特里姆应用](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. 创建 Striim 应用程序的方法有很多种。 选择 **"从模板开始**"以从现有模板开始。

   ![使用模板启动应用](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. 在 **"搜索模板"** 字段中，键入"宇宙"并选择 **"目标：Azure 宇宙数据库**"，然后选择**Oracle CDC 到 Azure 宇宙数据库**。

   ![选择 Oracle CDC 到宇宙数据库](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. 在下一页中，为应用程序命名。 您可以提供一个名称，如**oraToCosmosDB，** 然后选择 **"保存**"。

1. 接下来，输入源 Oracle 实例的源配置。 输入**源名称**的值。 源名称只是 Striim 应用程序的命名约定，您可以使用**类似src_onPremOracle**之类的内容。 输入其余源参数**URL、****用户名**、**密码**的值，选择**LogMiner**作为读取器从 Oracle 读取数据。 选择“下一步”**** 继续。

   ![配置源参数](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. Striim 将检查您的环境，并确保它可以连接到源 Oracle 实例、具有正确的权限以及 CDC 配置正确。 验证所有值后，选择 **"下一步**"。

   ![验证源参数](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. 从要迁移的 Oracle 数据库中选择表。 例如，让我们选择"订单"表，然后选择 **"下一步**"。 

   ![选择源表](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. 选择源表后，可以执行更复杂的操作，如映射和筛选。 在这种情况下，您将只需在 Azure Cosmos DB 中创建源表的副本。 因此，选择 **"下一步**"以配置目标

1. 现在，让我们配置目标：

   * **目标名称**- 为目标提供友好名称。 
   * **输入来自**- 从下拉列表中，从源 Oracle 配置中创建的输入流中选择输入流。 
   * **集合**- 输入目标 Azure Cosmos DB 配置属性。 集合语法是**源架构.源表、目标数据库.目标容器**。 在此示例中，值将为"SYSTEM"。ORDERS，斯特里姆德莫.订单"。 
   * **访问密钥**- Azure 宇宙帐户的主密钥。
   * **服务终结点**– Azure Cosmos 帐户的 URI，可以在 Azure 门户的 **"密钥"** 部分下找到它们。 

   选择 **"保存"** 和 **"下一步**"。

   ![配置目标参数](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. 接下来，您将到达流设计器，您可以在其中拖放开盒装连接器以创建流式处理应用程序。 此时，您不会对流进行任何修改。 因此，请继续通过选择 **"部署应用"** 按钮来部署应用程序。
 
   ![部署应用](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. 在部署窗口中，可以指定是否要在部署拓扑的特定部分上运行应用程序的某些部分。 由于我们通过 Azure 在简单的部署拓扑中运行，因此我们将使用默认选项。

   ![使用默认选项](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. 部署后，可以预览流以查看数据流经。 选择**波浪**图标及其旁边的眼球。 选择顶部菜单栏中的 **"已部署"** 按钮，然后选择 **"开始应用**"。

   ![启动应用](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. 通过使用**CDC（更改数据捕获）** 读取器，Striim 将只获取数据库上的新更改。 如果数据流经源表，您将看到它。 但是，由于这是一个演示表，因此源未连接到任何应用程序。 如果使用示例数据生成器，则可以将事件链插入到 Oracle 数据库中。

1. 您将看到数据流经斯特里姆平台。 Striim 还选取了与表关联的所有元数据，这有助于监视数据并确保数据落在正确的目标上。

   ![配置 CDC 管道](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. 最后，让我们登录到 Azure 并导航到 Azure Cosmos 帐户。 刷新数据资源管理器，您可以看到数据已到达。  

   ![验证 Azure 中迁移的数据](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

通过使用 Azure 中的 Striim 解决方案，可以从 Oracle、Cassandra、MongoDB 等各种源持续将数据迁移到 Azure Cosmos DB 到 Azure Cosmos DB。 要了解更多信息，请访问[Striim 网站](https://www.striim.com/)，[下载 Striim 的 30 天免费试用](https://go2.striim.com/download-free-trial)版，以及与 Striim 一起设置迁移路径时的任何问题，请提交[支持请求。](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>后续步骤

* 如果要将数据迁移到 Azure Cosmos DB SQL API，请参阅[如何使用 Striim 将数据迁移到 Cassandra API 帐户](cosmosdb-cassandra-api-migrate-data-striim.md)

* [使用 Azure Cosmos DB 指标监视和调试数据](use-metrics.md)

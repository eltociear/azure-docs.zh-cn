---
title: 按 Azure 服务列出的资源提供程序
description: 列出 Azure 资源管理器的所有资源提供程序命名空间，并显示该命名空间的 Azure 服务。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 9cf18a1e4c0dd40ae9106d32547ba4795ea9fbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472956"
---
# <a name="resource-providers-for-azure-services"></a>Azure 服务的资源提供程序

本文介绍资源提供程序命名空间如何映射到 Azure 服务。

## <a name="match-resource-provider-to-service"></a>将资源提供程序匹配到服务

| 资源提供程序命名空间 | Azure 服务 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure 活动目录域服务](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure 顾问](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API 管理](../../api-management/index.yml) |
| Microsoft.AppConfiguration | core |
| 微软.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 证明服务 |
| Microsoft.Authorization | [Azure 资源管理器](../index.yml) |
| Microsoft.Automation | [自动化](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure 活动目录 B2C](../../active-directory-b2c/index.yml) |
| 微软.Azure数据 | SQL 服务器注册表 |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [批](../../batch/index.yml) |
| Microsoft.Billing | [成本管理和计费](/azure/billing/) |
| Microsoft.BingMaps | [必应地图](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure 区块链服务](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure 蓝图](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure 机器人服务](/azure/bot-service/) |
| Microsoft.Cache | [用于 Redis 的 Azure 缓存](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [内容交付网络](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [应用服务证书](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| 微软.更改分析 | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 经典部署模型虚拟机 |
| Microsoft.ClassicInfrastructureMigrate | 经典部署模型迁移 |
| Microsoft.ClassicNetwork | 经典部署模型虚拟网络 |
| Microsoft.ClassicStorage | 经典部署模型存储 |
| Microsoft.ClassicSubscription | 经典部署模型 |
| Microsoft.CognitiveServices | [认知服务](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [虚拟机](/azure/virtual-machines/)<br />[虚拟机规模集](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [成本管理](/azure/cost-management/) |
| Microsoft.ContainerInstance | [容器实例](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [容器注册表](/azure/container-registry/) |
| Microsoft.ContainerService | [Azure 库伯奈斯服务 （AKS）](/azure/aks/) |
| Microsoft.CostManagement | [成本管理](/azure/cost-management/) |
| 微软.成本管理出口 | [成本管理](/azure/cost-management/) |
| 微软.客户锁定箱 | 适用于 Microsoft Azure 的客户密码箱 |
| 微软.自定义提供程序 | [Azure Custom Providers](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure 数据块](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [数据目录](/azure/data-catalog/) |
| Microsoft.DataFactory | [数据工厂](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [数据湖分析](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure 数据库迁移服务](/azure/dms/) |
| 微软.数据共享 | [Azure 数据共享](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [MySQL 的 Azure 数据库](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| 微软.桌面虚拟化 | [Windows 虚拟桌面](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Azure 部署管理器](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Azure IoT 中心](/azure/iot-hub/)<br />[Azure IoT 中心设备预配服务](/azure/iot-dps/) |
| 微软.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure 实验室服务](../../lab-services/index.yml) |
| 微软.数字双胞胎 | [Azure 数字孪生](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [应用服务](/azure/app-service/) |
| 微软.企业知识图 | 企业知识图 |
| Microsoft.EventGrid | [事件网格](/azure/event-grid/) |
| Microsoft.EventHub | [事件中心](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure 资源管理器](../index.yml) |
| Microsoft.GuestConfiguration | [Azure 策略](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure 大型实例上的 SAP HANA](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 专用 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| 微软.医疗保健Apis | [适用于 FHIR 的 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [斯托简单](/azure/storsimple/) |
| Microsoft.ImportExport | [Azure 导入/导出](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure 数字孪生](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [密钥保管库](../../key-vault/index.yml) |
| 微软.库伯内斯 | [Azure 库伯奈斯服务 （AKS）](/azure/aks/) |
| Microsoft.Kusto | [Azure 数据资源管理器](../../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure 实验室服务](../../lab-services/index.yml) |
| Microsoft.Logic | [逻辑应用](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [机器学习工作室](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure 机器学习](../../machine-learning/index.yml) |
| 微软.维护 | [Azure 维护](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [管理组](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure 地图](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [媒体服务](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure 迁移](../../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Azure 空间定位点](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp 文件](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [应用程序网关](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Azure DDoS 保护](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure 防火墙](../../firewall/index.yml)<br />[Azure Front Door 服务](../../frontdoor/index.yml)<br />[Azure 专用链接](../../private-link/index.yml)<br />[负载平衡器](../../load-balancer/index.yml)<br />[网络观察程序](../../network-watcher/index.yml)<br />[流量管理器](../../traffic-manager/index.yml)<br />[虚拟网络](../../virtual-network/index.yml)<br />[虚拟 WAN](../../virtual-wan/index.yml)<br />[VPN 网关](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [通知中心](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure 迁移](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| 微软.对等 | [Azure 对等服务](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure 策略](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure 门户](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| 微软.红帽开放移位 | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure 中继](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure 资源图](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure 服务运行状况](../../service-health/index.yml)|
| Microsoft.Resources | [Azure 资源管理器](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [计划程序](/azure/scheduler/) |
| Microsoft.Search | [Azure 认知搜索](../../search/index.yml) |
| Microsoft.Security | [安全中心](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure 哨兵](/azure/sentinel/) |
| 微软.串行控制台 | [适用于 Windows 的 Azure 串行控制台](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [服务总线](/azure/service-bus/) |
| Microsoft.ServiceFabric | [服务结构](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric 网格](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure SignalR 服务](../../azure-signalr/index.yml) |
| 微软.软件计划 | 许可 |
| Microsoft.Solutions | [Azure 托管应用程序](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL 数据库](../../sql-database/index.yml)<br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure 虚拟机上的 SQL 服务器](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [存储](../../storage/index.yml) |
| 微软.存储缓存 | [Azure HPC 缓存](/azure/hpc-cache/) |
| Microsoft.StorageSync | [存储](../../storage/index.yml) |
| Microsoft.StorSimple | [斯托简单](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Azure 流分析](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| 微软.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure 时间序列见解](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure 映像生成器](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| 微软.VMware云简单 | [Azure VMware Solution by CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [应用服务](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [视窗 10 IoT 核心服务](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>后续步骤

有关资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](resource-providers-and-types.md)

---
title: 使用 Azure Site Recovery 排查将 VMware VM 和物理服务器灾难恢复到 Azure 时的复制问题 | Microsoft Docs
description: 本文针对使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 期间遇到的常见复制问题提供故障排除信息。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423962"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>解决 VMware VM 和物理服务器的复制问题

本文介绍在使用 [Site Recovery](site-recovery-overview.md) 将本地 VMware VM 和物理服务器复制到 Azure 时可能遇到的一些常见问题和具体错误。

## <a name="step-1-monitor-process-server-health"></a>第 1 步：监视进程服务器运行状况

Site Recovery 使用[进程服务器](vmware-physical-azure-config-process-server-overview.md#process-server)接收和优化复制的数据，并将其发送到 Azure。

我们建议在门户中监视进程服务器的运行状况，以确保它们已连接并正常运行，且正在对进程服务器关联的源计算机进行复制。

- [了解](vmware-physical-azure-monitor-process-server.md)如何监视进程服务器。
- [了解最佳实践](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [排查](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)进程服务器运行状况问题。

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>第 2 步：解决连接和复制问题

初始和进行中的复制失败往往是源服务器与进程服务器或者进程服务器与 Azure 之间的连接问题造成的。 

若要解决这些问题，请[排查连接和复制问题](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)。




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>第 3 步：对不可用复制的源计算机进行故障排除

尝试选择源计算机来通过 Site Recovery 启用复制时，计算机可能由于以下原因之一而不可用：

* **具有相同实例 UUID 的两个虚拟机**：如果 vCenter 下的两个虚拟机具有相同的实例 UUID，则配置服务器发现的第一个虚拟机将显示在 Azure 门户中。 若要解决此问题，请确保没有两个虚拟机具有相同的实例 UUID。 如果备份 VM 处于活动状态，并且已记录到发现记录，则这种情况会很常见。 请参阅[Azure 站点恢复 VMware 到 Azure：如何清理重复或陈旧的条目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)以进行解析。
* **vCenter 用户凭据不正确**：请确保使用 OVF 模板或统一设置设置配置服务器时添加了正确的 vCenter 凭据。 若要验证安装期间添加的凭据是否正确，请参阅[修改用于自动发现的凭据](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 权限不足**：如果为访问 vCenter 提供的权限没有所需的权限，则可能会出现无法发现虚拟机的情况。 确保将[为自动发现准备帐户](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的权限添加到 vCenter 用户帐户。
* **Azure 站点恢复管理服务器**：如果虚拟机在以下一个或多个角色下用作管理服务器 - 配置服务器/横向扩展进程服务器/主目标服务器，则您将无法从门户中选择虚拟机。 无法复制管理服务器。
* **已通过 Azure 站点恢复服务保护/故障：** 如果虚拟机已通过站点恢复受到保护或故障，则虚拟机无法选择在门户中进行保护。 确保要在门户中查找的虚拟机尚未由其他任何用户进行保护，或者位于不同的订阅下。
* **vCenter 未连接**：检查 vCenter 是否处于连接状态。 若要验证，请转到“恢复服务保管库”>“Site Recovery 基础结构”>“配置服务器”，单击相应的配置服务器，此时，右侧会打开一个边栏选项卡，其中显示了关联服务器的详细信息。 检查 vCenter 是否已连接。 如果处于“未连接”状态，请解决问题，并在门户中[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **ESXi 已关闭**电源：如果虚拟机所在的 ESXi 主机处于关闭状态，则虚拟机将不会列出或在 Azure 门户上处于不可选择状态。 打开 ESXi 主机，并在门户中[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **挂起重新启动**：如果虚拟机上存在挂起的重新启动，则将无法在 Azure 门户上选择该计算机。 请务必完成等待中的重新启动活动，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **未找到 IP：** 如果虚拟机没有与其关联的有效 IP 地址，则将无法在 Azure 门户中选择该计算机。 请务必将有效的 IP 地址分配到虚拟机，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>排查在门户中灰显的受保护虚拟机的问题

如果系统中存在重复的条目，则在 Site Recovery 下复制的虚拟机将不会显示在 Azure 门户中。 要了解如何删除陈旧条目并解决问题，请参阅[Azure 站点恢复 VMware 到 Azure：如何清理重复项或过时条目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)。

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>在过去"XXX"分钟内没有 VM 可用的崩溃一致恢复点

下面列出了其中的一些最常见

### <a name="initial-replication-issues-error-78169"></a>初始复制问题 [错误 78169]

反复确认不存在连接、带宽或时间同步相关的问题后，请确保：

- 没有任何防病毒软件正在阻止 Azure Site Recovery。 [了解详细](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) Azure Site Recovery 要求排除的文件夹。

### <a name="source-machines-with-high-churn-error-78188"></a>源计算机的变动率较高 [错误 78188]

可能的原因：
- 虚拟机的所列磁盘中的数据更改率（每秒写入字节数）超过了复制目标存储帐户类型的 [Azure Site Recovery 支持限制](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)。
- 有大量的数据等待上传，使变动率出现突发性的高峰。

若要解决问题，请执行以下操作：
- 确保根据源中的变动率要求预配目标存储帐户类型（“标准”或“高级”）。
- 如果已复制到高级托管磁盘（asrseeddisk 类型），请确保磁盘大小支持根据 Site Recovery 限制观察到的变动率。 如果需要，可以增加 asrseeddisk 的大小。 请按照以下步骤操作：
    - 导航到受影响的复制计算机的“磁盘”边栏选项卡，并复制副本磁盘名称
    - 导航到此副本托管磁盘
    - 你可能会在“概述”边栏选项卡上看到一个横幅，指出已生成 SAS URL。 单击此横幅并取消导出。 如果看不到横幅，请忽略此步骤。
    - 撤销 SAS URL 后，请转至托管磁盘的“配置”边栏选项卡并增加大小，以便 ASR 支持源磁盘上观察到的变动率
- 如果观测到的变动率是暂时性的，请等待几个小时，让等待中的数据跟上上传进度并创建恢复点。
- 如果磁盘包含非关键数据（如临时日志、测试数据等），请考虑将此数据移到其他位置，或者从复制中完全排除此磁盘
- 如果问题持续出现，请使用 Site Recovery [部署规划器](site-recovery-deployment-planner.md#overview)来帮助规划复制。

### <a name="source-machines-with-no-heartbeat-error-78174"></a>源计算机无检测信号 [错误 78174]

如果源计算机上的 Azure Site Recovery 移动代理与配置服务器 (CS) 不通信，则会发生此错误。

若要解决此问题，请使用以下步骤来验证源 VM 与配置服务器之间的网络连接：

1. 确认源计算机正在运行。
2. 使用拥有管理员特权的帐户登录到源计算机。
3. 确认以下服务正在运行，如果未运行，请重启以下服务：
   - Svagents (InMage Scout VX Agent)
   - InMage Scout 应用程序服务
4. 在源计算机上，检查位于以下位置的日志以查看错误详细信息：

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>进程服务器无检测信号 [错误 806]
如果进程服务器 (PS) 未发出检测信号，请检查：
1. PS VM 已启动并正在运行
2. 检查 PS 上的以下日志以查看错误详细信息：

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>主目标服务器无检测信号 [错误 78022]

如果主目标上的 Azure Site Recovery 移动代理与配置服务器不通信，则会发生此错误。

若要解决此问题，请使用以下步骤验证服务状态：

1. 确认主目标 VM 正在运行。
2. 使用拥有管理员特权的帐户登录到主目标 VM。
    - 确认 svagents 服务正在运行。 如果它正在运行，请重启服务
    - 检查位于以下位置的日志以查看错误详细信息：
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. 要向配置服务器注册主目标，请导航到文件夹 **%PROGRAMDATA%\ASR\代理**，并在命令提示符上运行以下内容：
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>错误 ID 78144 - 在过去“XXX”分钟内没有可供 VM 使用的应用一致性恢复点

移动代理[9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39)版本进行了增强，以处理 VSS 安装失败行为。 确保您处于最新版本上，以了解有关 VSS 故障故障排除的最佳指南。

下面列出了其中的一些最常见

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>原因 1：SQL 服务器 2008/2008 R2 中的已知问题 
**如何修复**：SQL 服务器 2008/2008 R2 存在已知问题。 请参阅此知识库文章：[托管 SQL Server 2008 R2 的服务器的 Azure Site Recovery 代理或其他非组件 VSS 备份失败](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>原因 2：Azure 站点恢复作业在承载具有AUTO_CLOSE GB 的任何版本的 SQL Server 实例的服务器上失败 
**如何修复**：请参阅 Kb[文章](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>原因 3：SQL Server 2016 和 2017 中的已知问题
**如何修复**：请参阅 Kb[文章](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>更多 VSS 相关问题原因：

若要进一步排查问题，请查看源计算机上的文件，获取故障的具体错误代码：
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

如何在文件中查找错误？
在编辑器中打开 vacp.log 文件，搜索字符串“vacpError”
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

在上面的示例中，**2147754994** 是介绍故障情况的错误代码，如下所示

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 编写器未安装 - 错误 2147221164 

*如何修复*：要生成应用程序一致性标记，Azure 站点恢复使用 Microsoft 卷卷卷影副本服务 （VSS）。 它安装适用于其操作的 VSS 提供程序，以便拍摄应用一致性快照。 此 VSS 提供程序作为服务安装。 如果 VSS 提供程序服务未安装，则应用程序一致性快照创建会失败，并出现 ID 为 0x80040154 的错误“类未注册”。 </br>
请参阅[有关 VSS 编写器安装故障排除的文章](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 编写器已禁用 - 错误 2147943458

**如何修复**：要生成应用程序一致性标记，Azure 站点恢复使用 Microsoft 卷卷卷影副本服务 （VSS）。 它安装适用于其操作的 VSS 提供程序，以便拍摄应用一致性快照。 此 VSS 提供程序作为服务安装。 如果 VSS 提供程序服务已禁用，则应用程序一致性快照创建会失败，并出现 错误“指定的服务已禁用，无法启动(0x80070422)”。 </br>

- 如果已禁用 VSS：
    - 确认 VSS 提供程序服务的启动类型是否设置为“自动”。****
    - 重启以下服务：
        - VSS 服务
        - Azure Site Recovery VSS 提供程序
        - VDS 服务

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供程序未注册 - 错误 2147754756

**如何修复**：要生成应用程序一致性标记，Azure 站点恢复使用 Microsoft 卷卷卷影副本服务 （VSS）。 检查 Azure Site Recovery VSS 提供程序服务是否已安装。 </br>

- 使用以下命令重试提供程序安装：
- 卸载现有提供程序：C：\程序文件 （x86）\微软 Azure 站点恢复\代理\InMageVSSProvider_Uninstall.cmd
- 重新安装：C：\程序文件 （x86）\微软 Azure 站点恢复\代理\InMageVSSProvider_Install.cmd
 
确认 VSS 提供程序服务的启动类型是否设置为“自动”。****
    - 重启以下服务：
        - VSS 服务
        - Azure Site Recovery VSS 提供程序
        - VDS 服务

## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 我们的社区非常活跃，将有一位工程师为你提供帮助。

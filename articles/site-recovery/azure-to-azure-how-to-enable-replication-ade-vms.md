---
title: 在 Azure 站点恢复中为加密的 Azure VM 启用复制
description: 本文介绍如何使用 Site Recovery 对启用了 Azure 磁盘加密的 VM 配置从一个 Azure 区域到另一个区域的 Azure VM 复制。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 3a59f137240eff2a3a68fa5547be8c6c25d3e5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772221"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>将启用了 Azure 磁盘加密的虚拟机复制到另一个 Azure 区域

本文介绍如何在启用 Azure 磁盘加密 （ADE） 后，将 Azure VM 从一个 Azure 区域复制到另一个 Azure 区域。

>[!NOTE]
> 站点恢复当前支持 ADE，并且没有 Azure 活动目录 （AAD）， 用于运行 Windows 和 Linux 操作系统的 VM。  对于运行 ADE 1.1（没有 AAD）的计算机，VM 必须使用托管磁盘。 不支持具有非托管磁盘的 VM。 如果从 ADE 0.1（使用 AAD）切换到 1.1 ，则需要在启用 1.1 后禁用复制并启用 VM 的复制。


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>必需的用户权限
Site Recovery 要求用户具有在目标区域中创建密钥保管库以及将源区域密钥保管库中的密钥复制到目标区域密钥保管库的权限。

若要从 Azure 门户为支持磁盘加密的 VM 启用复制，用户需要对**源区域和目标区域**密钥保管库具有以下权限。

- 密钥保管库权限
    - 列出、创建和获取
    
- 密钥保管库机密权限
    - 机密管理操作
        - 获取、列出和设置
    
- Key Vault 密钥权限（只有当 VM 使用“密钥加密密钥”来加密磁盘加密密钥时才需要）
    - 密钥管理操作
        - 获取、列出和创建
    - 加密操作
        - 解密和加密

若要管理权限，请在门户中转到 Key Vault 资源。 添加用户所需的权限。 以下示例演示如何启用对源区域中 Key Vault *ContosoWeb2Keyvault* 的权限。

1. 转到**主页** > **密钥库** > **ContosoWeb2KeyVault >访问策略**。

   ![“Key Vault 权限”窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 可以发现目前没有任何用户权限。 选择“添加新订阅”****。 输入用户和权限信息。

   ![keyvault 权限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果启用灾难恢复 (DR) 的用户无权复制密钥，则拥有相应权限的安全管理员可使用以下脚本将加密机密和密钥复制到目标区域。

若要排查权限问题，请参阅本文稍后所述的 [Key Vault 权限问题](#trusted-root-certificates-error-code-151066)。

>[!NOTE]
>若要通过门户对启用了磁盘加密的 VM 启用复制，至少需要对 Key Vault、机密和密钥拥有“列出”权限。

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>使用 PowerShell 脚本将磁盘加密密钥复制到 DR 区域

1. [打开“CopyKeys”原始脚本代码](https://aka.ms/ade-asr-copy-keys-code)。
2. 将脚本复制到文件，并将其命名为**Copy-keys.ps1**。
3. 打开 Windows PowerShell 应用程序，并转到该文件所保存到的文件夹。
4. 执行 Copy-keys.ps1。
5. 提供用于登录的 Azure 凭据。
6. 选择你的 VM 的 **Azure 订阅**。
7. 等待加载资源组，然后选择 VM**的资源组**。
8. 从显示的列表中选择 VM。 该列表只显示启用了磁盘加密的 VM。
9. 选择**目标位置**。

    - **磁盘加密密钥保管库**
    - **密钥加密密钥保管库**

   默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 保管库的名称包含基于源 VM 磁盘加密密钥的“asr”后缀。 如果已存在 Site Recovery 创建的 Key Vault，则会重复使用它。 根据需要从列表中选择不同的 Key Vault。

## <a name="enable-replication"></a>启用复制

在此示例中，主要 Azure 区域是东亚，辅助区域是东南亚。

1. 在保管库中选择“+复制”。****
2. 注意以下字段。
    - **源**：VM 的起始点，在本例中为 **Azure**。
    - **源位置**：要保护虚拟机的 Azure 区域。 在此示例中，源位置为"东亚"。
    - **部署模型**：源计算机的 Azure 部署模型。
    - **源订阅**：源虚拟机所属的订阅。 它可以是恢复服务保管库所在的同一 Azure Active Directory 租户中的任一订阅。
    - **资源组**：源虚拟机所属的资源组。 所选资源组中要保护的所有 VM 会在下一步骤中列出。

3. 在**虚拟机** > **中选择虚拟机**，选择要复制的每个 VM。 只能选择可以启用复制的计算机。 然后，选择 **"确定**"。

4. 在“设置”中，可以配置以下目标站点设置。****

    - **目标位置**：将复制源虚拟机数据的位置。 Site Recovery 根据所选计算机的位置提供合适的目标区域列表。 我们建议使用与恢复服务保管库位置相同的位置。
    - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅与源订阅相同。
    - **目标资源组**：所有复制虚拟机所属的资源组。 默认情况下，Site Recovery 会在目标区域中创建一个新的资源组， 其名称带有“asr”后缀。 如果已存在 Azure Site Recovery 创建的资源组，将会重复使用它。 此外，可按以下部分所述，选择对资源组进行自定义。 目标资源组的位置可以是除托管源虚拟机区域以外的任何 Azure 区域。
    - **目标虚拟网络**：默认情况下，站点恢复在目标区域中创建新的虚拟网络。 其名称带有“asr”后缀。 此虚拟网络会映射到源网络并用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户（如果源 VM 不使用托管磁盘）：** 默认情况下，站点恢复通过模拟源 VM 存储配置创建新的目标存储帐户。 如果已存在一个存储帐户，将重复使用它。
    - **复制托管磁盘（如果源 VM 使用托管磁盘）：** 站点恢复在目标区域中创建新的复制管理磁盘，以镜像源 VM 的托管磁盘，这些磁盘的存储类型（标准或高级）与源 VM 的托管磁盘相同。
    - **缓存存储帐户**：站点恢复需要在源区域中称为*缓存存储*的额外存储帐户。 源 VM 上的所有更改将受到跟踪并发送到缓存存储帐户。 它们随后会复制到目标位置。
    - **可用性集**：默认情况下，站点恢复会在目标区域中创建新的可用性集。 其名称带有“asr”后缀。 如果已存在 Site Recovery 创建的可用性集，将会重复使用它。
    - **磁盘加密密钥保管库**：默认情况下，站点恢复会在目标区域中创建新的密钥保管库。 其名称包含基于源 VM 磁盘加密密钥的“asr”后缀。 如果已存在 Azure Site Recovery 创建的 Key Vault，将会重复使用它。
    - **密钥加密密钥保管库**：默认情况下，站点恢复会在目标区域中创建新的密钥保管库。 其名称包含基于源 VM 密钥加密密钥的“asr”后缀。 如果已存在 Azure Site Recovery 创建的 Key Vault，将会重复使用它。
    - **复制策略**：定义恢复点保留历史记录和应用一致的快照频率的设置。 默认情况下，站点恢复会创建一个新的复制策略，默认设置为*24 小时*，用于恢复点保留 *，60 分钟*用于应用一致的快照频率。

## <a name="customize-target-resources"></a>自定义目标资源

遵循以下步骤修改 Site Recovery 默认目标设置。

1. 选择“目标订阅”旁边的“自定义”以修改默认目标订阅****。 从 Azure AD 租户中可用的订阅列表中选择订阅。

2. 选择“资源组、网络、存储和可用性集”旁边的“自定义”，以修改以下默认设置****：
    - 对于“目标资源组”，请从订阅目标位置中的资源组列表中选择资源组。****
    - 对于“目标虚拟网络”，请从目标位置中的虚拟网络列表中选择网络。****
    - 对于“可用性集”，可将可用性集设置添加到 VM（如果它们是源区域中可用性集的一部分）。****
    - 对于“目标存储帐户”，请选择要使用的帐户。****

2. 选择“加密设置”旁边的“自定义”，以修改以下默认设置****：
   - 对于“目标磁盘加密 Key Vault”，请从订阅的目标位置中的 Key Vault 列表中选择目标磁盘加密 Key Vault****。
   - 对于“目标加密加密 Key Vault”，请从订阅的目标位置中的 Key Vault 列表中选择目标密钥加密 Key Vault****。

3. 选择 **"创建目标资源** > **启用复制**"。
4. 为 VM 启用复制后，可以在“复制的项”下检查 VM 的运行状况****。

>[!NOTE]
>在初始复制期间，VM 状态刷新可能需要一段时间，但不显示确切的进度。 单击 **"刷新**"获取最新状态。

## <a name="update-target-vm-encryption-settings"></a>更新目标 VM 加密设置
在以下情况下，需要更新目标 VM 的加密设置：
  - 你已在 VM 上启用 Site Recovery 复制。 后来，你在源 VM 上启用了磁盘加密。
  - 你已在 VM 上启用 Site Recovery 复制。 后来，你在源 VM 上更改了磁盘加密密钥或密钥加密密钥。

可以使用[一个脚本](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)将加密密钥复制到目标区域，然后在“恢复服务保管库” > “复制的项” > “属性” > “计算和网络”中更新目标加密设置**************。

![“更新 ADE 设置”对话框窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>在 Azure 到 Azure VM 复制期间解决密钥保管库权限问题

Azure Site Recovery 至少需要源区域密钥保管库的读取权限和目标区域密钥保管库的写入权限，才能读取机密并将其复制到目标区域密钥保管库。 

**原因 1：** 源**区域"密钥库**"上没有读取密钥的"GET"权限。 </br>
**如何修复：** 无论您是订阅管理员，都必须获得密钥保管库的权限。

1. 转到源区域密钥保管库，本例中为“ContososourceKeyvault”>“访问策略”**** 
2. 在“选择主体”**** 下添加你的用户名，例如：“dradmin@contoso.com”
3. 在“密钥权限”**** 下，选择 GET 
4. 在“机密权限”**** 下，选择 GET 
5. 保存访问策略

**原因 2：** 您不需要**对目标区域密钥保管库**进行写入密钥的权限。 </br>

*例如*：您尝试复制在源区域上具有密钥保管库*ContososourceKeyvault*的 VM。
你对源区域中的 Key Vault 拥有所有权限。 但在保护期间，你选择了已创建的、但没有权限的 Key Vault ContosotargetKeyvault。 发生错误。

[目标密钥保管库](#required-user-permissions)所需的权限

**如何修复：** 转到**主页** > **密钥保管库** > **ContosotargetKeyvault** > **访问策略**并添加相应的权限。

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。

---
title: 使用 PowerShell 还原 Azure 文件
description: 在本文中，了解如何使用 Azure 备份服务和 PowerShell 还原 Azure 文件。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086829"
---
# <a name="restore-azure-files-with-powershell"></a>使用 PowerShell 还原 Azure 文件

本文介绍如何从 Azure[备份](backup-overview.md)服务使用 Azure Powershell 创建的还原点还原整个文件共享或特定文件。

可以还原整个文件共享，或共享中的特定文件。 可以还原到原始位置或备用位置。

> [!WARNING]
> 确保 PS 版本升级到 AFS 备份的"Az.恢复服务 2.6.0"的最低版本。 有关详细信息，请参阅概述此更改要求[的部分](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups)。

## <a name="fetch-recovery-points"></a>提取恢复点

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) 列出已备份项的所有恢复点。

在以下脚本中：

* 变量 **$rp** 是一个数组，其中包含所选备份项在过去七天的恢复点。
* 数组按相反时间顺序排序，索引**0**处的最新恢复点。
* 使用标准 PowerShell 数组索引选取恢复点。
* 在此示例中 **，$rp{0}** 选择最新的恢复点。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

输出如下所示。

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

选择相关的恢复点之后，将文件共享或文件还原到原始位置或备用位置。

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>将 Azure 文件共享还原到备用位置

使用 [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 还原到所选的恢复点。 指定以下参数来标识备用位置：

* **目标存储帐户名称**：还原备份内容的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **TargetFileShareName**：文件共享在目标存储帐户中还原备份的内容。
* **目标文件夹**：还原数据的文件共享下的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **解决冲突**：如果与还原的数据发生冲突，则说明。 接受“覆盖”或“跳过”********。

结合如下所示的参数运行 cmdlet：

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

该命令返回一个要跟踪的、带有 ID 的作业，如以下示例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>将 Azure 文件还原到备用位置

使用 [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 还原到所选的恢复点。 指定以下参数来标识备用位置，以及唯一标识要还原的文件。

* **目标存储帐户名称**：还原备份内容的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **TargetFileShareName**：文件共享在目标存储帐户中还原备份的内容。
* **目标文件夹**：还原数据的文件共享下的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **SourceFilePath**：文件的绝对路径，将在文件共享中作为字符串还原。 此路径与 Get-AzStorageFile PowerShell cmdlet 中使用的路径相同****。
* **源文件类型**：是否选择了目录或文件。 接受“目录”或“文件”********。
* **解决冲突**：如果与还原的数据发生冲突，则说明。 接受“覆盖”或“跳过”********。

其他参数（SourceFilePath 和 SourceFileType）只与要还原的单个文件相关。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

该命令返回一个要跟踪的、带有 ID 的作业，如前一部分中所示。

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>将 Azure 文件共享和文件还原到原始位置

在还原到原始位置时，无需指定目的地和目标相关的参数。 仅“ResolveConflict”必须提供****。

#### <a name="overwrite-an-azure-file-share"></a>覆盖 Azure 文件共享

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>覆盖 Azure 文件

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>后续步骤

[了解如何](restore-afs.md)在 Azure 门户中还原 Azure 文件。

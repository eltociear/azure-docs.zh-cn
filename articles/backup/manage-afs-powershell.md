---
title: 使用 PowerShell 管理 Azure 文件共享备份
description: 了解如何使用 PowerShell 管理和监视由 Azure 备份服务备份的 Azure 文件共享。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083177"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>使用 PowerShell 管理 Azure 文件共享备份

本文介绍如何使用 Azure PowerShell 管理和监视由 Azure 备份服务备份的 Azure 文件共享。

> [!WARNING]
> 确保 PS 版本升级到 AFS 备份的"Az.恢复服务 2.6.0"的最低版本。 有关详细信息，请参阅概述此更改要求[的部分](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups)。

## <a name="modify-the-protection-policy"></a>修改保护策略

若要更改用于备份 Azure 文件共享的策略，请使用 [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)。 指定相关的备份项和新的备份策略。

以下示例将“testAzureFS”保护策略从“dailyafs”更改为“monthlyafs”************。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>跟踪备份和还原作业

按需备份和还原操作返回作业以及 ID，如[运行按需备份](backup-azure-afs-automation.md#trigger-an-on-demand-backup)时所示。 使用 [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet 跟踪作业的进度和详细信息。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>停止对文件共享的保护

可以通过两种方法来停止保护 Azure 文件共享：

* 停止所有将来的备份作业并*删除*所有恢复点
* 停止所有将来的备份作业，但*离开*恢复点

由于 Azure 备份创建的基础快照将保留，因此将存储中存储恢复点可能存在成本。 但是，离开恢复点的好处是，如果需要，您可以稍后还原文件共享。 有关离开恢复点的成本的信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/storage/files/)。 如果选择删除所有恢复点，则无法还原文件共享。

## <a name="stop-protection-and-retain-recovery-points"></a>停止保护并保留恢复点

要在保留数据时停止保护，请使用[禁用-AzRecovery 服务备份保护](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)cmdlet。

以下示例停止对*afsfileshare*文件共享的保护，但保留所有恢复点：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

输出中的作业 ID 属性对应于备份服务为"停止保护"操作创建的作业的作业 ID。 要跟踪作业的状态，请使用[获取-AzRecovery 服务备份作业](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)cmdlet。

## <a name="stop-protection-without-retaining-recovery-points"></a>停止保护而不保留恢复点

要停止保护而不保留恢复点，请使用[禁用-AzRecoveryServices 备份保护](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)cmdlet 并添加 **-删除恢复点**参数。

以下示例停止保护*afsfileshare*文件共享而不保留恢复点：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>后续步骤

[了解如何](manage-afs-backup.md)在 Azure 门户中管理 Azure 文件共享备份。

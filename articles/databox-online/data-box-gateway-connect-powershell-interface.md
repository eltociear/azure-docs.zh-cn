---
title: 使用 Windows PowerShell 连接到和管理 Azure 数据盒网关设备
description: 描述如何通过 Windows PowerShell 接口连接到数据框网关并对其进行管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260210"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>通过 Windows PowerShell 管理 Azure 数据盒网关设备

Azure 数据盒网关解决方案允许您通过网络将数据发送到 Azure。 本文介绍了数据盒网关设备的一些配置和管理任务。 您可以使用 Azure 门户、本地 Web UI 或 Windows PowerShell 界面来管理设备。

本文重点介绍使用 PowerShell 接口执行的任务。

本文包括以下过程：

- 连接到 PowerShell 接口
- 创建支持包
- 上传证书
- 在非 DHCP 环境中启动
- 查看设备信息

## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>上传证书

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>在非 DHCP 环境中启动

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>查看设备信息

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中部署 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。

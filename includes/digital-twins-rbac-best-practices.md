---
title: include 文件
description: include 文件
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044911"
---
基于角色的访问控制是一种继承驱动的安全策略，用于管理访问权限、权限和角色。 子代角色从父角色继承权限。 也可以在不从父角色继承的情况下分配权限。 还可以指定权限以根据需要自定义角色。

例如，空间管理员可能需要全局访问权限，才能运行指定空间的所有操作。 访问涉及空间下面或空间内的所有节点。 设备安装程序可能只需要设备和传感器的*读取*和*更新*权限。

根据最小特权原则，在任何情况下向角色授予的访问权限都应*准确且不超出其完成任务所需的访问权限*。 根据此原则，只向某个身份授予**：

* 完成其作业所需的访问权限。
* 合适的角色，且仅限于执行其作业。

>[!IMPORTANT]
> 请始终遵循最低权限原则。

需要遵循的其他两个重要的基于角色的访问控制做法：

> [!div class="checklist"]
> * 定期审核角色分配，以验证每个角色均拥有正确的权限。
> * 当个人更改角色或分配时，应清除原有角色和分配。
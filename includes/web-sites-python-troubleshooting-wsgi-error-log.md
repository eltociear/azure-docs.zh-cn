---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173554"
---
如果 Python 在启动应用程序时遇到错误，则仅返回一个简单的错误页（例如，"由于发生了内部服务器错误而无法显示该页"）。

捕获 Python 应用程序错误：

1. 在 Azure 门户中，在你的 Web 应用中选择“设置”****。
2. 在“设置”**** 选项卡上，选择“应用程序设置”****。
3. 在“应用设置”**** 下，输入以下键/值对：
    * 键：WSGI_LOG
    * 值：D:\home\site\wwwroot\logs.txt（输入所选文件名）

你现在应在 wwwroot 文件夹中的 logs.txt 文件中看到错误。

---
title: 适用于 Azure Functions 的 Azure IoT 中心绑定
description: 了解如何在 Azure 函数中使用 IoT 中心触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586117"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure IoT 中心绑定

这组文章介绍了如何使用 IoT 中心的 Azure 函数绑定。 IoT 中心支持基于 [Azure 事件中心绑定](functions-bindings-event-hubs.md)。

> [!IMPORTANT]
> 虽然下述代码示例使用事件中心 API，但给定的语法适用于 IoT 中心函数。

| 操作 | 类型 |
|--------|------|
| 响应发送到 IoT 中心事件流的事件。 | [触发](./functions-bindings-event-iot-trigger.md) |
| 将事件写入 IoT 事件流 | [输出绑定](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>后续步骤

- [响应发送到事件中心事件流的事件（触发器）](./functions-bindings-event-iot-trigger.md)
- [将事件写入事件流（输出绑定）](./functions-bindings-event-iot-output.md)

---
title: 诊断图形性能问题 远程桌面 - Azure
description: 本文介绍如何在远程桌面协议会话中使用 RemoteFX 图形计数器来诊断 Windows 虚拟桌面中图形的性能问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127552"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>诊断远程桌面中的图形性能问题

为了诊断远程会话的体验质量问题，已在性能监视器的"远程FX图形"部分下提供了计数器。 本文可帮助您在这些计数器远程桌面协议 （RDP） 会话期间查明和修复与图形相关的性能瓶颈。

## <a name="find-your-remote-session-name"></a>查找远程会话名称

您需要远程会话名称来标识图形性能计数器。 按照本节中的说明来标识每个计数器的实例。

1. 从远程会话打开 Windows 命令提示。
2. 运行**qwinsta**命令并找到会话名称。
    - 如果您的会话托管在多会话虚拟机 （VM） 中：每个计数器的实例后缀为与缀会话名称相同的数字，如"rdp-tcp 37"。
    - 如果您的会话托管在支持虚拟图形处理单元 （vGPU） 的 VM 中：每个计数器的实例存储在服务器上，而不是存储在 VM 中。 您的计数器实例包括 VM 名称，而不是会话名称中的数字，例如"Win8 企业 VM"。

>[!NOTE]
> 虽然计数器的名称中有 RemoteFX，但它们在 vGPU 方案中也包含远程桌面图形。

## <a name="access-performance-counters"></a>访问性能计数器

确定远程会话名称后，请按照这些说明收集远程会话的 RemoteFX 图形性能计数器。

1. 选择 **"启动** > **管理工具** > **性能监视器**"。
2. 在 **"性能监视器"** 对话框中，**展开监视工具**，选择**性能监视器**，然后选择"**添加**"。
3. 在"**添加计数器"** 对话框中，从 **"可用计数器"** 列表中展开 RemoteFX 图形部分。
4. 选择要监视的计数器。
5. 在**所选对象的实例**列表中，选择要监视所选计数器的特定实例，然后选择 **"添加**"。 要选择所有可用的计数器实例，请选择 **"所有实例**"。
6. 添加计数器后，选择 **"确定**"。

选定的性能计数器将显示在"性能监视器"屏幕上。

>[!NOTE]
>主机上的每个活动会话都有其每个性能计数器的实例。

## <a name="diagnose-issues"></a>诊断问题

与图形相关的性能问题通常分为四类：

- 低帧速率
- 随机摊位
- 高输入延迟
- 帧质量差

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>解决低帧速率、随机停滞和高输入延迟

首先检查输出帧/秒计数器。 它测量提供给客户端的帧数。 如果此值小于输入帧/秒计数器，则跳过帧。 要识别瓶颈，请使用跳过的帧/秒计数器。

跳过/秒的帧计数器有三种类型：

- 跳过/秒的帧（服务器资源不足）
- 跳过/秒的帧（网络资源不足）
- 跳过/秒的帧（客户端资源不足）

对于任何跳过的帧/秒计数器的较高值意味着问题与计数器跟踪的资源有关。 例如，如果客户端不解码和以服务器提供帧的相同速率呈现帧，则跳过的帧/秒（客户端资源不足）计数器将很高。

如果输出帧/秒计数器与输入帧/秒计数器匹配，但您仍然注意到异常延迟或延迟，则平均编码时间可能是罪魁祸首。 编码是在单会话 （vGPU） 方案中的服务器上和多会话方案中的 VM 上发生的同步过程。 平均编码时间应低于 33 毫秒。 如果平均编码时间低于 33 毫秒，但仍存在性能问题，则可能应用或操作系统有问题。

有关诊断应用相关问题的详细信息，请参阅[用户输入延迟性能计数器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

由于 RDP 支持 33 ms 的平均编码时间，因此它支持高达 30 帧/秒的输入帧速率。 请注意，33 ms 是支持的最大帧速率。 在许多情况下，用户经历的帧速率将较低，具体取决于源向 RDP 提供帧的频率。 例如，观看视频等任务需要 30 帧/秒的完整输入帧速率，但计算密集型任务（如不常编辑文档）导致输入帧/秒的值要低得多，并且用户体验质量不会降低。

### <a name="addressing-poor-frame-quality"></a>解决帧质量差的问题

使用帧质量计数器诊断帧质量问题。 此计数器表示输出帧的质量，作为源帧质量的百分比。 质量损失可能是由于 RemoteFX 造成的，或者可能是图形源固有的。 如果 RemoteFX 导致质量损失，则问题可能是缺少网络或服务器资源来发送高保真内容。

## <a name="mitigation"></a>缓解操作

如果服务器资源导致瓶颈，请尝试以下方法之一来提高性能：

- 减少每个主机的会话数。
- 增加服务器上的内存和计算资源。
- 断开连接的分辨率。

如果网络资源导致瓶颈，请尝试以下方法之一来提高每个会话的网络可用性：

- 减少每个主机的会话数。
- 使用带宽较高的网络。
- 断开连接的分辨率。

如果客户端资源导致瓶颈，请尝试以下方法之一来提高性能：

- 安装最新的远程桌面客户端。
- 增加客户端计算机上的内存和计算资源。

> [!NOTE]
> 我们目前不支持源帧/秒计数器。 目前，源帧/秒计数器将始终显示 0。

## <a name="next-steps"></a>后续步骤

- 要创建 GPU 优化的 Azure 虚拟机，请参阅[为 Windows 虚拟桌面环境配置图形处理单元 （GPU） 加速](configure-vm-gpu.md)。
- 有关故障排除和升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 要了解有关该服务的更多信息，请参阅[Windows 桌面环境](environment-setup.md)。

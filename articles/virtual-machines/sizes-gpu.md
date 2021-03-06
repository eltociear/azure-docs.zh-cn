---
title: Azure VM 大小 - GPU |微软文档
description: 列出 Azure 中可用于虚拟机的不同 GPU 优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913576"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 优化虚拟机大小

GPU 优化 VM 大小是具有单个或多个 NVIDIA GPU 的专用虚拟机。 这些大小是针对计算密集型、图形密集型和可视化工作负荷设计的。 本文介绍有关 GPU、vCPU、数据磁盘和 NIC 的数量和类型的信息。 此分组中的每个大小还包括存储吞吐量及网络带宽。

- [NC 系列](nc-series.md)[、NCv2 系列](ncv2-series.md)[、NCv3 系列](ncv3-series.md)尺寸针对计算密集型和网络密集型应用和算法进行了优化。 例如 CUDA 和基于 OpenCL 的应用程序和仿真、AI 和深度学习。 NCv3 系列专用于采用 NVIDIA Tesla V100 GPU 的高性能计算工作负载。 NC 系列使用英特尔至强 E5-2690 v3 2.60GHz v3 （哈斯韦尔） 处理器，NCv2 系列和 NCv3 系列 VM 使用英特尔至强 E5-2690 v4 （Broadwell） 处理器。

- [ND系列](nd-series.md)和[NDv2系列](ndv2-series.md)尺寸侧重于深度学习的训练和推理方案。 他们使用NVIDIA特斯拉P40 GPU和英特尔至强E5-2690 v4（布罗德韦尔）处理器。 NDv2 系列使用英特尔 Xeon 白金 8168（天湖）处理器。

- [NV 系列](nv-series.md)和[NVv3 系列](nvv3-series.md)尺寸经过优化设计，适用于使用 OpenGL 和 DirectX 等框架的远程可视化、流式处理、游戏、编码和 VDI 方案。 这些 VM 由 NVIDIA Tesla M60 GPU 提供支持。

- [NVv4 系列](nvv4-series.md)针对 VDI 和远程可视化优化和设计的 VM 大小。 NVv4 具有分区 GPU，可为需要较小 GPU 资源的工作负载提供合适的大小。 这些 VM 由 AMD Radeon 本能 MI25 GPU 支持。 NVv4 VM 目前仅支持 Windows 来宾操作系统。

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

要利用 Azure N 系列 VM 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。

[NVIDIA GPU 驱动程序扩展](/azure/virtual-machines/extensions/hpccompute-gpu-windows)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](/azure/virtual-machines/extensions/hpccompute-gpu-windows)。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](/azure/virtual-machines/extensions/overview)。

如果选择手动安装 NVIDIA GPU 驱动程序，请参阅适用于 Windows 的[N 系列 GPU 驱动程序设置](/azure/virtual-machines/windows/n-series-driver-setup)，或针对 Linux 的 N 系列 GPU[驱动程序设置](/azure/virtual-machines/linux/n-series-driver-setup)，了解支持的操作系统、驱动程序、安装和验证步骤。

## <a name="deployment-considerations"></a>部署注意事项

- 有关 N 系列 VM 的可用性，请查看[可用产品(按区域)](https://azure.microsoft.com/regions/services/)。

- N 系列 VM 只能按 Resource Manager 部署模型部署。

- N 系列的 VM 在对其磁盘支持的 Azure 存储类型方面有所不同。 NC 和 NV VM 仅支持标准磁盘存储 (HDD) 所支持的 VM 磁盘。 NCv2、NCv3、ND、NDv2 和 NVv2 VM 仅支持高级磁盘存储 (SSD) 所支持的 VM 磁盘。

- 如果需要部署的 N 系列 VM 较多，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- 可能需要提高 Azure 订阅中的核心配额（按区域）以及单独针对 NC、NCv2、NCv3、ND、NDv2、NV 或 NVv2 核心的配额。 要请求增加配额，[请免费打开在线客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 默认限制可能因订阅类别而异。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [高性能计算](sizes-hpc.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

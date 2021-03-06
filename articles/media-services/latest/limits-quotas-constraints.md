---
title: Azure 媒体服务中的配额和限制
description: 本主题介绍 Microsoft Azure 媒体服务中的配额和限制。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: 2d4f5f83335f6f115362bcf66cf69d7f9de7eaa5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582324"
---
# <a name="azure-media-services-quotas-and-limits"></a>Azure 媒体服务配额和限制

本文列出了一些最常见的 Microsoft Azure 媒体服务限制，有时也称为配额限制。

> [!NOTE]
> 对于未修复的资源，请打开一个支持票证，要求增加配额。 不要创建其他 Azure 媒体服务帐户以尝试获取更高的限制。

## <a name="account-limits"></a>帐户限制

| 资源 | 默认限制 | 
| --- | --- | 
| 单个订阅中的[媒体服务帐户](media-services-account-concept.md) | 25（固定） |

## <a name="asset-limits"></a>资产限额

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户[的资产](assets-concept.md) | 1,000,000|

## <a name="storage-limits"></a>存储限制

| 资源 | 默认限制 | 
| --- | --- | 
| 文件大小| 在某些情况下，支持在媒体服务中处理的最大文件大小存在限制。 <sup>(1)</sup> |
| [存储帐户](storage-account-concept.md) | 100<sup>（2）</sup> （固定） |

<sup>1</sup> 在 Azure Blob 存储中，单个 Blob 目前支持的最大大小为 5 TB。 媒体服务会根据服务使用的 VM 大小应用其他限制。 大小限制适用于你上传的文件，也适用于由于媒体服务处理（编码或分析）而生成的文件。 如果源文件大于 260 GB，作业可能会失败。 

下表显示了媒体保留单位（S1、S2 和 S3）的限制。 如果源文件大于表中定义的限制，则编码作业将失败。 编码持续时间较长的 4K 分辨率源时，需要使用 S3 媒体保留单位才能达到所需的性能。 如果 S3 媒体保留单位上的 4K 内容大于 260-GB 限制，请开具支持票证。

|媒体保留单位类型|最大输入大小 (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup>存储帐户必须来自同一 Azure 订阅。

## <a name="jobs-encoding--analyzing-limits"></a>作业（编码&分析）限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户的[作业](transforms-jobs-concept.md) | 500，000 <sup>（3）</sup> （固定）|
| 每个作业的作业输入 | 50（固定）|
| 每个作业的作业输出 | 20（固定） |
| 每个媒体服务帐户[的转换](transforms-jobs-concept.md) | 100（固定）|
| 变换转换中的输出 | 20（固定） |
| 每个作业输入的文件|10（固定）|

<sup>3</sup>此数字包括排队、已完成、处于活动状态和取消的作业。 不包括已删除的作业。 

即使记录总数低于最大配额，也会自动删除帐户中所有超过 90 天的作业记录。 

## <a name="live-streaming-limits"></a>实时流限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户[的实时事件](live-events-outputs-concept.md) <sup>（4）</sup> |5|
| 每个直播活动的实时输出 |3 <sup>（5）</sup> |
| 最长实时输出持续时间 | 25 小时 |

<sup>4</sup>有关实时事件限制的详细信息，请参阅[实时事件类型比较和限制](live-event-types-comparison.md)。

<sup>5</sup>实时输出从创建开始，删除时停止。

## <a name="packaging--delivery-limits"></a>包装&交货限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户[的流式处理终结点](streaming-endpoint-concept.md)（已停止或运行）|2 |
| [动态清单筛选器](filters-dynamic-manifest-overview.md)|100|
| [流式处理策略](streaming-policy-concept.md) | 100 <sup>（6）</sup> |
| 一次与资产关联的唯一[流式处理器](streaming-locators-concept.md) | 100<sup>（7）</sup> （固定） |

<sup>6</sup>使用自定义[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)时，应为媒体服务帐户设计一组有限的此类策略，并在需要相同的加密选项和协议时将其重新用于流式处理器。 不应为每个流式处理定位符创建新的流式处理策略。

<sup>7</sup>流式处理器不是用于管理每个用户的访问控制。 要为不同用户提供不同的访问权限，请使用数字权限管理 (DRM) 解决方案。

## <a name="protection-limits"></a>保护限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个[内容密钥策略的选项](content-key-policy-concept.md) |30 | 
| 每个帐户的媒体服务密钥交付服务上每种 DRM 类型的每月许可证数|1,000,000|

## <a name="support-ticket"></a>支持票证

对于未固定的资源，您可以通过打开[支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)请求提高配额。 请在请求中包含有关所需的配额更改、用例方案和所需区域的详细信息。 <br/>请**不要**创建更多的 Azure 媒体服务帐户以求获取更高的限制。

## <a name="next-steps"></a>后续步骤

[概述](media-services-overview.md)

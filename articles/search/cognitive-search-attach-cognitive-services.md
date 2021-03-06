---
title: 将认知服务附加到技能集
titleSuffix: Azure Cognitive Search
description: 有关在 Azure 认知搜索中将认知服务一体式订阅附加到 AI 扩充管道的说明。
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472445"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>将认知服务资源附加到 Azure 认知搜索中的技能集 

在 Azure 认知搜索中配置浓缩管道时，可以免费丰富数量有限的文档。 对于更大和更频繁的工作负载，应附加计费的认知服务资源。

在本文中，您将学习如何通过将密钥分配给定义浓缩管道的技能集来附加资源。

## <a name="resources-used-during-enrichment"></a>浓缩期间使用的资源

Azure 认知搜索依赖于认知服务，包括用于图像分析和光学字符识别 （OCR）[的计算机视觉](https://azure.microsoft.com/services/cognitive-services/computer-vision/)、用于自然语言处理[的文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)以及其他丰富功能，如[文本翻译](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)。 在 Azure 认知搜索中的扩充上下文中，这些 AI 算法被包装在*技能*中，放置在*技能集中**，并在索引*器编制期间引用。

## <a name="how-billing-works"></a>计费原理

+ Azure 认知搜索使用您在技能集上提供的认知服务资源密钥，以便为图像和文本富集计费。 执行计费技能是在[认知服务支付即用即付的价格](https://azure.microsoft.com/pricing/details/cognitive-services/)。

+ 图像提取是 Azure 认知搜索操作，在富集之前文档破裂时发生。 图像提取是计费的。 有关图像提取定价，请参阅[Azure 认知搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)。

+ 文本提取也会在文档破解短语期间发生。 它不计费。

+ 不调用认知服务的技能（包括条件、沙珀、文本合并和文本拆分技能）不可计费。

## <a name="same-region-requirement"></a>相同区域要求

我们要求 Azure 认知搜索和 Azure 认知服务存在于同一区域中。 否则，将在运行时收到此消息：`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

无法跨区域移动服务。 如果收到此错误，则应在 Azure 认知搜索相同的区域中创建新的认知服务资源。

> [!NOTE]
> 某些内置技能基于非区域认知服务（例如，[文本翻译技能](cognitive-search-skill-text-translation.md)）。 使用非区域技能意味着您的请求可能在 Azure 认知搜索区域以外的区域中提供服务。 有关详细信息，请参阅[认知服务产品（按区域）](https://aka.ms/allinoneregioninfo)页面。

## <a name="use-free-resources"></a>使用免费资源

您可以使用有限的免费处理选项来完成 AI 扩充教程和快速入门练习。

每个索引器每天只能提供 20 个文档的免费（有限浓缩）资源。 您可以删除并重新创建索引器以重置计数器。

1. 打开导入数据向导：

   ![打开导入数据向导](media/search-get-started-portal/import-data-cmd.png "打开导入数据向导")

1. 选择数据源并继续**添加 AI 扩充（可选）。** 有关此向导的分步演练，请参阅[在 Azure 门户中创建索引](search-get-started-portal.md)。

1. 展开“附加认知服务”，然后选择“免费(有限扩充)”：********

   ![扩展附加认知服务部分](./media/cognitive-search-attach-cognitive-services/attach1.png "扩展附加认知服务部分")

1. 现在，您可以继续执行后续步骤，包括**添加认知技能**。

## <a name="use-billable-resources"></a>使用付费资源

对于每天创建超过 20 个扩充的工作负荷，请确保附加可计费的认知服务资源。 我们建议你始终附加可计费的认知服务资源，即使你从未打算调用认知服务 API 也是如此。 附加资源会重写每日限制。

只有调用认知服务 API 的技能才收费。 您不需要按[自定义技能](cognitive-search-create-custom-skill-example.md)收费，或者像[文本合并](cognitive-search-skill-textmerger.md)、[文本拆分器](cognitive-search-skill-textsplit.md)和[shaper](cognitive-search-skill-shaper.md)这样的技能，这些技能不是基于 API 的。

1. 打开导入数据向导，选择数据源，并继续**添加 AI 充实（可选）。**

1. 展开“附加认知服务”，然后选择“创建新的认知服务资源”。******** 此时会打开一个新的选项卡让你创建资源：

   ![创建认知服务资源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "创建认知服务资源")

1. 在 **"位置**"列表中，选择 Azure 认知搜索服务所在的区域。 出于性能方面的原因，请确保使用此区域。 使用此区域还可避免跨区域的出站带宽费用。

1. 在**定价层**列表中，选择**S0**以获取认知服务功能的一体集合，包括支持 Azure 认知搜索提供的内置技能的视觉和语言功能。

   对于 S0 层，可以在[认知服务定价页](https://azure.microsoft.com/pricing/details/cognitive-services/)上找到特定工作负荷的费率。
  
   + 在“选择套餐”列表中，确保“认知服务”已选中。********
   + 在“语言”功能下，“文本分析标准版”的费率适用于 AI 索引。********
   + 在“视觉”功能下，适用“计算机视觉 S1”的费率。********

1. 选择“创建”预配新的认知服务资源。****

1. 返回到包含导入数据向导的上一选项卡。 选择“刷新”显示该认知服务资源，然后选择该资源。****

   ![选择认知服务资源](./media/cognitive-search-attach-cognitive-services/attach2.png "选择认知服务资源")

1. 展开"**添加认知技能"** 部分，以选择要在数据上运行的特定认知技能。 完成向导中的剩余步骤。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>将现有技能集附加到认知服务资源

如果你有现有的技能集，可将其附加到新的或不同的认知服务资源。

1. 在“服务概述”页上选择“技能集”：********

   ![技能集选项卡](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能集选项卡")

1. 选择技能集的名称，然后选择现有资源或新建资源。 选择“确定”以确认所做的更改。****

   ![技能集资源列表](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能集资源列表")

   请记住，“免费(有限扩充)”选项限制为每日 20 个文档，可以使用“创建新的认知服务资源”预配新的可计费资源。******** 如果创建了新资源，请选择“刷新”以刷新认知服务资源的列表，然后选择该资源。****

## <a name="attach-cognitive-services-programmatically"></a>以编程方式附加认知服务

以编程方式定义技能集时，请将 `cognitiveServices` 节添加到该技能集。 在该节中，包含要与该技能集关联的认知服务资源的键。 请记住，资源必须与 Azure 认知搜索资源位于同一区域。 另外请包含 `@odata.type`，并将其设置为 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

以下示例演示了此模式。 请注意定义末尾的 `cognitiveServices` 节。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>示例：估算成本

若要估算认知搜索索引编制的相关成本，请先构想一下文档的大致结构，以便能够得出一些数字。 例如，可以使用以下似近值：

+ 1,000 个 PDF。
+ 每个 PDF 有 6 个页面。
+ 每个页面包含一个图像（共 6,000 个图像）。
+ 每个页面包含 3,000 个字符。

假设管道的功能包括：每个 PDF 的文档破解、图像和文本提取、图像的光学字符识别 (OCR)，以及组织的实体识别。

本文中所示的价格是虚构的。 这些价格用于演示估算过程。 你的成本可能更低。 有关实际交易价格，请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 破解包含文本和图像内容的文档时，文本提取目前是免费的。 对于 6,000 个图像，假设每提取 1,000 个图像需要 $1。 则此步骤的成本是 $6.00。

2. 对于英语环境中 6000 个图像的 OCR，OCR 认知技能使用最佳算法 (DescribeText)。 假设每分析 1,000 个图像的成本为 $2.50，则这一步需要支付 $15.00。

3. 提取实体时，每页总共有 3 个文本记录。 每条记录包含 1,000 个字符。 每页 3 个文本记录 * 6,000 页 = 18,000 个文本记录。 假设 1000 个文本记录的成本为 $2.00，则这一步的成本为 $36.00。

综合起来，在使用上述技能集引入 1,000 个此类 PDF 文档时，需要支付大约 $57.00。

## <a name="next-steps"></a>后续步骤
+ [Azure 认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)

---
title: Azure 媒体服务 DRM 加密和许可证交付服务
titleSuffix: Azure Media Services
description: 了解如何使用 DRM 动态加密和许可证交付服务来交付使用 Microsoft PlayReady、Google Widevine 或 Apple FairPlay 许可证加密的流。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086728"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>教程：使用 DRM 动态加密和许可证交付服务

> [!NOTE]
> 尽管本教程使用了 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 示例，但 [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支持的 [SDK](media-services-apis-overview.md#sdks) 的常规步骤是相同的。

可以使用 Azure 媒体服务来传送通过 Microsoft PlayReady、Google Widevine 或 Apple FairPlay 许可证加密的流。 如需深入说明，请参阅[使用动态加密保护内容](content-protection-overview.md)。

媒体服务还提供提供 PlayReady、Widevine 和 FairPlay DRM 许可证的服务。 当用户请求受 DRM 保护的内容时，播放器应用会从媒体服务许可证服务请求许可证。 如果播放器应用获得授权，媒体服务许可证服务会向该播放器颁发许可证。 许可证包含客户端播放器用来对内容进行解密和流式传输的解密密钥。

本文基于[使用 DRM 进行加密](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM)示例。

本文中所述的示例生成以下结果：

![Azure Media Player 中的具有 DRM 保护视频的 AMS](./media/protect-with-drm/ams_player.png)

本教程演示如何：

> [!div class="checklist"]
> * 创建编码转换。
> * 设置用于验证令牌的签名密钥。
> * 设置对内容密钥策略的要求。
> * 使用指定的流式处理策略创建 StreamingLocator。
> * 创建一个用于播放文件的 URL。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

以下项目是完成本教程所需具备的条件：

* 查看[内容保护概述](content-protection-overview.md)一文。
* 查看[具有访问控制的设计多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)。
* 安装 Visual Studio Code 或 Visual Studio。
* 按照[本快速入门](create-account-cli-quickstart.md)所述，创建新的 Azure 媒体服务帐户。
* 根据[访问 API](access-api-cli-how-to.md) 中所述，获取使用媒体服务 API 时所需的凭据
* 在应用配置文件 (appsettings.json) 中设置相应的值。

## <a name="download-code"></a>下载代码

使用以下命令，将包含本文中所述完整 .NET 示例的 GitHub 存储库克隆到计算机：

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
“使用 DRM 进行加密”示例位于 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) 文件夹中。

> [!NOTE]
> 每次运行应用时，该示例就会创建唯一的资源。 通常，我们会重复使用现有的资源，例如转换和策略（如果现有资源具有所需的配置）。

## <a name="start-using-media-services-apis-with-net-sdk"></a>开始结合使用媒体服务 API 与 .NET SDK

若要开始将媒体服务 API 与 .NET 结合使用，请创建 AzureMediaServicesClient 对象****。 若要创建对象，需要提供客户端所需凭据以使用 Azure AD 连接到 Azure。 在本文开头克隆的代码中，**GetCredentialsAsync** 函数根据本地配置文件中提供的凭据创建 ServiceClientCredentials 对象。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>创建输出资产  

输出[资产](assets-concept.md)会存储作业编码的结果。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>获取或创建编码转换

创建新[转换](transforms-jobs-concept.md)实例时，需要指定希望生成的输出内容。 所需参数是 `transformOutput` 对象，如以下代码所示。 每个 TransformOutput 包含一个预设****。 预设介绍了视频和/或音频处理操作的分步说明，这些操作将用于生成所需的 TransformOutput。 本文中的示例使用名为 AdaptiveStreaming 的内置预设****。 此预设将输入的视频编码为基于输入的分辨率和比特率自动生成的比特率阶梯（比特率 - 分辨率对），并通过与每个比特率 - 分辨率对相对应的 H.264 视频和 AAC 音频生成 ISO MP4 文件。 

在创建新的**转换**之前，应该先检查是否已存在使用 **Get** 方法的转换，如以下代码中所示。  在 Media Services v3**获取**实体上的方法返回**null**如果实体不存在 （不区分大小写的名称检查）。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>提交作业

如上所述，**转换**对象为脚本，[作业则是](transforms-jobs-concept.md)对媒体服务的实际请求，请求将转换应用到给定输入视频或音频内容****。 Job 指定输入视频位置和输出位置等信息****。

本教程基于直接从 [HTTPs 源 URL](job-input-from-http-how-to.md) 引入的文件创建作业的输入。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>等待作业完成

该作业需要一些时间才能完成操作。 在该过程中，你应能够接收通知。 以下代码示例显示如何轮询服务以获取**作业**状态。 对于生产应用程序，由于可能出现延迟，并不建议将轮询作为最佳做法。 如果在帐户上过度使用轮询，轮询会受到限制。 开发者应改用事件网格。 请参阅[将事件路由到自定义 Web 终结点](job-state-events-cli-how-to.md)。

作业通常将经历以下状态：“已计划”、“已排队”、“正在处理”、“已完成”（最终状态）********************。 如果作业出错，则显示“错误”状态****。 如果作业正处于取消过程中，则显示“正在取消”，完成时则显示“已取消”********。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>创建内容密钥策略

内容密钥提供对资产的安全访问。 通过 DRM 加密内容时，需要创建[内容密钥策略](content-key-policy-concept.md)。 此策略配置如何将内容密钥传送到最终的客户端。 内容密钥与流定位器相关联。 媒体服务还提供密钥传送服务，将加密密钥和许可证传送给已授权的用户。

您需要在**内容密钥策略**上设置要求（限制），必须满足这些要求（限制）才能使用指定的配置传递密钥。 此示例设置了以下配置和要求：

* Configuration

    配置了 [PlayReady](playready-license-template-overview.md) 和 [Widevine](widevine-license-template-overview.md) 许可证，因此只能由媒体服务许可证传送服务传送这些许可证。 即使此示例应用未配置[FairPlay](fairplay-license-overview.md)许可证，它包含可用于配置公平播放的方法。 您可以将 FairPlay 配置添加为另一个选项。

* 限制

    该应用在策略中设置了 JSON Web 令牌 (JWT) 令牌类型限制。

当播放器请求流时，媒体服务将使用指定的密钥动态加密内容。 为解密流，播放器从密钥传送服务请求密钥。 为了确定是否已授权用户获取密钥，服务将评估你为密钥指定的内容密钥策略。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>创建流定位符

完成编码并设置内容密钥策略后，下一步是使输出资产中的视频可供客户端播放。 可以通过两个步骤来提供视频：

1. 创建[流式处理器](streaming-locators-concept.md)。
2. 生成客户端可以使用的流式处理 URL。

创建**流定位器**的过程称为发布。 默认情况下，除非配置可选的开始和结束时间，否则调用 API 后，流式处理定位符**** 立即生效， 并持续到被删除为止。

创建**流定位器**时，需要指定所需的 `StreamingPolicyName`。 本教程使用某个预定义的流式处理策略来告知 Azure 媒体服务如何发布流式处理的内容。 在此示例中，请将 StreamingLocator.StreamingPolicyName 设置为“Predefined_MultiDrmCencStreaming”策略。 应用 PlayReady 和 Widevine 加密，并根据配置的 DRM 许可证将密钥传递到播放客户端。 如果还要使用 CBCS (FairPlay) 加密流，请使用“Predefined_MultiDrmStreaming”。

> [!IMPORTANT]
> 使用自定义的[流策略](streaming-policy-concept.md)时，应为媒体服务帐户设计有限的一组此类策略，并在需要同样的加密选项和协议时重新将这些策略用于 StreamingLocators。 媒体服务帐户具有对应于 StreamingPolicy 条目数的配额。 不应为每个 StreamingLocator 创建新的 StreamingPolicy。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>获取测试令牌

本教程在内容密钥策略中指定使用令牌限制。 令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用 [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) 格式的令牌，我们在示例中配置了此格式。

ContentKeyPolicy 中使用了 ContentKeyIdentifierClaim，这意味着，提供给密钥传送服务的令牌必须包含 ContentKey 的标识符。 本示例未指定内容密钥，在创建流定位器时，系统会创建一个随机内容密钥。 若要生成测试令牌，必须获取要放入 ContentKeyIdentifierClaim 声明中的 ContentKeyId。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>生成流 URL

创建 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 后，可以获取流 URL。 若要生成 URL，需要连接 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 主机名称和流定位器路径****。 在此示例中，使用*默认***流式处理终结点**。 首次创建媒体服务帐户时，此*默认***流式处理终结点**将处于已停止状态，因此您需要调用**Start**。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

运行应用时看到以下屏幕：

![使用 DRM 提供保护](./media/protect-with-drm/playready_encrypted_url.png)

可以打开浏览器并粘贴生成的 URL 来启动 Azure Media Player 演示页，其中已经填充了该 URL 和令牌。

## <a name="clean-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

一般来说，除了打算重用的对象之外，应该清除所有对象（通常，将重用转换、StreamingLocators 等）。 如果希望帐户在试验后保持干净状态，则删除不打算重复使用的资源。 例如，以下代码可删除作业：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除之前创建的资源组。

执行以下 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>附加说明

* Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

签出

> [!div class="nextstepaction"]
> [使用 AES-128 进行保护](protect-with-aes128.md)

---
title: 如何安装和运行容器 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 如何在本演练教程中下载、安装和运行计算机视觉容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f8d19ded32f7f4a90b23106b6cec53418eef407e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458029"
---
# <a name="install-and-run-read-containers-preview"></a>安装并运行读取容器（预览）

容器使您能够在您自己的环境中运行计算机视觉 API。 容器非常适合特定的安全和数据治理要求。 在本文中，您将学习如何下载、安装和运行计算机视觉容器。

单个 Docker 容器*Read*可用于计算机视觉。 *"读取"* 容器允许您从不同表面和背景的各种对象（如收据、海报和名片）的图像中检测和提取*打印的文本*。 此外，*读取*容器可检测图像中的*手写文本*，并提供 PDF、TIFF 和多页文件支持。 有关详细信息，请参阅[读取](concept-recognizing-text.md#read-api)API 文档。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

在使用容器之前，必须满足以下先决条件：

|必选|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> **在 Windows 上**，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|计算机视觉资源 |若要使用容器，必须具有：<br><br>Azure**计算机视觉**资源和关联的 API 键端点 URI。 这两个值都可以在资源的“概述”和“密钥”页上找到，并且是启动容器所必需的。<br><br>**[API_KEY]**：**密钥**页上的两个可用资源键之一<br><br>**[ENDPOINT_URI]**：**概述**页上提供的终结点|

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

**主机**是运行 Docker 容器的计算机。 主机*必须支持*[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)（AVX2）。 可以使用以下命令检查 Linux 主机上的 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> 支持 AVX2*需要*主机。 如果没有 AVX2 支持，容器*将不能*正常工作。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

可供读取的容器映像可用。

| 容器 | 容器注册表/存储库/映像名称 |
|-----------|------------|
| 读取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令下载容器映像。

### <a name="docker-pull-for-the-read-container"></a>读取容器的 Docker 拉拔

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](computer-vision-resource-container-config.md)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{ENDPOINT_URI}` 和 `{API_KEY}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。

`docker run` 命令的[示例](computer-vision-resource-container-config.md#example-docker-run-commands)可用。

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行读取容器。
* 分配 8 个 CPU 内核和 16 GB 的内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

提供 `docker run` 命令的多个[示例](./computer-vision-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `http://localhost:5000`，以获得容器 API。

### <a name="asynchronous-read"></a>异步读取

可以协同使用`POST /vision/v2.0/read/core/asyncBatchAnalyze`和`GET /vision/v2.0/read/operations/{operationId}`操作异步读取图像，类似于计算机视觉服务使用这些相应的 REST 操作的方式。 异步 POST 方法将用作`operationId`识别器的 将 返回到 HTTP GET 请求。

从摇曳的 UI 中，`asyncBatchAnalyze`选择 在浏览器中展开它。 然后选择 **"尝试"选择** > **文件**。 在此示例中，我们将使用以下图像：

![选项卡 vs 空格](media/tabs-vs-spaces.png)

当异步 POST 成功运行时，它将返回**HTTP 202**状态代码。 作为响应的一部分，有一个`operation-location`标头保存请求的结果终结点。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

是`operation-location`完全限定的 URL，可通过 HTTP GET 进行访问。 以下是从前一个映像执行`operation-location`URL 的 JSON 响应：

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>同步读取

可以使用 该`POST /vision/v2.0/read/core/Analyze`操作同步读取图像。 当图像完整读取时，API 才会返回 JSON 响应。 唯一的例外是如果发生错误。 发生错误时，返回以下 JSON：

```json
{
    status: "Failed"
}
```

JSON 响应对象与异步版本具有相同的对象图。 如果您是 JavaScript 用户，并且想要类型安全，可以使用以下类型将 JSON 响应转换为`AnalyzeResult`对象。

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

有关示例用例，请参阅<a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">此处<span class="docon docon-navigate-external x-hidden-focus"></span>的 TypeScript 沙盒</a>，然后选择 **"运行"** 以可视化其易用性。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑难解答

如果运行启用了输出[装入点](./computer-vision-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

认知服务容器使用 Azure 帐户上的相应资源向 Azure 发送计费信息。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./computer-vision-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

在本文中，你已学习相关概念，以及计算机视觉容器的下载、安装和运行工作流。 综上所述：

* 计算机视觉为 Docker 提供 Linux 容器，封装读取。
* 容器映像从 Azure 中的"容器预览"容器注册表下载。
* 容器映像在 Docker 中运行。
* 通过指定容器的主机 URI，可以使用 REST API 或 SDK 在读取容器中调用操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](computer-vision-resource-container-config.md)的配置设置
* 查看[计算机视觉概述](Home.md)，了解有关识别印刷文本和手写文本的详细信息
* 有关该容器支持的方法的详细信息，请参阅[计算机视觉 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)。
* 参阅[常见问题解答 (FAQ)](FAQ.md)，以解决与计算机视觉功能相关的问题。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)

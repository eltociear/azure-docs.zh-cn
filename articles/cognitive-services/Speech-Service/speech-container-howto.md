---
title: 安装语音容器 - 语音服务
titleSuffix: Azure Cognitive Services
description: 安装和运行语音容器。 语音转文本可将音频流实时听录为应用程序、工具或设备可以使用或显示的文本。 文本转语音可将输入文本转换为类似人类的合成语音。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2beee81bc365d00e59a62cacabacc5f5d6b62a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474775"
---
# <a name="install-and-run-speech-service-containers-preview"></a>安装和运行语音服务容器（预览）

容器使您能够在您自己的环境中运行某些语音服务 API。 容器非常适合特定的安全和数据治理要求。 在本文中，您将学习如何下载、安装和运行语音容器。

语音容器使客户能够构建针对强大的云功能和边缘区域优化的语音应用程序体系结构。 有四个不同的容器可用。 这两个标准容器是**语音到文本**和**文本到语音**。 这两个自定义容器是**自定义语音到文本**和**自定义文本到语音**。

> [!IMPORTANT]
> 所有语音容器目前都是[作为公共"封闭"预览](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)的一部分提供的。 当语音容器前进到通用 （GA） 时，将发布公告。

| 函数 | 功能 | 最新版本 |
|--|--|--|
| 语音转文本 | 将连续实时语音或批量音频录制转录为文本，并带有中间结果。 | 2.1.1 |
| 自定义语音转文本 | 使用[自定义语音门户](https://speech.microsoft.com/customspeech)中的自定义模型，将连续实时语音或批处理音频录制转录到文本中，并带有中间结果。 | 2.1.1 |
| 文本转语音 | 使用纯文本输入或语音合成标记语言 （SSML） 将文本转换为自然语音。 | 1.3.0 |
| 自定义文本到语音 | 使用[自定义语音门户](https://aka.ms/custom-voice-portal)中的自定义模型，将文本转换为具有纯文本输入或语音合成标记语言 （SSML） 的自然语音。 | 1.3.0 |

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

使用语音容器之前，以下先决条件：

| 必选 | 目的 |
|--|--|
| Docker 引擎 | 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> **在 Windows 上**，还必须将 Docker 配置为支持 Linux 容器。<br><br> |
| 熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。 |
| 语音资源 | 若要使用这些容器，必须具有：<br><br>一个用于获取关联 API 密钥和终结点 URI 的 Azure 语音资源。__ 可在 Azure 门户的**语音**“概述”和“密钥”页上获取两个值。 必须获取这两个值才能启动容器。<br><br>**[API_KEY]**：**密钥**页上的两个可用资源键之一<br><br>**[ENDPOINT_URI]**：**概述**页上提供的终结点 |

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

填写并提交[认知服务语音容器请求表单](https://aka.ms/speechcontainerspreview/)以请求访问容器。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

**** 主机是运行 docker 容器的计算机。 主机*必须支持*[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)（AVX2）。 可以使用以下命令检查 Linux 主机上的 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> 支持 AVX2*需要*主机。 如果没有 AVX2 支持，容器*将不能*正常工作。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了每个语音容器的最小和建议的资源分配。

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 语音转文本 | 2 核，2 GB 内存 | 4 核，4 GB内存 |

# <a name="custom-speech-to-text"></a>[自定义语音到文本](#tab/cstt)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 自定义语音转文本 | 2 核，2 GB 内存 | 4 核，4 GB内存 |

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 文本转语音 | 单核，2-GB 内存 | 2 核，3 GB内存 |

# <a name="custom-text-to-speech"></a>[自定义文本到语音](#tab/ctts)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 自定义文本到语音 | 单核，2-GB 内存 | 2 核，3 GB内存 |

***

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!NOTE]
> 最小值和建议的基于 Docker 限制，*而不是*主机资源。 例如，语音到文本容器存储大型语言模型的内存映射部分，*建议*整个文件适合内存，即额外的 4-6 GB。 另外，由于模型要在内存中分页，因此首次运行任一容器可能需要更长的时间。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

语音的容器映像可在以下容器注册表中提供。

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

| 容器 | 存储库 |
|-----------|------------|
| 语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[自定义语音到文本](#tab/cstt)

| 容器 | 存储库 |
|-----------|------------|
| 自定义语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

| 容器 | 存储库 |
|-----------|------------|
| 文本转语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[自定义文本到语音](#tab/ctts)

| 容器 | 存储库 |
|-----------|------------|
| 自定义文本到语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>语音容器的 Docker 拉

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>语音到文本容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> 标记`latest`将拉取`en-US`区域设置。 有关其他区域设置，请参阅[语音到文本区域设置](#speech-to-text-locales)。

#### <a name="speech-to-text-locales"></a>语音到文本区域设置

除以下`latest`所有标记外，所有标记均采用以下格式，并且区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

以下标记是格式示例：

```
2.1.1-amd64-en-us-preview
```

有关**语音到文本**容器的所有受支持区域设置，请参阅[语音到文本图像标记](../containers/container-image-tags.md#speech-to-text)。

# <a name="custom-speech-to-text"></a>[自定义语音到文本](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>自定义语音到文本容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> 自定义`locale`语音`voice`容器和 的 容器由容器引入的自定义模型确定。

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>文本到语音容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 标记提取 `en-US` 区域设置和 `jessarus` 语音。 有关其他区域设置，请参阅[文本到语音区域设置](#text-to-speech-locales)。

#### <a name="text-to-speech-locales"></a>文本到语音区域设置

除以下`latest`所有标记外，所有标记均采用以下格式，并且区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

以下标记是格式示例：

```
1.3.0-amd64-en-us-jessarus-preview
```

有关**文本到语音**容器的所有受支持区域设置和相应语音，请参阅[文本到语音图像标记](../containers/container-image-tags.md#text-to-speech)。

> [!IMPORTANT]
> 构造*标准文本到语音转换*的 HTTP POST 时，[语音合成标记语言 （SSML）](speech-synthesis-markup.md)消息`voice`需要具有`name`属性的元素。 该值是相应的容器区域设置和语音，也称为["短名称"。](language-support.md#standard-voices) 例如，`latest`该标记的语音名称为`en-US-JessaRUS`。

# <a name="custom-text-to-speech"></a>[自定义文本到语音](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>自定义文本到语音容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> 自定义`locale`语音`voice`容器和 的 容器由容器引入的自定义模型确定。

***

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](speech-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{Endpoint_URI}` 和 `{API_Key}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 命令的其他[示例](speech-container-configuration.md#example-docker-run-commands)`docker run`也可用。

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

要运行 *"语音到文本"* 容器，请执行以下`docker run`命令。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行*语音到文本*容器。
* 分配 4 个 CPU 内核和 4 GB 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="custom-speech-to-text"></a>[自定义语音到文本](#tab/cstt)

*自定义语音到文本*容器依赖于自定义语音模型。 自定义模型必须已使用[自定义语音门户](https://speech.microsoft.com/customspeech)[进行训练](how-to-custom-speech-train-model.md)。

> [!IMPORTANT]
> 自定义语音模型需要从以下模型版本之一进行训练：
> * **20181201 （v3.3 统一）**
> * **20190520 （v4.14 统一）**
> * **20190701 （v4.17 统一）**<br>
> ![自定义语音训练容器模型](media/custom-speech/custom-speech-train-model-container-scoped.png)

运行容器需要自定义语音**模型 ID。** 可在自定义语音门户的 **"训练"** 页面上找到。 从自定义语音门户导航到 **"训练"** 页面并选择模型。
<br>

![自定义语音培训页面](media/custom-speech/custom-speech-model-training.png)

获取**模型 ID**以用作命令`ModelId`参数的`docker run`参数。
<br>

![自定义语音模型详细信息](media/custom-speech/custom-speech-model-details.png)

下表表示各种`docker run`参数及其相应的说明：

| 参数 | 描述 |
|---------|---------|
| `{VOLUME_MOUNT}` | 主机[卷装载](https://docs.docker.com/storage/volumes/)，docker 用于持久化自定义模型。 例如 *，C：\自定义语音*，其中*C 驱动器*位于主机上。 |
| `{MODEL_ID}` | 自定义语音**模型 ID**来自自定义语音门户的 **"训练**"页。 |
| `{ENDPOINT_URI}` | 计量和计费需要终结点。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |
| `{API_KEY}` | API 密钥是必需的。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |

要运行*自定义语音到文本*容器，请执行以下`docker run`命令：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行*自定义语音到文本*容器。
* 分配 4 个 CPU 内核和 4 GB 内存。
* 从卷输入装载加载*自定义语音到文本*模型，例如*C：\自定义语音*。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 下载给定的模型`ModelId`（如果在卷装载上找不到）。
* 如果以前下载了自定义模型，则忽略`ModelId`。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

要运行*文本到语音转换*容器，请执行以下`docker run`命令。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行*文本到语音转换*容器。
* 分配 2 个 CPU 内核和 1 GB 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="custom-text-to-speech"></a>[自定义文本到语音](#tab/ctts)

*自定义文本到语音转换*容器依赖于自定义语音模型。 自定义模型必须已使用[自定义语音门户](https://aka.ms/custom-voice-portal)[进行训练](how-to-custom-voice-create-voice.md)。 运行容器需要自定义语音**模型 ID。** 可在自定义语音门户的 **"训练"** 页面上找到。 从自定义语音门户导航到 **"训练"** 页面并选择模型。
<br>

![自定义语音培训页面](media/custom-voice/custom-voice-model-training.png)

获取**模型 ID**以用作 docker run`ModelId`命令参数的参数。
<br>

![自定义语音模型详细信息](media/custom-voice/custom-voice-model-details.png)

下表表示各种`docker run`参数及其相应的说明：

| 参数 | 描述 |
|---------|---------|
| `{VOLUME_MOUNT}` | 主机[卷装载](https://docs.docker.com/storage/volumes/)，docker 用于持久化自定义模型。 例如 *，C：\自定义语音*，其中*C 驱动器*位于主机上。 |
| `{MODEL_ID}` | 自定义语音**模型 ID**来自自定义语音门户的 **"训练**"页。 |
| `{ENDPOINT_URI}` | 计量和计费需要终结点。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |
| `{API_KEY}` | API 密钥是必需的。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |

要运行*自定义文本到语音转换*容器，请执行以下`docker run`命令：

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行*自定义文本到语音转换*容器。
* 分配 2 个 CPU 内核和 1 GB 内存。
* 从卷输入装载加载*自定义文本到语音转换*模型，例如*C：\自定义语音*。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 下载给定的模型`ModelId`（如果在卷装载上找不到）。
* 如果以前下载了自定义模型，则忽略`ModelId`。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

***

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

| 容器 | SDK 主机 URL | 协议 |
|--|--|--|
| 语音到文本和自定义语音文本 | `ws://localhost:5000` | WS |
| 文本到语音转换和自定义文本到语音 | `http://localhost:5000` | HTTP |

有关使用 WSS 和 HTTPS 协议的详细信息，请参阅[容器安全性](../cognitive-services-container-support.md#azure-cognitive-services-container-security)。

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>文本到语音转换或自定义文本到语音

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要使用公开端口运行多个容器，请确保在运行每个容器时使用不同的公开端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

可以让此容器和其他 Azure 认知服务容器一起运行在该主机上。 此外，还可以让同一认知服务容器的多个容器一起运行。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑难解答

启动或运行容器时，可能会遇到问题。 使用输出[装载](speech-container-configuration.md#mount-settings)并启用日志记录。 这样做将允许容器生成在排除故障时有用的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

语音容器使用 Azure 帐户中的“语音”资源向 Azure 发送计费信息。**

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](speech-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

本文已介绍语音容器的概念，及其下载、安装和运行工作流。 综上所述：

* 语音为 Docker 提供了四个 Linux 容器，封装了各种功能：
  * *语音到文本*
  * *自定义语音到文本*
  * *文本转语音*
  * *自定义文本到语音*
* 容器映像从 Azure 中的容器注册表下载。
* 容器映像在 Docker 中运行。
* 无论是使用 REST API（仅限文本到语音转换）还是 SDK（语音到文本或文本到语音），您都指定容器的主机 URI。 
* 实例化容器时，必须提供计费信息。

> [!IMPORTANT]
>  如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](speech-container-configuration.md)的配置设置
* 了解如何[使用库伯奈斯和赫尔姆语音服务容器](speech-container-howto-on-premises.md)
* 使用更多[认知服务容器](../cognitive-services-container-support.md)

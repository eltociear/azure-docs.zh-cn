---
title: 创建和使用资源文件 - Azure Batch
description: 了解如何基于各种输入源创建 Batch 资源文件。 本文将会介绍用于创建资源文件并将其放在 VM 上的几个常用方法。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531135"
---
# <a name="creating-and-using-resource-files"></a>创建和使用资源文件

通常，需要提供某种形式的数据才能处理 Azure Batch 任务。 资源文件是通过任务向 Batch 虚拟机 （VM） 提供此数据的方法。 所有类型的任务都支持资源文件：任务、启动任务、作业准备任务、作业发布任务等。本文介绍了一些有关如何创建资源文件并将其放置在 VM 上的常用方法。  

资源文件将数据放入批处理中的 VM，但数据类型及其使用方式是灵活的。 但是存在一些常见用例：

1. 在启动任务中使用资源文件在每个 VM 上预配通用文件
1. 预配任务要处理的输入数据

例如，通用文件可能是用于安装任务运行的应用程序的启动任务中的文件。 输入数据可能是原始图像或视频数据，或者 Batch 要处理的任何信息。

## <a name="types-of-resource-files"></a>资源文件的类型

可使用多种不同的选项来生成资源文件。 资源文件的创建过程根据原始数据的存储位置而有所不同。

用于创建资源文件的选项：

- [存储容器 URL](#storage-container-url)：从 Azure 中的任何存储容器生成资源文件
- [存储容器名称](#storage-container-name)：从链接到 Batch 的 Azure 存储帐户中的容器名称生成资源文件
- [Web 终结点](#web-endpoint)：从任何有效的 HTTP URL 生成资源文件

### <a name="storage-container-url"></a>存储容器 URL

使用存储容器 URL 意味着，具有正确的权限，可以访问 Azure 中的任何存储容器中的文件。 

在此 C# 示例中，文件已上传到用作 Blob 存储的 Azure 存储容器。 若要访问创建资源文件所需的数据，我们首先需要获取对该存储容器的访问权限。

使用适当的权限创建共享访问签名 (SAS) URI 用于访问该存储容器。 设置 SAS 的过期时间和权限。 在本例中未指定开始时间，因此，该 SAS 在生成后会立即生效，并在两个小时后过期。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 必须拥有 `Read` 和 `List` 权限才能访问容器，而只需 `Read` 权限就能访问 Blob。

配置权限后，创建 SAS 令牌并设置 SAS URL 的格式，以访问存储容器。 使用存储容器的格式化 SAS URL，使用 生成资源[`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)文件。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

生成 SAS URL 的替代方法是在 Azure Blob 存储中启用对容器及其 Blob 的匿名公共读取访问。 这样做可以授予对这些资源的只读访问权限，无需共享帐户密钥，也无需 SAS。 公共读取访问通常用于您希望某些 Blob 始终可用于匿名读取访问的情况。 如果你的解决方案存在这种情况，请参阅[匿名访问 Blob](../storage/blobs/storage-manage-access-to-resources.md) 一文详细了解如何管理对 Blob 数据的访问。

### <a name="storage-container-name"></a>存储容器名称

如果不配置并创建 SAS URL，可以使用 Azure 存储容器的名称来访问 Blob 数据。 您使用的存储容器必须位于链接到 Batch 帐户的 Azure 存储帐户中。 该存储帐户称为自动存储帐户。 使用自动存储容器可以绕过配置和创建 SAS URL 来访问存储容器。

在此示例中，我们假设用于创建资源文件的数据已在链接到 Batch 帐户的 Azure 存储帐户中。 如果你没有自动存储帐户，请参阅[创建 Batch 帐户](batch-account-create-portal.md)中的步骤来详细了解如何创建和链接帐户。

如果使用链接的存储帐户，则无需创建和配置存储容器的 SAS URL， 只需提供链接的存储帐户中的存储容器名称即可。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web 终结点

未上传到 Azure 存储的数据仍可用于创建资源文件。 可以指定包含输入数据的任何有效 HTTP URL。 该 URL 将提供给 Batch API，然后，这些数据将用于创建资源文件。

在下面的 C# 示例中，输入数据托管在虚构的 GitHub 终结点上。 API 将从有效的 Web 终结点检索文件，并生成一个资源文件供任务使用。 此方案不需要凭据。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>提示和建议

每个 Azure Batch 任务以不同的方式使用文件，正因如此，Batch 提供了用于管理任务中的文件的选项。 以下方案并不全面，只是涵盖了几种常见情况，并提供了建议。

### <a name="many-resource-files"></a>许多资源文件

Batch 作业可能包含多个任务，而所有这些任务使用相同的通用文件。 如果通用任务文件在多个任务之间共享，则更好的选项可能是使用应用程序包来包含文件，而不是使用资源文件。 应用程序包可以优化下载速度。 此外，每次执行不同的任务，应用程序包中的数据都会缓存，因此，如果任务文件不经常更改，则应用程序包可能很适合你的解决方案。 使用应用程序包时，无需手动管理多个资源文件，或生成 SAS URL 用于访问 Azure 存储中的文件。 Batch 在后台与 Azure 存储协作来存储应用程序包，并将其部署到计算节点。

如果每个任务都有许多特定于该任务的文件，则资源文件是最佳选项，因为使用唯一文件的任务通常需要更新或替换，这与应用程序包内容不太容易。 在更新、添加或编辑单个文件方面，资源文件提供更大的灵活性。

### <a name="number-of-resource-files-per-task"></a>每个任务的资源文件数

如果任务上指定了几百个资源文件，Batch 可能会拒绝该任务过大。 最好是尽量减少任务本身包含的资源文件数，使任务保持较小的大小。

如果无法做到尽量减少任务所需的文件数，可以通过创建单个引用资源文件存储容器的资源文件来优化任务。 为此，请将资源文件放入 Azure 存储容器，并为资源文件使用不同的"容器"[方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods)。 使用 Blob 前缀选项指定要为任务下载的文件集合。

## <a name="next-steps"></a>后续步骤

- 了解可以替代资源文件的[应用程序包](batch-application-packages.md)。
- 有关将容器用于资源文件的详细信息，请参阅[容器工作负载](batch-docker-container-workloads.md)。
- 若要了解如何收集和保存任务的输出数据，请参阅[保存作业和任务输出](batch-task-output.md)。
- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。

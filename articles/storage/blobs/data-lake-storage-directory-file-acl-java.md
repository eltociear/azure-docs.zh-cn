---
title: Azure 数据存储 Gen2 Java SDK，用于文件& ACL
description: 使用用于 Java 的 Azure 存储库在启用了分层命名空间 (HNS) 的存储帐户中管理目录和文件以及目录访问控制列表 (ACL)。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061557"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Java 管理 Azure 数据存储库第 2 代中的目录、文件和 ACL

本文介绍了如何使用 Java 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录、文件与权限。 

[包（Maven）](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [样本](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [第 1 代到第 2 代映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

若要开始，请打开[此页](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)，找到最新版本的 Java 库。 然后，在文本编辑器中打开 pom.xml 文件**。 添加引用该版本的依赖项元素。

如果计划使用 Azure 活动目录 （AD） 对客户端应用程序进行身份验证，则向 Azure 密钥客户端库添加依赖项。 请参阅[将机密客户端库包添加到您的项目](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)。

接下来，将这些 import 语句添加到代码文件。

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>连接到帐户 

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。 

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。 

本示例使用帐户密钥创建**DataLakeServiceClient**实例。

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>使用 Azure 活动目录 （Azure AD） 进行连接

可以使用 Java[的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)使用 Azure AD 对应用程序进行身份验证。

本示例使用客户端 ID、客户端机密和租户 ID 创建**DataLakeServiceClient**实例。  要获取这些值，请参阅[从 Azure AD 获取令牌以授权来自客户端应用程序的请求](../common/storage-auth-aad-app.md)。

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> 有关详细信息，请参阅 Azure[标识客户端库以了解 Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)文档。


## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 您可以通过调用**DataLakeServiceClient.createSystem**方法来创建一个。

此示例创建名为 `my-file-system` 的文件系统。 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>创建目录

可以通过调用 **DataLakeFileSystemClient.createDirectory** 方法来创建目录引用。

此示例将名为 `my-directory` 的目录添加到文件系统，然后添加名为 `my-subdirectory` 的子目录。 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用 **DataLakeDirectoryClient.rename** 方法来重命名或移动目录。 以参数形式传递所需目录的路径。 

此示例将某个子目录重命名为名称 `my-subdirectory-renamed`。

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

此示例将名为 `my-subdirectory-renamed` 的目录移到名为 `my-directory-2` 的目录的子目录中。 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>删除目录

通过调用 **DataLakeDirectoryClient.deleteWithResponse** 方法来删除目录。

此示例删除名为 `my-directory` 的目录。   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>管理目录 ACL

此示例获取并设置名为 `my-directory` 的目录的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

首先，通过创建 **DataLakeFileClient** 类的实例，在目标目录中创建文件引用。 通过调用 **DataLakeFileClient.append** 方法上传文件。 确保通过调用 **DataLakeFileClient.FlushAsync** 方法完成上传。

此示例将文本文件上传到名为 `my-directory` 的目录。

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> 如果文件大小较大，则代码必须对**DataLakeFileClient.append**方法进行多次调用。 请考虑使用**DataLakeFileClient.上传从文件**的方法。 这样，您可以在单个调用中上载整个文件。 
>
> 有关示例，请参阅下一部分。

## <a name="upload-a-large-file-to-a-directory"></a>将大型文件上载到目录

使用**DataLakeFileClient.uploadFromFile**方法上载大型文件，而无需对**DataLakeFileClient.append**方法进行多次调用。

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>管理文件 ACL

此示例获取并设置名为 `upload-file.txt` 的文件的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>从目录下载

首先，创建表示要下载的文件的一个 **DataLakeFileClient** 实例。 使用 **DataLakeFileClient.read** 方法读取该文件。 使用任何 .NET 文件处理 API 将来自流的字节保存到文件。 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>列出目录内容

此示例输出名为 `my-directory` 的目录中每个文件的路径。

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>请参阅

* [API 参考文档](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [包 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [第 1 代到第 2 代映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)
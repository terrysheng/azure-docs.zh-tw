---
title: "Azure 快速入門 - 使用 .NET 在 Azure Blob 儲存體之間傳送物件 | Microsoft Docs"
description: "快速了解使用 .NET 在 Azure Blob 儲存體之間傳送物件"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: robinsh
ms.openlocfilehash: 9c5628307e76bd30d2dd59f284f2c4b30d434223
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>使用 .NET 在 Azure Blob 儲存體之間傳送物件

在本快速入門中，您會了解如何在 Windows 的 Azure Blob 儲存體容器中，使用 C#.NET 上傳、下載及列出區塊 Blob。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

* 使用下列工作負載安裝 [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)：
    - **Azure 開發**

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門使用的範例應用程式是基本的主控台應用程式。 

使用 [git](https://git-scm.com/) 將應用程式的複本下載至您的開發環境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

此命令會將存放庫複製到本機的 git 資料夾。 若要開啟 Visual Studio 解決方案，請找到並開啟 storage-blobs-dotnet-quickstart 資料夾，然後按兩下 storage-blobs-dotnet-quickstart.sln。 

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

在應用程式，您必須提供儲存體帳戶的連接字串。 從 Visual Studio 的方案總管開啟 `app.config` 檔案。 尋找 `StorageConnectionString` 項目。 針對 [值]，將連接字串的整個值取代為從 Azure 入口網站儲存的連接字串。 您的 `storageConnectionString` 看起來應該會像下面這樣：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=<NameHere>;
    AccountKey=<KeyHere>" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>執行範例

此範例會在 [我的文件] 中建立測試檔案、將它上傳至 Blob 儲存體、列出容器中的 Blob，然後以新名稱下載該檔案，以便比較新舊檔案。 

按 F5 執行範例。 在主控台視窗中顯示的輸出，類似下面的內容： 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

當您按任意鍵繼續時，它會刪除儲存體容器和檔案。 在繼續之前，請檢查 MyDocuments 找出這兩個檔案。 您可以開啟它們，並查看它們是否相同。 複製主控台視窗的 Blob URL，將它貼至瀏覽器以檢視 Blob 儲存體中的檔案內容。

您也可以使用 [Azure 儲存體總管](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 之類的工具來檢視 Blob 儲存體中的檔案。 Azure 儲存體總管是免費的跨平台工具，可讓您存取儲存體帳戶資訊。 

確認檔案之後，請按任一鍵以完成示範並刪除測試檔案。 現在您已知道這個範例的工作內容，請開啟 Program.cs 檔案中查看程式碼。 

## <a name="get-references-to-the-storage-objects"></a>取得儲存體物件的參考

第一件事是建立用來存取和管理 Blob 儲存體的物件參考。 這些物件是互為建置基礎，各自都為清單中的下一個物件所使用。

* 建立指向儲存體帳戶之 **CloudStorageAccount** 物件的執行個體。 

* 建立 **CloudBlobClient** 物件的執行個體，它會指向您儲存體帳戶中的 Blob 服務。 

* 建立 **CloudBlobContainer** 物件的執行個體，它代表您要存取的容器。 容器是用來組織 Blob，就像在電腦上用資料夾組織檔案一樣。

只要您有 **CloudBlobContainer**，就可以建立 **CloudBlockBlob** 物件的執行個體，指向您感興趣的特定 Blob 並執行上傳、下載、複製等等作業。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需有關容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

在本節中，您要建立物件的執行個體、建立新的容器，然後設定容器上的權限，以便公開 Blob 並使用 URL 即可存取。 容器名為 **quickstartblobs**。 

因為我們需要在每次執行範例時建立新的容器，所以此範例會使用 **CreateIfNotExists**。 在整個應用程式都使用相同容器的生產環境中，最好只呼叫一次 **CreateIfNotExists**。 或者，若要事先建立容器，以便您不需要在程式碼中加以建立。

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

## <a name="upload-blobs-to-the-container"></a>將 Blob 上傳到容器

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 最常使用的是區塊 Blob，這也是本快速入門中所使用的。 

若要將檔案上傳至 Blob，請取得目標容器中的 Blob 參考。 取得 Blob 參考之後，即可使用 [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) 將資料上傳給它。 如果沒有 Blob，此作業會建立 Blob，如已存在，則予以覆寫。

範例程式碼會建立用於上傳和下載的本機檔案，將要上傳的檔案以 **localFileName** 中的 Blob 名稱儲存為 **fileAndPath**。 下列範例會將檔案上傳到名為 **quickstartblobs** 的容器。

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

有數個上傳方法可以搭配 Blob 儲存體使用。 例如，如果有記憶體資料流，則您可以使用 UploadFromStreamAsync 方法，而不是 UploadFromFileAsync 方法。 

區塊 Blob 可以是任何類型的文字或二進位檔案。 分頁 Blob 主要用於備份 IaaS VM 所用的 VHD 檔案。 附加 Blob 是用於記錄，例如當您想要寫入檔案，並繼續新增更多資訊時。 儲存在 Blob 儲存體中的大部分物件都是區塊 Blob。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

您可以使用 [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) 來取得容器中的檔案清單。 下列程式碼會擷取 Blob 的清單，透過它們執行迴圈，顯示找到的 Blob URI。 您可以複製命令視窗中的 URI，將它貼入至瀏覽器來檢視檔案。

如果容器中有 5,000 個或更少的 Blob，呼叫一次 ListBlobsSegmentedAsync 即可擷取所有的 Blob 名稱。 如果容器中有 5000 個以上的 Blob，服務會以 5000 為一組擷取清單，直到擷取所有的 Blob 名稱。 因此第一次呼叫此 API 時，它會傳回第一組的 5000 個 Blob 名稱和接續權杖。 第二次，您要提供權杖，服務會擷取下一組的 Blob 名稱，一直反復到接續權杖為 null 為止，這表示已擷取所有的 Blob 名稱。 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

## <a name="download-blobs"></a>下載 Blob

使用 [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) 將 Blob 下載到本機磁碟。

下列程式碼會下載上一節中上傳的 Blob，將 "_DOWNLOADED" 尾碼新增至 Blob 名稱，以便您在本機磁碟上看到這兩個檔案。 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本快速入門中上傳的 Blob，您可以使用 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) 刪除整個容器。 如果不再需要所建立的檔案，請一併刪除。

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 .NET 在本機磁碟和 Azure Blob 儲存體之間傳輸檔案。 若要深入瞭解 Blob 儲存體的用法，請繼續閱讀 Blob 儲存體使用說明。

> [!div class="nextstepaction"]
> [Blob 儲存體作業的使用說明](storage-dotnet-how-to-use-blobs.md)

如需儲存體總管和 Blob 的詳細資訊，請參閱[使用儲存體總管管理 Azure Blob 儲存體資源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

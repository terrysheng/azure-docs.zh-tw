---
title: "適用於 Functions 的 Azure Cosmos DB 繫結 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure Cosmos DB 觸發程序和繫結。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: d05c0342e771e229a7175570ad227c4359980990
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>適用於 Functions 的 Azure Cosmos DB 繫結
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何在 Azure Functions 中設定 Azure Cosmos DB 繫結和撰寫其程式碼。 Functions 支援適用於 Azure Cosmos DB 的觸發程序、輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

如需如何使用 Azure Cosmos DB 進行無伺服器計算的詳細資訊，請參閱 [Azure Cosmos DB：使用 Azure Functions 的無伺服器資料庫計算](..\cosmos-db\serverless-computing-database.md)。

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Azure Cosmos DB 觸發程序

Azure Cosmos DB 觸發程序會使用 [Azure Cosmos DB 變更摘要](../cosmos-db/change-feed.md)，跨分割區接聽變更。 觸發程序需要第二個集合，用來在分割區上儲存「租用」。

要監視的集合和包含租用的集合必須可供觸發程序用來運作。

Azure Cosmos DB 觸發程序支援下列屬性：

|屬性  |說明  |
|---------|---------|
|**type** | 必須設為 `cosmosDBTrigger`。 |
|**name** | 函式程式碼中使用的變數名稱，代表有變更的文件清單。 | 
|**direction** | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此參數。 |
|**connectionStringSetting** | 應用程式設定的名稱，包含用來連接到要監視之 Azure Cosmos DB 帳戶的連接字串。 |
|**databaseName** | 含有要監視之集合的 Azure Cosmos DB 資料庫名稱。 |
|**collectionName** | 要監視的集合名稱。 |
| **leaseConnectionStringSetting** | (選擇性) 應用程式設定的名稱，包含連至保存租用集合之服務的連接字串。 如果未設定，會使用 `connectionStringSetting` 值。 在入口網站中建立繫結時，會自動設定此參數。 |
| **leaseDatabaseName** | (選擇性) 保存用來儲存租用之集合的資料庫名稱。 如果未設定，會使用 `databaseName` 設定的值。 在入口網站中建立繫結時，會自動設定此參數。 |
| **leaseCollectionName** | (選擇性) 用來儲存租用的集合名稱。 如果未設定，會使用 `leases` 值。 |
| **createLeaseCollectionIfNotExists** | (選擇性) 設為 `true` 時，如果租用集合尚未存在，即會自動加以建立。 預設值為 `false`。 |
| **leaseCollectionThroughput** | (選擇性) 定義要在建立租用集合時指派的要求單位數。 只有在將 `createLeaseCollectionIfNotExists` 設為 `true` 時才會使用此設定。 使用入口網站建立繫結時，會自動設定此參數。

>[!NOTE] 
>用來連接到租用集合的連接字串必須具有寫入權限。

這些屬性可以在 Azure 入口網站上函式的 [整合] 索引標籤中設定，或是藉由編輯 `function.json` 專案檔來設定。

## <a name="using-an-azure-cosmos-db-trigger"></a>使用 Azure Cosmos DB 觸發程序

本節包含如何使用 Azure Cosmos DB 觸發程序的範例。 範例假設觸發程序中繼資料看起來如下：

```json
{
  "type": "cosmosDBTrigger",
  "name": "documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "<connection-app-setting>",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```
 
如需如何從入口網站中的函式應用程式建立 Azure Cosmos DB 觸發程序的範例，請參閱[建立由 Azure Cosmos DB 所觸發的函式](functions-create-cosmos-db-triggered-function.md)。 

### <a name="trigger-sample-in-c"></a>C# 中的觸發程序範例 #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>以 JavaScript 撰寫的觸發程序範例
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>DocumentDB API 輸入繫結
DocumentDB API 輸入繫結會擷取 Azure Cosmos DB 文件，並將它傳遞給函式的具名輸入參數。 您可以根據叫用該函式的觸發程序來判斷文件識別碼。 

DocumentDB API 輸入繫結在 *function.json* 中具有下列屬性：

|屬性  |說明  |
|---------|---------|
|**name**     | 代表函式中之文件的繫結參數名稱。  |
|**type**     | 必須設為 `documentdb`。        |
|**databaseName** | 包含文件的資料庫。        |
|**collectionName**  | 包含文件的集合名稱。 |
|**id**     | 要擷取之文件的識別碼。 此屬性支援繫結參數。 若要深入了解，請參閱[在繫結運算式中繫結到自訂輸入屬性](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression)。 |
|**sqlQuery**     | 用來擷取多份文件的 Azure Cosmos DB SQL 查詢。 此查詢支援執行階段繫結，例如以下範例：`SELECT * FROM c where c.departmentId = {departmentId}`。        |
|**連接**     |包含 Azure Cosmos DB 連接字串的應用程式設定名稱。        |
|**direction**     | 必須設為 `in`。         |

您無法同時設定 **id** 和 **sqlQuery** 屬性。 如果未設定任何一項，就會擷取整個集合。

## <a name="using-a-documentdb-api-input-binding"></a>使用 DocumentDB API 輸入繫結

* 在 C# 和 F# 函式中，當函式順利結束時，系統會自動保留透過具名輸入參數對輸入文件所做的任何變更。 
* 在 JavaScript 函數中，不會在函數結束時自動執行更新。 請改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 來進行更新。 請參閱 [JavaScript 範例](#injavascript)。

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>單一文件的輸入範例
假設您在 function.json 的 `bindings` 陣列中有下列 DocumentDB API 輸入繫結︰

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_COSMOSDB",     
  "direction": "in"
}
```

請參閱使用此輸入繫結來更新文件的文字值的特定語言範例。

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C# 中的輸入範例 #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F# 中的輸入範例 #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

此範例需要一個指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 相依性的 `project.json` 檔案︰

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

若要新增 `project.json` 檔案，請參閱 [F# 封裝管理](functions-reference-fsharp.md#package)。

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>以 JavaScript 撰寫的輸入範例

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>具有多份文件的輸入範例

假設您想要使用佇列觸發程序來自訂查詢參數，以擷取 SQL 查詢所指定的多份文件。 

在此範例中，佇列觸發程序會提供參數 `departmentId`。`{ "departmentId" : "Finance" }` 的佇列訊息會傳回財務部門的所有記錄。 請在 *function.json* 中使用下列程式碼︰

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>以 C# 撰寫之具有多份文件的輸入範例

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>以 JavaScript 撰寫之具有多份文件的輸入範例

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }       
    context.done();
};
```

## <a id="docdboutput"></a>DocumentDB API 輸出繫結
DocumentDB API 輸出繫結可讓您將新的文件寫入 Azure Cosmos DB 資料庫。 它在 *function.json* 中具有下列屬性：

|屬性  |說明  |
|---------|---------|
|**name**     | 代表函式中之文件的繫結參數名稱。  |
|**type**     | 必須設為 `documentdb`。        |
|**databaseName** | 包含其中將建立文件之集合的資料庫。     |
|**collectionName**  | 包含其中將建立文件之集合的名稱。 |
|**createIfNotExists**     | 一個布林值，用來指出當集合不存在時，是否要建立集合。 預設值為 *false*。 這是因為會使用保留的輸送量來建立新集合，可能會涉及成本。 如需詳細資訊，請瀏覽 [定價頁面](https://azure.microsoft.com/pricing/details/documentdb/)。  |
|**連接**     |包含 Azure Cosmos DB 連接字串的應用程式設定名稱。        |
|**direction**     | 必須設為 `out`。         |

## <a name="using-a-documentdb-api-output-binding"></a>使用 DocumentDB API 輸出繫結
本節說明如何在您的函式程式碼中使用您的 DocumentDB API 輸出繫結。

根據預設，當您在函式中寫入輸出參數時，會在資料庫中建立文件。 這份文件已自動產生 GUID 作為文件識別碼。 您可以藉由在傳遞至輸出參數的 JSON 物件中指定 `id` 屬性，來指定輸出文件的文件識別碼。 

>[!Note]  
>當您指定現有文件的識別碼時，新的輸出文件會覆寫現有文件。 

如果要輸出多個文件，您也可以繫結至 `ICollector<T>` 或 `IAsyncCollector<T>`，`T` 表示其中一種支援的類型。

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>DocumentDB API 輸出繫結範例
假設您在 function.json 的 `bindings` 陣列中有下列 DocumentDB API 輸出繫結︰

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_COSMOSDB",     
  "direction": "out"
}
```

而且您有可接收 JSON 佇列的佇列輸入繫結，其格式如下︰

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

而且您想要針對每一筆記錄建立下列格式的 Azure Cosmos DB 文件：

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

請參閱使用此輸出繫結將文件新增至您的資料庫的特定語言範例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# 中的輸出範例 #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F# 中的輸出範例 #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

此範例需要一個指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 相依性的 `project.json` 檔案︰

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

若要新增 `project.json` 檔案，請參閱 [F# 封裝管理](functions-reference-fsharp.md#package)。

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>以 JavaScript 撰寫的輸出範例

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```

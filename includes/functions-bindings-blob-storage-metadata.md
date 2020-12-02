---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: f4e6d5fb41769544b7be0f689447364988d0380d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998841"
---
BLOB トリガーは、いくつかのメタデータ プロパティを提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらの値は、[CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) 型と同じセマンティクスを持ちます。

|プロパティ  |種類  |説明  |
|---------|---------|---------|
|`BlobTrigger`|`string`|トリガーする BLOB のパス。|
|`Uri`|`System.Uri`|プライマリ ロケーションの BLOB URI。|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blob のシステム プロパティ。 |
|`Metadata` |`IDictionary<string,string>`|BLOB のユーザー定義メタデータ。|

たとえば、次の C# スクリプトと JavaScript の例では、トリガーする BLOBへのパスがログに記録されます (コンテナーを含む)。

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
---
title: Azure Digital Twins 内のオブジェクトに BLOB を追加する方法 | Microsoft Docs
description: Azure Digital Twins 内のオブジェクトに BLOB を追加する方法を説明します
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688334"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Azure Digital Twins 内のオブジェクトに BLOB を追加する方法

BLOB は、一般的なファイルの種類 (画像やログなど) の非構造化表現です。 BLOB では、MIME の種類 (例:"image/jpeg") とメタデータ (名前、説明、型など) を使用して表現するデータの種類が追跡されます。

Azure Digital Twins では、デバイス、スペース、ユーザーへの BLOB のアタッチがサポートされます。 BLOB では、ユーザーのプロファイル画像、デバイスの写真、ビデオ、マップ、またはログを表すことができます。

> [!NOTE]
> この記事では、以下のことを前提としています。
> * Management API 要求を受信するようにインスタンスが正しく構成されていること。
> * 任意の REST クライアントを使用して正しく認証されていること。

## <a name="uploading-blobs-an-overview"></a>BLOB のアップロード: 概要

特定のエンドポイントとそれぞれの機能に BLOB をアップロードするには、マルチパート要求が使用されます。

> [!IMPORTANT]
> マルチパート要求には、次の 3 つの基本情報が必要です。 Azure Digital Twins の場合:
> * **Content-Type** ヘッダー:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Disposition**: `form-data; name="metadata"`。
> * アップロードするファイルの内容。
>
> 正確な **Content-Type** と **Content-Disposition** は使用シナリオによって異なる場合があります。

各マルチパート要求は、複数のチャンクに分割されます。 Azure Digital Twins Management API に対して行われたマルチパート要求は、このような **2** つ のパーツに分割されます **。**

1. 最初のパーツは必須で、上記の **Content-Type** および **Content-Disposition** によって関連付けられている MIME の種類などの BLOB メタデータが含まれています。

1. 2 番目のパーツには、実際の BLOB コンテンツ (ファイルの非構造化コンテンツ) が含まれています。  

2 つのパーツのどちらも **PATCH** 要求には不要です。 どちらも **POST** または作成操作に必要です。

### <a name="blob-metadata"></a>BLOB のメタデータ

**Content-Type** と **Content-Disposition** に加えて、マルチパート要求では、正しい JSON 本文も指定する必要があります。 送信する JSON 本文は、実行される HTTP 要求操作の種類によって決まります。

使用される 4 つの主な JSON スキーマは次のとおりです。

![スペース BLOB][1]

これらのモデル スキーマについては、提供される Swagger ドキュメント内で詳しく説明されています。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[Swagger の使用方法](./how-to-use-swagger.md)に関する記事を読んで、提供されている参照ドキュメントの使用についてご確認ください。

### <a name="examples"></a>例

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

テキスト ファイルを BLOB としてアップロードし、それをスペースに関連付ける POST 要求を行うには:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| パラメーター値 | 置換後の文字列 |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | マルチパート コンテンツ境界名 |

クラス [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) を使用した同じ BLOB アップロードの .NET の実装を以下に示します。

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>API エンドポイント

以下では、コア エンドポイントとその特定の機能のチュートリアルを提供しています。

### <a name="devices"></a>デバイス

BLOB は、デバイスにアタッチすることができます。 (Management API の Swagger リファレンス ドキュメントを表す) 次の図は、BLOB 消費のデバイス関連 API エンドポイントと、それらに渡す必須のパス パラメーターを示しています。

![デバイスの BLOB][2]

たとえば、BLOB を更新または作成して、BLOB をデバイスにアタッチするために、以下に対して PATCH 要求が行われます。

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

要求が成功すると、応答内で DeviceBlob JSON オブジェクトが返されます。 DeviceBlobs は、次の JSON スキーマに準拠します。

| Attribute | type | 説明 | 例 |
| --- | --- | --- | --- |
| **DeviceBlobType** | String | デバイスにアタッチできる BLOB カテゴリ | `Model` と `Specification` |
| **DeviceBlobSubtype*** | String | DeviceBlobType よりもきめ細かい BLOB サブカテゴリ | `PhysicalModel`、`LogicalModel`、`KitSpecification`、および `FunctionalSpecification` |

> [!TIP]
> 上の表を使用して、正常に返された要求データを処理します。

### <a name="spaces"></a>スペース

BLOB は、スペースにもアタッチできます。 次の図は、BLOB とそれらに渡す任意のパス パラメーターを処理する責任を持つすべてのSpace API エンドポイントの一覧を示しています。

![スペース BLOB][3]

たとえば、スペースにアタッチされている BLOB を返すには、以下に対して GET 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

同じエンドポイントに PATCH 要求を行うと、メタデータの説明を更新し、BLOB の新しいバージョンを作成できます。 HTTP 要求は、必要なメタデータおよびマルチパート フォーム データと共に PATCH メソッドを使って実行されます。

操作が成功すると、次のスキーマに準拠する SpaceBlob が返され、返されたデータを利用するために使用できます。

| Attribute | type | 説明 | 例 |
| --- | --- | --- | --- |
| **SpaceBlobType** | String | スペースにアタッチできる BLOB カテゴリ | `Map` と `Image` |
| **SpaceBlobSubtype** | String | SpaceBlobType よりもきめ細かい BLOB サブカテゴリ | `GenericMap`、`ElectricalMap`、`SatelliteMap`、および `WayfindingMap` |

### <a name="users"></a>ユーザー

BLOB は、ユーザー モデルにアタッチできます (たとえば、プロファイルの写真を関連付けるため)。 次の図は、関連するユーザー API エンドポイントと、`id` などの必須のパス パラメーターを示しています。

![ユーザー BLOB][4]

たとえば、ユーザーにアタッチされている BLOB をフェッチするには、必要なフォーム データを使用して GET 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

返される JSON (UserBlobs) は、次の JSON のモデルに準拠しています。

| Attribute | type | 説明 | 例 |
| --- | --- | --- | --- |
| **UserBlobType** | String | ユーザーにアタッチできる BLOB カテゴリ | `Image` と `Video` |
| **UserBlobSubtype** |  String | UserBlobType よりきめ細かい BLOB サブカテゴリ | `ProfessionalImage`、`VacationImage`、`CommercialVideo` |

## <a name="common-errors"></a>一般的なエラー

適切なヘッダー情報が含まれていません。

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>次の手順

提供される Azure Digital Twins Swagger の参照ドキュメントについて詳しくは、[Digital Twins Swagger の使用方法](how-to-use-swagger.md)に関する記事をご覧ください。

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG

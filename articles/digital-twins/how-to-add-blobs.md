---
title: Azure Digital Twins 内のオブジェクトに BLOB を追加する方法 | Microsoft Docs
description: Azure Digital Twins 内のオブジェクトに BLOB を追加する方法について説明します。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 604093dcec048b0991bbc9beac3ef998cc47e351
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974519"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Azure Digital Twins 内のオブジェクトに BLOB を追加する

BLOB は、一般的なファイルの種類 (画像やログなど) の非構造化表現です。 BLOB では、MIME の種類 (例:"image/jpeg") とメタデータ (名前、説明、型など) を使用して表現するデータの種類が追跡されます。

Azure Digital Twins では、デバイス、スペース、ユーザーへの BLOB のアタッチがサポートされます。 BLOB では、ユーザーのプロファイル画像、デバイスの写真、ビデオ、マップ、またはログを表すことができます。

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>BLOB のアップロード: 概要

マルチパート要求を使用して、特定のエンドポイントとそれぞれの機能に BLOB をアップロードできます。

> [!IMPORTANT]
> マルチパート要求には、次の 3 つの情報が必要です。
> * **Content-Type** ヘッダー:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **Content-Disposition**: `form-data; name="metadata"`
> * アップロードするファイルの内容
>
> **Content-Type** と **Content-Disposition** の情報は、使用シナリオによって異なる場合があります。

Azure Digital Twins Management API に対して行われるマルチパート要求には、2 つ の部分があります。

* **Content-Type** および **Content-Disposition** の情報で示される、関連付けられている MIME の種類などの BLOB メタデータ

* Blob の内容 (ファイルの構造化されていない内容)  

2 つの部分のどちらも **PATCH** 要求には不要です。 どちらも **POST** または作成操作に必要です。

### <a name="blob-metadata"></a>BLOB のメタデータ

**Content-Type** と **Content-Disposition** に加えて、マルチパート要求では、正しい JSON 本文も指定する必要があります。 送信する JSON 本文は、実行される HTTP 要求操作の種類によって決まります。

4 つの主な JSON スキーマは次のとおりです。

![JSON スキーマ][1]

Swagger のドキュメントでは、これらのモデル スキーマが詳細に説明されています。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

参照ドキュメントの使用については、[Swagger の使用方法](./how-to-use-swagger.md)に関する記事をご覧ください。

### <a name="examples"></a>例

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

テキスト ファイルを BLOB としてアップロードし、それをスペースに関連付ける **POST** 要求を行うには:

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

| 値 | 置換後の文字列 |
| --- | --- |
| USER_DEFINED_BOUNDARY | マルチパート コンテンツ境界名 |

次に示すコードは、[MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) クラスを使用した同じ BLOB アップロードの .NET での実装です。

```csharp
//Supply your metadata in a suitable format
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

次のセクションでは、コアとなる BLOB 関連の API エンドポイントとその機能について説明します。

### <a name="devices"></a>デバイス

BLOB をデバイスにアタッチできます。 次の図では、Management API に関する Swagger のリファレンス ドキュメントを示します。 BLOB を使用するためのデバイス関連の API エンドポイントと、それらに渡す必要があるパス パラメーターが指定されています。

![デバイスの BLOB][2]

たとえば、BLOB を更新または作成して、BLOB をデバイスにアタッチするために、以下に対して **PATCH** 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

要求が成功すると、応答内で **DeviceBlob** JSON オブジェクトが返されます。 **DeviceBlobs** オブジェクトは、次の JSON スキーマに準拠します。

| Attribute | type | 説明 | 例 |
| --- | --- | --- | --- |
| **DeviceBlobType** | String | デバイスにアタッチできる BLOB カテゴリ | `Model` と `Specification` |
| **DeviceBlobSubtype** | String | **DeviceBlobType** より具体的な BLOB サブカテゴリ | `PhysicalModel`、`LogicalModel`、`KitSpecification`、および `FunctionalSpecification` |

> [!TIP]
> 上の表を使用して、正常に返された要求データを処理します。

### <a name="spaces"></a>スペース

スペースに BLOB をアタッチすることもできます。 次の図では、BLOB を処理するすべてのスペース API エンドポイントの一覧を示します。 また、それらのエンドポイントに渡すすべてのパス パラメーターの一覧も示します。

![スペース BLOB][3]

たとえば、スペースにアタッチされている BLOB を返すには、以下に対して **GET** 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

同じエンドポイントに **PATCH** 要求を行うと、メタデータの説明を更新し、BLOB の新しいバージョンを作成できます。 HTTP 要求は、必要なメタデータおよびマルチパート フォーム データと共に **PATCH** メソッドを使って実行されます。

操作が成功すると、次のスキーマに準拠している **SpaceBlob** オブジェクトが返されます。 それを使用して、返されたデータを使用できます。

| Attribute | type | 説明 | 例 |
| --- | --- | --- | --- |
| **SpaceBlobType** | String | スペースにアタッチできる BLOB カテゴリ | `Map` と `Image` |
| **SpaceBlobSubtype** | String | **SpaceBlobType** より具体的な BLOB サブカテゴリ | `GenericMap`、`ElectricalMap`、`SatelliteMap`、および `WayfindingMap` |

### <a name="users"></a>ユーザー

BLOB を (たとえば、プロファイル画像を関連付けるために) ユーザー モデルにアタッチできます。 次の図では、関連するユーザー API エンドポイントと、`id` などの必須のパス パラメーターを示します。

![ユーザー BLOB][4]

たとえば、ユーザーにアタッチされている BLOB をフェッチするには、必要なフォーム データを使用して **GET** 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

返される JSON (**UserBlobs** オブジェクト) は、次の JSON モデルに準拠しています。

| Attribute | type | 説明 | 例 |
| --- | --- | --- | --- |
| **UserBlobType** | String | ユーザーにアタッチできる BLOB カテゴリ | `Image` と `Video` |
| **UserBlobSubtype** |  String | **UserBlobType** より具体的な BLOB サブカテゴリ | `ProfessionalImage`、`VacationImage`、`CommercialVideo` |

## <a name="common-errors"></a>一般的なエラー

一般的なエラーには、適切なヘッダー情報が含まれていません。

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>次の手順

Azure Digital Twins に関する Swagger の参照ドキュメントについて詳しくは、[Azure Digital Twins Swagger の使用方法](how-to-use-swagger.md)に関する記事をご覧ください。

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG

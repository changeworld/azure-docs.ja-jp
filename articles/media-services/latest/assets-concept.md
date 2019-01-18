---
title: Media Services のアセット - Azure | Microsoft Docs
description: この記事では、アセットとは何かについて説明し、Azure Media Services によるそれらの使用方法についても説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969577"
---
# <a name="assets"></a>アセット

Azure Media Services の[アセット](https://docs.microsoft.com/rest/api/media/assets)には、デジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) と、それらのファイルに関するメタデータが含まれます。 デジタル ファイルは、アセットにアップロードされた後、Media Services エンコード、ストリーム配信、コンテンツ分析ワークフローで使用できます。 詳しくは、後の「[アセットへのデジタル ファイルのアップロード](#upload-digital-files-into-assets)」をご覧ください。

アセットは [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、アセット内のファイルはブロック BLOB としてそのコンテナーに格納されます。 Media Services は、アカウントが汎用 v2 (GPv2) ストレージを使用している場合に、BLOB 層をサポートします。 GPv2 を使用して、[クール ストレージまたはアーカイブ ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)にファイルを移動できます。 **アーカイブ** ストレージは、(エンコード後などに) 不要になったソース ファイルをアーカイブするのに適しています。

**アーカイブ** ストレージ層は、既にエンコードされ、エンコード ジョブの出力が出力 BLOB コンテナーに配置されている非常に大きなソース ファイルの場合のみ推奨されます。 アセットに関連付け、コンテンツのストリーム配信や分析に使用する出力コンテナー内の BLOB は、**ホット**または**クール** ストレージ層に存在する必要があります。

## <a name="asset-definition"></a>アセットの定義

次の表は、アセットのプロパティとその定義を示しています。

|Name|説明|
|---|---|
|id|リソースの完全修飾リソース ID。|
|name|リソースの名前。|
|properties.alternateId |資産の代替 ID。|
|properties.assetId |資産 ID。|
|properties.container |資産の BLOB コンテナーの名前。|
|properties.created |アセットの作成日。<br/> datetime は常に UTC 形式です。|
|properties.description|アセットの説明。|
|properties.lastModified |アセットの最終変更日。 <br/> datetime は常に UTC 形式です。|
|properties.storageAccountName |ストレージ アカウントの名前。|
|properties.storageEncryptionFormat |アセットの暗号化形式。 None または MediaStorageEncryption のいずれか。|
|type|リソースの種類。|

完全な定義については、「[Assets (アセット)](https://docs.microsoft.com/rest/api/media/assets)」をご覧ください。

## <a name="upload-digital-files-into-assets"></a>アセットへのデジタル ファイルのアップロード

Media Services の一般的なワークフローの 1 つに、ファイルのアップロード、エンコード、ストリーム配信があります。 このセクションでは、一般的な手順について説明します。

1. Media Services v3 API を使用して、新しい "入力" アセットを作成します。 この操作により、Media Services アカウントに関連付けられているストレージ アカウントにコンテナーが作成されます。 API からコンテナー名が返されます (例: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。
   
    アセットに関連付ける BLOB コンテナーが既にある場合、アセットを作成するときに、そのコンテナーの名前を指定できます。 Media Services では、現在、ファイル名にパスが含まれていない、コンテナー ルートにある BLOB のみをサポートしています。 そのため、"input.mp4" というファイル名のコンテナーは使用できます。 しかし、"videos/inputs/input.mp4" というファイル名のコンテナーは使用できません。

    Azure CLI を使用して、自分のサブスクリプション内にある、権限を持っている任意のストレージ アカウントおよびコンテナーに直接アップロードできます。 <br/>コンテナー名は一意であり、ストレージの名前付けガイドラインに従っている必要があります。 名前は Media Services アセット コンテナー名 (Asset-GUID) の形式に従う必要はありません。 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. デジタル ファイルをアセット コンテナーにアップロードするために使用する、読み取り/書き込みアクセス許可のある SAS URL を取得します。 Media Services API を使用して、[アセット コンテナーの URL を一覧表示](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)できます。
3. Azure Storage API または SDK (たとえば、[Storage REST API](../../storage/common/storage-rest-api-auth.md)、[JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md)、[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) を使用して、ファイルをアセット コンテナーにアップロードします。 
4. Media Services v3 API を使用して、"入力" アセットを処理する変換とジョブを作成します。 詳しくは、「[Transform と Job](transform-concept.md)」をご覧ください。
5. "出力" アセットからのコンテンツをストリーム配信します。

> [!TIP]
> アセットの作成、ストレージ内のアセット コンテナーに書き込み可能な SAS URL の取得、SAS URL を使用したストレージ内のコンテナーへのファイル アップロードを行う方法を示す .NET の完全な例については、「[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)」をご覧ください。

### <a name="create-a-new-asset"></a>新しいアセットの作成

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

REST の例については、[REST を使用したアセットの作成](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)の例をご覧ください。

この例では、**要求本文**を作成する方法を示しており、説明、コンテナー名、ストレージ アカウントなどの有用な情報を指定できます。

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

完全な例については、「[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)」をご覧ください。 Media Services v3 では、ジョブの入力を HTTPS URL から作成することもできます (「[HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)」をご覧ください)。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

Media Services は、アセットに対して次の OData クエリ オプションをサポートしています。 

* $filter 
* $orderby 
* $top 
* $skiptoken 

演算子の説明:

* Eq = 次の値と等しい
* Ne = 次の値と等しくない
* Ge = 次の値以上
* Le = 次の値以下
* Gt = より大きい
* Lt = より小さい

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションをアセット プロパティに適用する方法をまとめたものです。 

|Name|filter|順序|
|---|---|---|
|id|||
|name|サポート:Eq、Gt、Lt|サポート:昇順および降順|
|properties.alternateId |サポート:Eq||
|properties.assetId |サポート:Eq||
|properties.container |||
|properties.created|サポート:Eq、Gt、Lt| サポート:昇順および降順|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

次の C# 例は、作成日に基づいてフィルター処理を実行しています。

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 1000 です。

> [!TIP]
> 常に次のリンクを使用してコレクションを列挙する必要があります。特定のページ サイズに依存しないでください。

クエリ応答に多数の項目が含まれている場合、サービスは "\@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ページ サイズを構成することはできません。 

コレクションのページング中にアセットが作成または削除された場合、(コレクションの中で、ダウンロードされていない部分に対する変更の場合) その変更は返される結果に反映されます。 

#### <a name="c-example"></a>C# の例

次の C# 例は、アカウント内のすべてのアセットを列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>REST の例

たとえば、$skiptoken が使用されている次の例について考えてみましょう。 必ず、*amstestaccount* を自分のアカウント名に置き換え、*api-version* の値を最新バージョンに設定してください。

次のようにアセットの一覧を要求すると、

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

次のような応答が返されます。

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

その後、次の get 要求を送信して、次のページを要求します。

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

REST の他の例については、「[Assets - List (アセット - リスト)](https://docs.microsoft.com/rest/api/media/assets/list)」をご覧ください。

## <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時の資産を保護するには、ストレージ側の暗号化で資産を暗号化する必要があります。 次の表では、Media Services でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services のストレージの暗号化|AES-256 暗号化、Media Services によって管理されるキー|サポートされています<sup>(1)</sup>|サポートされていません<sup>(2)</sup>|
|[Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー|サポートされています|サポートされています|
|[ストレージ クライアント側暗号化](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー|サポートされていません|サポートされていません|

<sup>1</sup> Media Services は、クリアな、どのような形式でも暗号化されていないコンテンツの処理をサポートしますが、そうすることは推奨されません。

<sup>2</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済み資産では動作しますが、そのような資産を新規作成することはできません。

## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)

[Media Services v2 と v3 の違い](migrate-from-v2-to-v3.md)

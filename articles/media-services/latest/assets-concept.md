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
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f9a6f0963ce8f45da567bb4f6326e9fcc8f435ef
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140134"
---
# <a name="assets"></a>アセット

**アセット** には、デジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) と、それらのファイルに関するメタデータが含まれます。 デジタル ファイルがアセットにアップロードされた後は、Media Services エンコードおよびストリーミング ワークフローで使用できます。

アセットは [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、アセット内のファイルはブロック BLOB としてそのコンテナーに格納されます。 ストレージ SDK クライアントを使用して、コンテナー内の資産ファイルを操作できます。

Azure Media Services は、アカウントが汎用 v2 (GPv2) を使用しているときに、BLOB 層をサポートします。 GPv2 を使用して、クール ストレージまたはコールド ストレージにファイルを移動できます。 コールド ストレージは、不要になったソース ファイルをアーカイブするのに適しています (エンコード後など)。

Media Services v3 では、アセットまたは HTTP(S) URL からジョブの入力を作成できます。 ジョブに対する入力として使用できる資産を作成するには、[ローカル ファイルからのジョブの入力の作成](job-input-from-local-file-how-to.md)に関する記事を参照してください。

「[Media Services のストレージ アカウント](storage-account-concept.md)」と「[Transform と Job](transform-concept.md)」も参照してください。

## <a name="asset-definition"></a>アセットの定義

次の表は、アセットのプロパティとその定義を示しています。

|Name|説明|
|---|---|
|id|リソースの完全修飾リソース ID。|
|name|リソースの名前。|
|properties.alternateId |資産の代替 ID。|
|properties.assetId |資産 ID。|
|properties.container |資産の BLOB コンテナーの名前。|
|properties.created |アセットの作成日。|
|properties.description|アセットの説明。|
|properties.lastModified |資産の最終変更日。|
|properties.storageAccountName |ストレージ アカウントの名前。|
|properties.storageEncryptionFormat |アセットの暗号化形式。 None または MediaStorageEncryption のいずれか。|
|type|リソースの種類。|

完全な定義については、「[Assets](https://docs.microsoft.com/rest/api/media/assets)」(アセット) を参照してください。

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
|name|サポート:Eq、Gt、Lt|サポート: 昇順および降順|
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

次の C# 例は、アカウント内のすべてのアセットを列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、「[Assets - List](https://docs.microsoft.com/rest/api/media/assets/list)」(アセット - リスト) を参照してください。

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

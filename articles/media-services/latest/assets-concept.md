---
title: Azure Media Services の資産 | Microsoft Docs
description: この記事では、資産とは何かについて説明し、Azure Media Services によるそれらの使用方法についても説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 76ed74f2df62d478b83e109a492977ec2d580198
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305091"
---
# <a name="assets"></a>資産

**資産** には、デジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) と、それらのファイルに関するメタデータが含まれます。 デジタル ファイルが資産にアップロードされた後は、Media Services エンコードおよびストリーミング ワークフローで使用できます。

資産は [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、資産内のファイルはブロック BLOB としてそのコンテナーに格納されます。 ストレージ SDK クライアントを使用して、コンテナー内の資産ファイルを操作できます。

Azure Media Services は、アカウントが汎用 v2 (GPv2) を使用しているときに、BLOB 層をサポートします。 GPv2 を使用して、クール ストレージまたはコールド ストレージにファイルを移動できます。 コールド ストレージは、不要になったソース ファイルをアーカイブするのに適しています (エンコード後など)。

Media Services v3 では、資産または HTTP(S) URL からジョブの入力を作成できます。 ジョブに対する入力として使用できる資産を作成するには、[ローカル ファイルからのジョブの入力の作成](job-input-from-local-file-how-to.md)に関する記事を参照してください。

「[Media Services のストレージ アカウント](storage-account-concept.md)」と「[Transform と Job](transform-concept.md)」も参照してください。

## <a name="asset-definition"></a>資産の定義

次の表は、資産のプロパティとその定義を示しています。

|Name|type|[説明]|
|---|---|---|
|ID|文字列|リソースの完全修飾リソース ID。|
|name|文字列|リソースの名前。|
|properties.alternateId |文字列|資産の代替 ID。|
|properties.assetId |文字列|資産 ID。|
|properties.container |文字列|資産の BLOB コンテナーの名前。|
|properties.created |文字列|資産の作成日。|
|properties.description |文字列|資産の説明。|
|properties.lastModified |文字列|資産の最終変更日。|
|properties.storageAccountName |文字列|ストレージ アカウントの名前。|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |資産の暗号化形式。 None または MediaStorageEncryption のいずれか。|
|型|文字列|リソースの種類。|

完全な定義については、「[Assets](https://docs.microsoft.com/rest/api/media/assets)」(資産) を参照してください。

## <a name="filtering-ordering-and-paging-support"></a>フィルター処理、順序付け、ページングのサポート

Media Services は、資産に対して次の OData クエリ オプションをサポートしています。 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションを資産プロパティに適用する方法をまとめたものです。 

|Name|filter|順序|
|---|---|---|
|ID|サポート:<br/>等しい<br/>より大きい<br/>より小さい|サポート:<br/>昇順<br/>降順|
|name|||
|properties.alternateId |サポート:<br/>等しい||
|properties.assetId |サポート:<br/>等しい||
|properties.container |||
|properties.created|サポート:<br/>等しい<br/>より大きい<br/>より小さい|サポート:<br/>昇順<br/>降順|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|型|||

次の C# 例は、作成日に基づいてフィルター処理を実行しています。

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 

クエリ応答に多数の項目が含まれている場合 (現在 1,000 を超えている場合)、サービスは "@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ユーザーはページ サイズを構成できません。 

コレクションのページング中に資産が作成または削除された場合、(コレクションの中で、ダウンロードされていない部分に対する変更の場合) その変更は返される結果に反映されます。 

次の C# 例は、アカウント内のすべての資産を列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、「[Assets - List](https://docs.microsoft.com/rest/api/media/assets/list)」(資産 - リスト) を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルのストリーミング](stream-files-dotnet-quickstart.md)

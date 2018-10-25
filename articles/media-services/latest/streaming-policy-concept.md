---
title: Azure Media Services のストリーミング ポリシー | Microsoft Docs
description: この記事では、ストリーミング ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 4d02ddf50660cd700b2f1c5999ceadfb472b6906
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380757"
---
# <a name="streaming-policies"></a>ストリーミング ポリシー

Azure Media Services v3 では、ストリーミング ポリシーを使用して、StreamingLocator のためのストリーミング プロトコルと暗号化オプションを定義できます。 作成したストリーミング ポリシーの名前を指定するか、定義済みのストリーミング ポリシーのいずれかを指定できます。 現在、利用できる定義済みのストリーミング ポリシーは次のとおりです。'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' および 'Predefined_MultiDrmStreaming'。

> [!IMPORTANT]
> カスタム [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 Media Service アカウントには、StreamingPolicy エントリの数に対するクォータがあります。 StreamingLocator ごとに新しい StreamingPolicy を作成しないでください。

## <a name="streamingpolicy-definition"></a>StreamingPolicy の定義

次の表は、StreamingPolicy のプロパティとそれらの定義を示しています。

|Name|type|説明|
|---|---|---|
|id|string|リソースの完全修飾リソース ID。|
|name|string|リソースの名前。|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs|CommonEncryptionCbcs の構成|
|properties.commonEncryptionCenc|CommonEncryptionCenc|CommonEncryptionCenc の構成|
|properties.created |string|ストリーミング ポリシーの作成時刻|
|properties.defaultContentKeyPolicyName |string|現在のストリーミング ポリシーによって使用される既定の ContentKey|
|properties.envelopeEncryption  |EnvelopeEncryption|EnvelopeEncryption の構成|
|properties.noEncryption|NoEncryption|NoEncryption の構成|
|type|string|リソースの種類。|

完全な定義については、「[Streaming Policies](https://docs.microsoft.com/rest/api/media/streamingpolicies)」(ストリーミング ポリシー) を参照してください。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

Media Services は、ストリーミング ポリシー用の次の OData クエリ オプションをサポートします。 

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

次の表は、これらのオプションを StreamingPolicy プロパティに適用できる方法を示しています。 

|Name|filter|順序|
|---|---|---|
|id|||
|name|eq、ne、ge、le、gt、lt|昇順および降順|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 10 です。

> [!TIP]
> 常に次のリンクを使用してコレクションを列挙する必要があります。特定のページ サイズに依存しないでください。

クエリ応答に多数の項目が含まれている場合、サービスは "\@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ページ サイズを構成することはできません。 

コレクションのページング中に StreamingPolicy が作成または削除された場合、その変更は返される結果に反映されます (変更がまだダウンロードされていないコレクション部分にある場合)。 

次の C# の例は、アカウント内のすべての StreamingPolicy を列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、[ストリーミング ポリシーの一覧](https://docs.microsoft.com/rest/api/media/streamingpolicies/streamingpolicies_list)に関する記事を参照してください

## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)

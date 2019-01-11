---
title: Azure Media Services のストリーミング ロケーター | Microsoft Docs
description: この記事では、ストリーミング ロケーターとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 658843fd5acbe0d4e29947e99c00edf4909fe9f4
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742747"
---
# <a name="streaming-locators"></a>ストリーミング ロケーター

エンコードされたビデオまたはオーディオ ファイルを再生するために使用できる URL をクライアントに提供する必要があり、[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成してストリーミング URL を構築する必要があります。 詳しくは、[ファイルのストリーム処理](stream-files-dotnet-quickstart.md)に関するページをご覧ください。

## <a name="streaminglocator-definition"></a>StreamingLocator の定義

次の表は、StreamingLocator のプロパティとそれらの定義を示しています。

|Name|説明|
|---|---|
|id |リソースの完全修飾リソース ID。|
|name|リソースの名前。|
|properties.alternativeMediaId|このストリーミング ロケーターの別のメディア ID。|
|properties.assetName|アセット名。|
|properties.contentKeys|このストリーミング ロケーターで使用される ContentKey。|
|properties.created|ストリーミング ロケーターの作成時刻。|
|properties.defaultContentKeyPolicyName|このストリーミング ロケーターで使用される既定の ContentKeyPolicy の名前。|
|properties.endTime|ストリーミング ロケーターの終了時刻。|
|properties.startTime|ストリーミング ロケーターの開始時刻。|
|properties.streamingLocatorId|ストリーミング ロケーターの StreamingLocatorId。|
|properties.streamingPolicyName |このストリーミング ロケーターで使用されるストリーミング ポリシーの名前。 作成したストリーミング ポリシーの名前を指定するか、定義済みのストリーミング ポリシーのいずれかを指定します。 利用できる定義済みのストリーミング ポリシーは次のとおりです: 'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' および 'Predefined_MultiDrmStreaming'|
|type|リソースの種類。|

完全な定義については、「[Streaming Locators](https://docs.microsoft.com/rest/api/media/streaminglocators)」(ストリーミング ロケーター) を参照してください。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

Media Services は、ストリーミング ロケーター用の次の OData クエリ オプションをサポートします。 

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

次の表は、これらのオプションを StreamingLocator プロパティに適用できる方法を示しています。 

|Name|filter|順序|
|---|---|---|
|id |||
|name|eq、ne、ge、le、gt、lt|昇順および降順|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 10 です。

> [!TIP]
> 常に次のリンクを使用してコレクションを列挙する必要があります。特定のページ サイズに依存しないでください。

クエリ応答に多数の項目が含まれている場合、サービスは "\@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ページ サイズを構成することはできません。 

コレクションのページング中に StreamingLocator が作成または削除された場合、その変更は返される結果に反映されます (変更がまだダウンロードされていないコレクション部分にある場合)。 

次の C# の例は、アカウント内のすべての StreamingLocator を列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、[ストリーミング ロケーターの一覧](https://docs.microsoft.com/rest/api/media/streaminglocators/list)に関する記事を参照してください

## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)

---
title: Media Services のコンテンツ キー ポリシー - Azure | Microsoft Docs
description: この記事では、コンテンツ キー ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
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
ms.openlocfilehash: 541713677184d93eb78856e3c3373ab432d5f0cf
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141530"
---
# <a name="content-key-policies"></a>コンテンツ キー ポリシー

Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。

Azure Media Services v3 では、コンテンツ キー ポリシーによって、Media Services Key Delivery コンポーネント経由でコンテンツ キーがエンド クライアントに配信される方法を指定できます。 詳細については、「[コンテンツ保護の概要](content-protection-overview.md)」をご覧ください。

## <a name="contentkeypolicies-definition"></a>ContentKeyPolicies 定義

次の表は、ContentKeyPolicy のプロパティとそれらの定義を示しています。

|Name|説明|
|---|---|
|id|リソースの完全修飾リソース ID。|
|name|リソースの名前。|
|properties.created |ポリシーの作成日|
|properties.description |ポリシーの説明。|
|properties.lastModified|ポリシーの最終変更日|
|properties.options |キー ポリシーのオプション。|
|properties.policyId    |従来のポリシー ID。|
|type   |リソースの種類。|

完全な定義については、「[Content Key Policies](https://docs.microsoft.com/rest/api/media/contentkeypolicies)」(コンテンツ キー ポリシー) を参照してください。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

Media Services は、ContentKeyPolicies 用の次の OData クエリ オプションをサポートします。 

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
|properties.created |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.description |eq、ne、ge、le、gt、lt||
|properties.lastModified    |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.options |||
|properties.policyId    |Eq、ne||
|type   |||

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 10 です。

> [!TIP]
> 常に次のリンクを使用してコレクションを列挙する必要があります。特定のページ サイズに依存しないでください。

クエリ応答に多数の項目が含まれている場合、サービスは "\@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ページ サイズを構成することはできません。 

コレクションのページング中に StreamingPolicy が作成または削除された場合、その変更は返される結果に反映されます (変更がまだダウンロードされていないコレクション部分にある場合)。 

次の C# の例は、アカウント内のすべての ContentKeyPolicies を列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、[コンテンツ キー ポリシーの一覧](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

[AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)

[DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)

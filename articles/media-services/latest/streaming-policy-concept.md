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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120236"
---
# <a name="streaming-policies"></a>ストリーミング ポリシー

Azure Media Services v3 では、[ストリーミング ポリシー](https://docs.microsoft.com/rest/api/media/streamingpolicies)を使用して、[ストリーミング ロケーター](streaming-locators-concept.md)のためのストリーミング プロトコルと暗号化オプションを定義できます。 Media Services v3 には、試用または運用環境で直接使用できるように、いくつかの定義済みのストリーミングポリシーが用意されています。 

現在利用できる定義済みのストリーミング ポリシー:<br/>'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' および 'Predefined_MultiDrmStreaming'

特別な要件がある (例: 異なるプロトコルを指定したい、カスタム キー配信サービスを使用する必要がある、クリアなオーディオ トラックを使用する必要がある) 場合は、カスタム ストリーミング ポリシーを作成できます。 

 
> [!IMPORTANT]
> * Datetime 型の**ストリーミング ポリシー**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳細については、「[クォータと制限](limits-quotas-constraints.md)」をご覧ください。

## <a name="decision-tree"></a>デシジョン ツリー

次のデシジョン ツリーを使用して、自分のシナリオに適した定義済みのストリーミング ポリシーを選択できます。

画像をクリックすると、フル サイズで表示されます。  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>例

### <a name="not-encrypted"></a>暗号化なし

平文 (暗号化されていない) でファイルをストリーミングする場合は、定義済みの平文のストリーミング ポリシーを 'Predefined_ClearStreamingOnly' に設定します (.NET では、PredefinedStreamingPolicy.ClearStreamingOnly 列挙型を使用できます)。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>暗号化 

エンベロープと CENC 暗号化を使用してコンテンツを暗号化する必要がある場合は、ポリシーを 'Predefined_MultiDrmCencStreaming' に設定してください。 このポリシーは、2 つのコンテンツ キー (エンベロープおよび CENC) を生成してロケーターに設定する必要があることを示しています。 このため、エンベロープ、PlayReady および Widevine の暗号化が適用されます (キーは構成済みの DRM ライセンスに基づいて再生クライアントに配信されます)。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

また、CBCS (FairPlay) でもストリームを暗号化する場合は、'Predefined_MultiDrmStreaming' を使用します。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
* [AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)

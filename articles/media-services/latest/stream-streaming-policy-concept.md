---
title: Azure Media Services のストリーミング ポリシー
description: この記事では、ストリーミング ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: aa2682dacffc5bbb9b90f1627449858d5078627d
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281543"
---
# <a name="streaming-policies"></a>ストリーミング ポリシー

Azure Media Services v3 では、[ストリーミング ポリシー](/rest/api/media/streamingpolicies)を使用して、[ストリーミング ロケーター](stream-streaming-locators-concept.md)のためのストリーミング プロトコルと暗号化オプションを定義できます。 Media Services v3 には、試用または運用環境で直接使用できるように、いくつかの定義済みのストリーミングポリシーが用意されています。 

現在利用できる定義済みのストリーミング ポリシー:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

次の "デシジョン ツリー" を使用して、自分のシナリオに適した定義済みのストリーミング ポリシーを選択できます。

> [!IMPORTANT]
> * Datetime 型の **ストリーミング ポリシー** のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳しくは、[クォータと制限](limits-quotas-constraints-reference.md)に関するトピックを参照してください。

## <a name="decision-tree"></a>デシジョン ツリー

画像をクリックすると、フル サイズで表示されます。  

[![シナリオに対して定義済みのストリーミング ポリシーを選択できるように設計されたデシジョン ツリーを示す図。](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

コンテンツを暗号化する場合、[コンテンツ キー ポリシー](drm-content-key-policy-concept.md)を作成する必要がありますが、**コンテンツ キー ポリシー** はクリアなストリーミングまたはダウンロードには必要ありません。 

特別な要件がある (例: 異なるプロトコルを指定したい、カスタム キー配信サービスを使用する必要がある、クリアなオーディオ トラックを使用する必要がある) 場合は、カスタム ストリーミング ポリシーを[作成](/rest/api/media/streamingpolicies/create)できます。 

## <a name="get-a-streaming-policy-definition"></a>ストリーミング ポリシー定義を取得する  

ストリーミング ポリシーの定義を表示する場合は、[Get](/rest/api/media/streamingpolicies/get) を使用し、ポリシー名を指定します。 次に例を示します。

### <a name="rest"></a>REST

要求:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

応答:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](filter-order-page-entitites-how-to.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
* [AES-128 動的暗号化とキー配信サービスの使用](drm-playready-license-template-concept.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](drm-protect-with-drm-tutorial.md)

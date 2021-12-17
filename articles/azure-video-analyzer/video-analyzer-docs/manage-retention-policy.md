---
title: Azure Video Analyzer を使用してアイテム保持ポリシーを管理する
description: このトピックでは、Azure Video Analyzer を使用してアイテム保持ポリシーを管理する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0bb6ad0f3b9cb5ddea876969da3b6429e2f2ba32
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849389"
---
# <a name="manage-retention-policy-with-video-analyzer"></a>Video Analyzer を使用してアイテム保持ポリシーを管理する

Azure Video Analyzer を使用すると、1 つのソースの数秒から数年分の長期間に及ぶビデオ コンテンツからのコンテンツをアーカイブできます。 コンテンツのアイテム保持ポリシーを明示的に制御できます。これにより、古いコンテンツが定期的にトリミングされます。 さまざまなアーカイブにさまざまなポリシーを適用できます。たとえば、駐車場のカメラからの最新の 3 日間の録画と、レジ カウンターの背後にあるカメラからの最新の 30 日間の録画を保持できます。

## <a name="retention-period"></a>保持期間

Azure Video Analyzer アカウントの[ビデオ リソース](terminology.md#video)ごとに、必要に応じてアイテム保持ポリシーを適用できます。 保持期間を指定すると (以下で説明)、サービスは、その期間より古いコンテンツを定期的にトリミングします。 ポリシーを指定しない場合、コンテンツは無期限に格納されます。

通常、保持期間は、パイプライン トポロジの作成時にビデオ シンク ノードのプロパティで設定されます。 `retentionPeriod` プロパティを `videoCreationProperties` セクションで見つけます。 このプロパティは、名前が示すとおり、このトポロジを使用してライブ パイプラインがアクティブ化され、新しいビデオ リソースが作成されるときに使用されます。 ビデオ リソースが既に存在する場合 (パイプラインが再アクティブ化された場合など)、これらの作成プロパティは使用されません。 すべての駐車場カメラに 3 日間のアイテム保持ポリシーを適用する方法の例を次に示します。

```
{
  "@type": "#Microsoft.VideoAnalyzer.VideoSink",
  "name": "{nodeName}",         
  "videoName": "{nameForVideoResource}",
  "videoCreationProperties":
  {
    "title": "{titleForVideo}",
    "description": "{descriptionForVideo}",
    "segmentLength": "PT30S",
    "retentionPeriod": "P3D"
  },
}
```

また、Azure portal を使用するか、[REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json) を使用して、ビデオ リソースの `retentionPeriod` プロパティを設定または更新できます。 3 日間のアイテム保持ポリシーを設定する例を次に示します。

```
"archival":
{
   "retentionPeriod": "P3D",
},
```

## <a name="rules-and-limitations"></a>規則と制約

* アイテム保持ポリシーが有効になるまでに、最大 24 時間かかる場合があります
* 形式: `retentionPeriod` プロパティは、ISO 8601 の期間の値を受け取ります。この値は、日数の倍数である必要があります。 使用できる値の例: P1D、P20D、P1M、P365D、P1Y、P5Y
    * 最小値は P1D、最大値は P5Y
    * null 値に設定すると、アイテム保持ポリシーがクリアされ、ビデオ コンテンツは無期限に格納されます
* アイテム保持ポリシーを使用すると、サービスが BLOB を検索して削除するときに、追加の Azure Storage トランザクション コストが発生します

## <a name="next-steps"></a>次のステップ

[記録の再生](playback-recordings-how-to.md)

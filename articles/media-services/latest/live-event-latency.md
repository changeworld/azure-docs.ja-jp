---
title: Azure Media Services 内の LiveEvent の待機時間 | Microsoft Docs
description: このトピックでは、LiveEvent の待機時間の概要を説明し、低待機時間を設定する方法を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: b167d3424d520cf8be515eec9d495164dd9785ab
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682097"
---
# <a name="liveevent-latency-in-media-services"></a>Media Services 内の LiveEvent の待機時間

この記事では、**LiveEvent** に対して低待機時間を設定する方法を示します。 さらに、さまざまなプレーヤーで低待機時間の設定を使用したときの一般的な結果について説明します。 結果は、CDN およびネットワーク待機時間によって異なります。 

新しい **LowLatency** 機能を使用するには、**LiveEvent** の **StreamOptionsFlag** を **LowLatency** に設定します。 ストリームが稼働状態になったら、[Azure Media Player](http://ampdemo.azureedge.net/) (AMP) のデモ ページを使用して、"Low Latency Heuristic Profile" を使用するように再生オプションを設定できます。

次の .NET の例では、**LiveEvent** に対して **LowLatency** を設定する方法を示しています。

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

完全な例については次を参照してください:[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)。

## <a name="liveevents-latency"></a>LiveEvent の待機時間

次の表に、Media Services 内の待機時間の一般的な結果を示します (LowLatency フラグが有効になっている場合)。コントリビューション フィードがサービスに到達した時間から、ビューアーがプレーヤーで再生を見た時間までが測定されています。 低待機時間を最適に使用するには、エンコーダーの設定を "画像グループ" (GOP) の長さが 1 秒になるまで調整する必要があります。 高い GOP の長さを使用するときは、帯域幅の消費を最小限に抑えて、同じフレーム レートでのビットレートを減らします。 モーションの少ない動画で特に有効です。

### <a name="pass-through"></a>パススルー 

||2 秒 GOP の低待機時間が有効|1 秒 GOP の低待機時間が有効|
|---|---|---|
|AMP での DASH|10 秒|8 秒|
|ネイティブ iOS プレーヤーでの HLS|14 秒|10 秒|

### <a name="live-encoding"></a>ライブ エンコード

||2 秒 GOP の低待機時間が有効|1 秒 GOP の低待機時間が有効|
|---|---|---|
|AMP での DASH|14 秒|10 秒|
|ネイティブ iOS プレーヤーでの HLS|18 秒|13 秒|

> [!NOTE]
> エンドツーエンドの待機時間は、ローカル ネットワークの状態や、CDN キャッシュ レイヤーの導入によって変動する可能性があります。 正しい構成でテストする必要があります。

## <a name="next-steps"></a>次の手順

- [ライブ ストリーミングの概要](live-streaming-overview.md)
- [ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)


---
title: プレビュー イメージを有効にする
description: この記事では、Azure Video Analyzer を使用してビデオを記録するときにプレビュー イメージを有効にし、アクセスする方法について説明します
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: dd78a3753f61fe5615f15a41d1b1493556d0dafc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559430"
---
# <a name="enable-preview-images-when-recording-video"></a>ビデオを記録するときにプレビュー イメージを有効にする

Azure Video Analyzer を使用して、RTSP カメラから[ビデオをキャプチャして記録](../video-recording.md)することができます。 この[クイックスタート](detect-motion-record-video-clips-cloud.md)またはこの[チュートリアル](use-continuous-video-recording.md)で示すように、ビデオ シンク ノードを含むパイプライン トポロジを作成します。 

Video Analyzer エッジ モジュールを使用してビデオを記録する場合、ビデオ シンク ノードを有効にすると、さまざまなサイズのプレビュー イメージのセットを定期的に生成できます。 これらのイメージは、Video Analyzer アカウントの[ビデオ リソース](../terminology.md#video)から取得できます。 たとえば、解像度が 1920 x 1080 のビデオがカメラで生成された場合、プレビュー イメージのサイズは次のようになります。

  * 320 x 180: 小
  * 640 x 360: 中
  * 1280 x 720: 大

> [!NOTE]
> プレビュー イメージでは、カメラからのビデオの縦横比が維持されます。

プレビュー イメージは、[`segmentLength`](../playback-recordings-how-to.md#recording-and-playback-latencies) によって決定される頻度で定期的に生成されます。 [イベントベースの記録](record-event-based-live-video.md)を使用している場合は、ライブ パイプラインがアクティブでビデオが記録されているときにのみイメージが生成されることに注意する必要があります。 一連のプレビュー イメージが生成されるたびに、前のセットが上書きされます。

> [!NOTE]
> この機能は現在、Video Analyzer エッジ モジュールでのみ使用できます。 さらに、これを有効にすると、イメージを作成したり表示したりするために頻繁なトランザクションによって発生する Azure Storage のコストと、イメージのサイズが影響を受けます。

## <a name="enable-preview-images-in-the-video-sink-node"></a>ビデオ シンク ノードでプレビュー イメージを有効にする
プレビュー イメージを有効にするには、パイプライン トポロジのビデオ シンク ノードで適切なフラグを設定する必要があります。 **videoPublishingOptions** で **enableVideoPreviewImage** を **true** に設定します  

例:
```
        "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "name": "videoSink",
          "videoName": "{$parameter-for-specifying-unique-videoName-for-each-pipeline}",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "videoPublishingOptions": {
            "enableVideoPreviewImage": true
          },
          "videoCreationProperties": {
            "title": "{$parameter-for-specifying-unique-title-for-each-pipeline}",
            "description": "{$parameter-for-specifying-unique-description-for-each-pipeline}k",
            "segmentLength": "PT30S"
          },
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048"
        }
      ]
``` 

## <a name="accessing-preview-images"></a>プレビュー イメージへのアクセス

使用可能なプレビュー イメージへの静的 URL を取得するには、[認可されたベアラー トークン](../playback-recordings-how-to.md#accessing-videos)を使用して、ビデオ リソースに対して GET 要求を呼び出す必要があります。 次に示すように、応答の **contentUrls** の下に URL が一覧表示されます。

```
      "contentUrls": {
        ...
        "previewImageUrls": {
          "small": "XXXX",
          "medium": "XXXX",
          "large": "XXXX"
         }
       },
    
```

## <a name="next-steps"></a>次のステップ

この[クイックスタート](detect-motion-record-video-clips-cloud.md)またはこの[チュートリアル](use-continuous-video-recording.md)では、トポロジでビデオ プレビュー イメージを有効にすることを試してください。 

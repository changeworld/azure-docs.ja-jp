---
title: Azure Media Services のライブ文字起こし | Microsoft Docs
description: この記事では、Azure Media Services のライブ文字起こしとはどのようなものであるかを説明します。
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
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: a85f9f8b9d98f77cf673778f031d8f47f132fbe1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327347"
---
# <a name="live-transcription-preview"></a>ライブ文字起こし (プレビュー)

Azure Media Services は、さまざまなプロトコルでビデオやオーディオを配信し、現在ではテキストも配信します。 MPEG-DASH や HLS/CMAF を使用してライブ ストリームを発行し、その後、ビデオやオーディオと一緒に発行するときには、Microsoft のサービスにより、IMSC 1.1 互換 TTML で文字起こしされ、MPEG-4 Part 30 (ISO/IEC 14496-30) フラグメントにパッケージ化されたテキストが配信されます。 HLS/TS による配信を使用する場合、テキストはチャンク VTT として配信されます。 

この記事では、Azure Media Services v3 でライブ イベントをストリーミングするときにライブ文字起こしを有効にする方法について説明します。 続行する前に、Media Services v3 REST API の使用に慣れておいてください (詳細については、[このチュートリアル](stream-files-tutorial-with-rest.md)を参照してください)。 また、[ライブ ストリーミング](live-streaming-overview.md)の概念について理解している必要があります。 [Media Services によるライブ ストリーミング](stream-live-tutorial-with-api.md)のチュートリアルを完了することをお勧めします。 

> [!NOTE]
> 現在、ライブ文字起こしは、米国西部 2 リージョンのプレビュー機能としてのみ提供されています。 英語音声の文字起こしがサポートされています。 この機能の API リファレンスは、機能がプレビュー段階にあるためこのドキュメントに記載されています。Microsoft の REST ドキュメントでは詳細は提供されていません。 

## <a name="creating-the-live-event"></a>ライブ イベントの作成 

ライブ イベントを作成するには、次のように、PUT 操作を 2019-05-01 バージョンに送信します。 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

この操作には次の本文があります (取り込みプロトコルとしての RTMP によってパススルー ライブ イベントが作成されます)。 transcriptions プロパティが追加されていることに注目してください。 language に対して指定できる値は en-us のみです。 

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

ライブ イベントの状態が "Running" 状態になるまで、状態をポーリングする必要があります。この状態は、RTMP のコントリビューション フィードを送信できるようになったことを示します。 これで、プレビュー フィードの確認やライブ出力の作成など、このチュートリアルと同じ手順に従えるようになりました。 

## <a name="delivery-and-playback"></a>配信と再生 

このサービスが動的パッケージングを使用して、どのようにさまざまなプロトコルでビデオやオーディオを配信し、現在ではテキストも配信しているか、[動的パッケージングの概要](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)に関する記事で確認してください。 MPEG-DASH や HLS/CMAF を使用してライブ ストリームを発行し、その後、ビデオやオーディオと一緒に発行するときには、Microsoft のサービスにより、IMSC 1.1 互換 TTML で文字起こしされ、MPEG-4 Part 30 (ISO/IEC 14496-30) フラグメントにパッケージ化されたテキストが配信されます。 HLS/TS による配信を使用する場合、テキストはチャンク VTT として配信されます。 [Azure Media Player](use-azure-media-player.md) などの Web プレーヤーを使用してストリームを再生できます。  

> [!NOTE]
>  Azure Media Player を使用する場合は、バージョン 2.3.3 以降を使用します。

## <a name="known-issues"></a>既知の問題 

以下に、プレビューでのライブ文字起こしに関する既知の問題を示します 

* この機能は、米国西部 2 でのみ使用できます。
* アプリケーションは、[Media Services v3 OpenAPI の仕様](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)で説明されているプレビュー API を使用する必要があります。
* サポートされている言語は英語のみです。
* コンテンツ保護については、AES エンベロープ暗号化のみがサポートされています。

## <a name="next-steps"></a>次の手順

[Media Services の概要](media-services-overview.md)

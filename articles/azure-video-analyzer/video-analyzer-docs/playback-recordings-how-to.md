---
title: ビデオ記録をする - Azure Video Analyzer
description: Azure Video Analyzer を使用して、クラウドにビデオを数週間から数か月にわたって録画する継続的なビデオ記録を行うことができます。 また、イベントベースの録画を使用して、記録を必要なクリップに制限することもできます。 この記事では、そのような記録を再生する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 415edea10eaf635b88786d33bbd439bc386a57fc
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602190"
---
# <a name="playback-of-video-recordings"></a>ビデオ記録を再生する 

## <a name="pre-read"></a>先にお読みください  

* [継続的なビデオ記録](continuous-video-recording.md)
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)

## <a name="background"></a>バックグラウンド  

Azure Video Analyzer を使用して、クラウドにビデオを数週間から数か月にわたって録画する[継続的なビデオ記録](continuous-video-recording.md) (CVR) を行うことができます。 また、[イベントベースのビデオ記録](event-based-video-recording-concept.md) (EVR) を使用して、記録を必要なクリップに制限することもできます。 どちらの場合も、[パイプライン](pipeline.md)が AI を使用して推論結果を生成する場合、[ビデオと共にこれらの結果を記録する](record-stream-inference-data-with-video.md)必要があります。 

Video Analyzer の機能を評価する場合は、[CVR のチュートリアル](use-continuous-video-recording.md)、または Azure Portal を使用して録画を再生する[推論メタデータを使用したビデオの再生に関するチュートリアル](record-stream-inference-data-with-video.md)を参照してください。

Video Analyzer API を使用してアプリケーションまたはサービスを構築している場合は、記録を再生する方法を理解するために、[アクセス ポリシー](access-policies.md)と [Video Analyzer プレーヤー ウィジェット](player-widget.md)の記事を確認するとともに、次の説明もお読みください。

<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="determining-that-a-video-recording-is-ready-for-playback"></a>ビデオ記録の再生準備ができていることを確認する

お使いの Video Analyzer アカウントを Azure Storage アカウントにリンクして、ビデオをクラウドに録画すると、コンテンツは[ビデオ リソース](terminology.md#video)に書き込まれます。 録画完了後または録画中に[コンテンツのストリーミング](terminology.md#streaming)ができます。 これは、ビデオ リソースの `true` に対して設定される `canStream` [フラグ](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-05-01-preview/Videos.json)によって示されます。 

## <a name="video-analyzer-player-widget"></a>Video Analyzer プレーヤー ウィジェット
Video Analyzer には、HLS または MPEG-DASH プロトコルを介して、ストリームを再生デバイス (クライアント) に配信するために必要な機能が用意されています。 Video Analyzer プレーヤー ウィジェットを使用して、ストリーミング URL と再生認証トークンを取得し、これらをクライアント アプリで使用してビデオおよび推論メタデータを再生します。

Video Analyzer プレーヤー ウィジェットをインストールすると、ビデオ記録を再生できます。 ウィジェットは `npm` または `yarn` を使用してインストールできます。これにより、独自のクライアント側アプリケーションにウィジェットを含めることができます。 次のいずれかのコマンドを実行して、独自のアプリケーションにウィジェットを追加します。

NPM:
```
npm install –-save @azure/video-analyzer-widgets
```
YARN:
```
yarn add @azure/video-analyzer-widgets 
```
また、次の例を使用して、ビルド前の場所を参照する script 要素に type="module" を追加することで、既存のビルド前スクリプトを埋め込むこともできます。

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="recording-and-playback-latencies"></a>記録と再生の待機時間

Video Analyzer を使用してビデオ リソースに記録する場合は、[`segmentLength` プロパティ](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json)を指定して、クラウドに書き込まれる前にビデオの最小時間 (秒単位) を集計するようにモジュールに指示します。 たとえば、`segmentLength` を 300 に設定すると、モジュールは 5 分間の長さのビデオを累積してから 5 分間の "チャンク" を 1 つアップロードします。その後、次の 5 分間の累積モードに入り、またアップロードします。 `segmentLength` を長くすると、読み取りと書き込みの回数が `segmentLength` 秒ごとに 1 回とした場合の回数よりも少なくなるので、Azure Storage のトランザクション コストを削減できるという利点があります。

そのため、Video Analyzer アカウントからのビデオのストリーミングは、少なくともその時間分遅くなります。 

再生の待機時間 (カメラの前でイベントが発生した時刻から、再生デバイスで表示できる時刻までの遅延) を決定するもう 1 つの要因が、[GOP](https://en.wikipedia.org/wiki/Group_of_pictures) (Group Of Pictures) 時間です。 [3 つの簡単な方法を使用してライブ ストリームの遅延を減らす](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)ことで、GOP 時間が長くなるほど、待機時間も長くなります。 一般的に、監視やセキュリティのシナリオで使用される IP カメラは、30 秒よりも長い GOP を使用するように構成されています。 これは、全体の待機時間に大きな影響を与えます。

## <a name="next-steps"></a>次のステップ

[継続的なビデオ記録チュートリアル](use-continuous-video-recording.md)

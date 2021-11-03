---
title: ビデオの表示 - Azure Video Analyzer
description: Azure Video Analyzer を使用して、クラウドにビデオを数週間から数か月にわたって録画する継続的なビデオ記録を行うことができます。 また、イベントベースの録画を使用して、記録を必要なクリップに制限することもできます。 さらに、Video Analyzer サービスを使用してカメラからビデオをキャプチャする場合は、キャプチャ中にビデオをストリーミングできます。 この記事では、このようなビデオを表示する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b98d68024857322c591ee2dc77af22ebb1492183
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048573"
---
# <a name="viewing-of-videos"></a>ビデオの表示

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事

* [Video Analyzer ビデオ リソース](terminology.md#video)
* [継続的なビデオ記録](continuous-video-recording.md)
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)

## <a name="background"></a>バックグラウンド  

Azure Video Analyzer アカウントで[ビデオ リソース](terminology.md#video)を作成するには、RTSP カメラから記録するか、そのような記録の一部をエクスポートします。 Video Analyzer API を使用して [VMS](terminology.md#vms) をビルドしている場合、この記事は、ビデオを表示する方法を理解するのに役立ちます。 この記事を読んだ後、[アクセス ポリシー](access-policies.md)と[Video Analyzer プレーヤー ウィジェット](player-widget.md)に関する記事を確認する必要があります。 

Video Analyzer の機能を評価する場合は、[クイックスタート](edge/detect-motion-record-video-clips-cloud.md)または[チュートリアル](edge/use-continuous-video-recording.md)のいずれかを使用し、Azure portal を使用してビデオを表示できます。
<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="creating-videos"></a>ビデオの作成

Video Analyzer エッジ モジュールを使用してビデオを作成するには、次のような方法があります。

* 数週間または数か月以上、RTSP カメラから[継続的に](continuous-video-recording.md)記録します (CVR)。
* [イベントベースのビデオ記録](event-based-video-recording-concept.md) (EVR) を使用して、必要な部分のみ記録します。 
 
また、Video Analyzer サービスにより、CVR を使用してビデオを作成することもできます。 このサービスを使用して、ビデオ記録の一部をエクスポートしてビデオを作成することもできます。このようなビデオには、ダウンロード可能なファイル (MP4 ファイル形式) が含まれます。

## <a name="accessing-videos"></a>ビデオへのアクセス

ARM API [`Videos`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json) をクエリして、 ビデオ リソースのプロパティを表示できます。 また、Video Analyzer では、エンドユーザーが ARM を介さずにビデオを参照して表示できるアプリケーションをビルドすることもできます。 [パブリック エンドポイント](access-public-endpoints-networking.md)の記事に示されているように、ビデオのプロパティをクエリできる、いわゆるクライアント API にアクセスできます。 これらの API は、Azure portal の Video Analyzer ブレードの概要セクションにあるクライアント API エンドポイント、または ARM API [`VideoAnalyzers`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/VideoAnalyzers.json) を介して公開されます。 [アクセス ポリシー](access-policies.md)を使用してこのエンドポイントへのアクセスを制御できます。[Video Analyzer プレーヤー ウィジェットの記事](player-widget.md)にその方法が示されています。

## <a name="determining-that-a-video-recording-is-ready-for-viewing"></a>ビデオ記録の表示準備ができていることを確認する

ビデオ リソースが RTSP カメラからの記録を表している場合は、記録が完了した後、または記録の進行中に、[そのコンテンツをストリーミング](terminology.md#streaming)できます。 これは、ビデオ リソースの `true` に対して設定される `canStream` フラグによって示されます。 このようなビデオでは `type` が `archive` に設定され、再生またはストリーミングの URL が `archiveBaseUrl` で返されることに注意してください。 

ビデオ記録の一部を MP4 ファイルにエクスポートすると、結果のビデオ リソースの `type` が `file` に設定され、ビデオのエクスポート ジョブが完了すると、再生またはダウンロードできるようになります。 このようなファイルを再生またはダウンロードするための URL は `downloadUrl` で返されます。
   > [!NOTE]
   > 上記の URL には[ベアラー トークン](./access-policies.md#creating-a-token)が必要です。 詳細については、[Video Analyzer プレーヤー ウィジェット](player-widget.md)のドキュメントを参照してください。

## <a name="recording-and-playback-latencies"></a>記録と再生の待機時間

Video Analyzer エッジ モジュールを使用してビデオ リソースに記録する場合は、パイプライン トポロジで [`segmentLength` プロパティ](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json)を指定して、クラウドに書き込まれる前にビデオの最小時間 (秒単位) を集計するようにモジュールに指示します。 たとえば、`segmentLength` を 300 に設定すると、モジュールは 5 分間の長さのビデオを累積してから 5 分間の "チャンク" を 1 つアップロードします。その後、次の 5 分間の累積モードに入り、またアップロードします。 `segmentLength` を長くすると、読み取りと書き込みの回数が `segmentLength` 秒ごとに 1 回とした場合の回数よりも少なくなるので、Azure Storage のトランザクション コストを削減できるという利点があります。 Video Analyzer サービスを使用している場合、パイプライン トポロジには同じ [`segmentLength` プロパティ](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/PipelineTopologies.json)が含まれています。

そのため、Video Analyzer アカウントからのビデオのストリーミングは、少なくともその時間分遅くなります。 

エンドツーエンドの待機時間 (カメラの前でイベントが発生した時刻から、再生デバイスで表示される時刻までの遅延) を決定するもう 1 つの要因が、[GOP](https://en.wikipedia.org/wiki/Group_of_pictures) (Group Of Pictures) 時間です。 [3 つの簡単な方法を使用してライブ ストリームの遅延を減らす](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)ことで、GOP 時間が長くなるほど、待機時間も長くなります。 一般的に、監視やセキュリティのシナリオで使用される IP カメラは、30 秒よりも長い GOP を使用するように構成されています。 これは、全体の待機時間に大きな影響を与えます。

## <a name="low-latency-streaming"></a>低待機時間ストリーミング

Video Analyzer サービスを使用して RTSP カメラからビデオをキャプチャおよび記録する場合、[低待機時間ストリーミング](terminology.md#low-latency-streaming)を使用して、約 2 秒の待機時間でビデオを表示できます。 このサービスにより、[Video Analyzer プレーヤー ウィジェット](player-widget.md)などの RTSP 対応プレーヤーが [RTSP プロトコル](https://datatracker.ietf.org/doc/html/rfc7826.html)を使用してビデオを受信できる WebSocket トンネルを利用できるようになります。 全体の待機時間は、カメラとクラウドの間、およびクラウドと再生デバイスの間のネットワーク帯域幅、さらに再生デバイスの処理能力に左右されることに注意してください。 低待機時間ストリーミングの URL は `rtspTunnelUrl` で返されます。

   > [!NOTE]
   > 上記の URL には[ベアラー トークン](./access-policies.md#creating-a-token)が必要です。 詳細については、[Video Analyzer プレーヤー ウィジェット](player-widget.md)のドキュメントを参照してください。

## <a name="video-analyzer-player-widget"></a>Video Analyzer プレーヤー ウィジェット
Video Analyzer には、HLS、MPEG-DASH、または RTSP プロトコルを介して、ストリームを再生デバイス (クライアント) に配信するために必要な機能が用意されています。 [Video Analyzer プレーヤー ウィジェット](player-widget.md)を使用して、関連 URL とコンテンツ認証トークンを取得し、これらをクライアント アプリで使用してビデオおよび推論メタデータを再生します。

Video Analyzer プレーヤー ウィジェットをインストールすると、ビデオを表示できます。 ウィジェットは `npm` または `yarn` を使用してインストールできます。これにより、独自のクライアント側アプリケーションにウィジェットを含めることができます。 次のいずれかのコマンドを実行して、独自のアプリケーションにウィジェットを追加します。

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

## <a name="viewing-video-with-inference-results"></a>推論結果を含むビデオの表示
Video Analyzer エッジ モジュールを使用してビデオを記録するときに、[パイプライン](pipeline.md)が AI を使用して推論結果を生成している場合は、これらの結果をビデオと一緒に記録できます。 ビデオを表示するときに、Video Analyzer プレーヤー ウィジェットでビデオに結果を重ねて表示することができます。 詳細については、[このチュートリアル](edge/record-stream-inference-data-with-video.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [アクセス ポリシーについて](access-policies.md)
* [Video Analyzer プレーヤー ウィジェットの使用](player-widget.md)
* [エッジでの継続的なビデオ記録](edge/use-continuous-video-recording.md)
* [クラウドでの継続的なビデオ記録](cloud/get-started-livepipelines-portal.md)

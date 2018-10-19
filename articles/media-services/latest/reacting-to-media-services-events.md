---
title: Azure Media Services イベントへの対応 | Microsoft Docs
description: Media Services イベントをサブスクライブするには、Azure Event Grid を使います。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/19/2018
ms.author: juliako
ms.openlocfilehash: 143fec2ddb168b0fff0e419fa5767e9718637241
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465539"
---
# <a name="reacting-to-media-services-events"></a>Media Services イベントへの対応

Media Services イベントは、アプリケーションがモダンなサーバーレス アーキテクチャを使用してさまざまなイベント (ジョブ状態変更イベントなど) に対応できるようにします。 複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。 代わりに、イベントが [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通してイベント ハンドラー ([Azure Functions](https://azure.microsoft.com/services/functions/) や [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) の他にユーザー独自の Webhook も可) にプッシュされるため、支払いは使用した分だけで済みます。 価格の詳細については、[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)に関する記事をご覧ください。

Media Services イベントの可用性は、Event Grid の[可用性](../../event-grid/overview.md)に関連付けられており、Event Grid の場合と同様に、他のリージョンで使用可能になります。  

## <a name="available-media-services-events"></a>利用可能な Media Services イベント

Event Grid は、[イベント サブスクリプション](../../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。  現時点では、Media Services イベントのサブスクリプションには、次のイベントを含めることができます。  

|イベント名|説明|
|----------|-----------|
| Microsoft.Media.JobStateChange| ジョブの状態が変更されたときに発生します。 |
| Microsoft.Media.LiveEventConnectionRejected | エンコーダーの接続試行が拒否されました。 |
| Microsoft.Media.LiveEventEncoderConnected | エンコーダーがライブ イベントとの接続を確立しました。 |
| Microsoft.Media.LiveEventEncoderDisconnected | エンコーダーが切断されました。 |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | データ チャンクが遅すぎるか、タイムスタンプが重複している (新しいデータ チャンクのタイムスタンプが前のデータ チャンクの終了時間よりも古い) ため、メディア サーバーがデータ チャンクをドロップしました。 |
| Microsoft.Media.LiveEventIncomingStreamReceived | メディア サーバーが、ストリームまたは接続において、各トラックの最初のデータ チャンクを受信しました。 |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | オーディオ ストリームとビデオ ストリームの同期がずれていることをメディア サーバーが検出しました。ユーザー エクスペリエンスへの影響はないと考えられるため、警告として使用してください。 |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 外部エンコーダーから受信している 2 つのビデオ ストリームの同期がずれていることをメディア サーバーが検出しました。ユーザー エクスペリエンスへの影響はないと考えられるため、警告として使用してください。 |
| Microsoft.Media.LiveEventIngestHeartbeat | ライブ イベントの実行中、各トラックについて 20 秒ごとに発行されます。 取り込みの正常性についての概要が得られます。 |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | メディア サーバーが受信中のトラックの途切れを検出しました。 |

## <a name="event-schema"></a>イベント スキーマ

Media Services イベントには、データの変更に対応するために必要なすべての情報が含まれます。  Media Services イベントは、eventType プロパティが "Microsoft.Media" で始まっていることで識別できます。

詳細については、「[Media Services イベントのスキーマ](media-services-event-schemas.md)」を参照してください。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法

Media Services イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションが構成されている可能性があるので、イベントが特定のソースからのものであると思い込まず、メッセージのトピックを調べて、想定しているストレージ アカウントからのものであることを確認することが重要です。
* 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
* わからないフィールドは無視します。  この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
* イベントを特定のイベントに制限するには、"subject" プレフィックスとサフィックスの一致を使用します。

## <a name="next-steps"></a>次の手順

[ジョブ状態イベントを取得する](job-state-events-cli-how-to.md)

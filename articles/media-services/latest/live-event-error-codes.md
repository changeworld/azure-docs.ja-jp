---
title: Azure Media Services ライブ イベントのエラーコード | Microsoft Docs
description: このアーティクルでは、ライブ イベントのエラーコードをリストにします。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654581"
---
# <a name="media-services-live-event-error-codes"></a>Media Services ライブ イベント エラー コード

このセクションの表では、[ライブ イベント](live-events-outputs-concept.md)のエラーコードを一覧に示します。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

ライブイベントの [Event Grid](https://docs.microsoft.com/azure/event-grid/) イベントにサブスクライブすると、[LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) イベントの次のいずれかのエラーが表示されることがあります。

| 結果コード | 説明 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 取り込み URL が正しくありません。 |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 構成されている IP 許可リストにエンコーダー IP が存在しません。 |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP エンコーダーが setDataFrame コマンドを送信しませんでした。 |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 指定されたコーデックはサポートされていません。 |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |実際のメディア データが配信される前に、メディアの説明情報が受信されませんでした。|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |オーディオまたはビデオの種類に対する品質の数が、許容される上限を超えました。|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |ライブ イベントまたはチャンネル サービスの受信ビットレートの合計が、許容される上限を超えました。|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP エンコーダーからのビデオまたはオーディオ FLVTag のタイムスタンプが無効です。 |
| MPE_INGEST_FRAMERATE_EXCEEDED | 受信エンコーダーが、ライブ イベント/チャンネルをエンコードするために許容される上限の 30fps を超えるフレームレートでストリームを取り込みました。|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | 受信エンコーダーが、次の許容される解像度を超えるストリームを取り込みました。ライブ イベント/チャンネルのエンコード: 1920x1088、パススルー ライブ イベント/チャンネル: 4096 x 2160|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

[LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) イベントの次のいずれかのエラーが表示される場合があります。

|結果コード|説明|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|RTMP セッションは、アイドル状態のまま許容制限時間を超えてタイムアウトしました。|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|RTMP エンコーダーからのビデオまたはオーディオ FLVTag のタイムスタンプが無効です。|
|MPE_CAPACITY_LIMIT_REACHED|エンコーダーからデータが送信される速度が速すぎます。|
|不明なエラー コード|これらのエラー コードは、メモリ エラーから、ハッシュ マップにおけるエントリの重複まで多岐にわたります。|


## <a name="see-also"></a>関連項目

[ストリーミング エンドポイント (配信元) エラー コード](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>次のステップ

[チュートリアル:Media Services を使用したライブ ストリーム配信](stream-live-tutorial-with-api.md)

---
title: Azure Media Services ライブ イベントのエラー コード
description: このアーティクルでは、ライブ イベントのエラーコードをリストにします。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 09859a953b0127733cbf1b0876c1d2ffa6082096
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279478"
---
# <a name="media-services-live-event-error-codes"></a>Media Services ライブ イベント エラー コード

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

次の表に、[ライブ イベント](live-event-outputs-concept.md)のエラー コードを示します。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

ライブ イベントの [Event Grid](../../event-grid/index.yml) イベントをサブスクライブすると、[LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) イベントから次のいずれかのエラーが表示されることがあります。
> [!div class="mx-tdCol2BreakAll"]
>| エラー | Information |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|[説明] | 取り込み URL が正しくありません。 |
>|推奨されている解決方法| APPID は、RTMP 取り込み URL 内の GUID トークンです。 それが API からの取り込み URL と一致することを確認します。 |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| [説明] |構成されている IP 許可リストにエンコーダー IP が存在しません |
>| 推奨されている解決方法| エンコーダーの IP が IP 許可リストに含まれていることを確認します。 *whoismyip* や *CIDR calculator* などのオンライン ツールを使用して、適切な値を設定します。  実際のライブ イベントの前に、エンコーダーがサーバーに到達できることを確認します。 |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| [説明]|RTMP エンコーダーによって `setDataFrame` コマンドが送信されませんでした。 |
>| 推奨されている解決方法|ほとんどの商用エンコーダーでは、ストリーム メタデータが送信されます。 シングル ビットレートの取り込みがプッシュされるエンコーダーの場合、これは問題でない可能性があります。 LiveEvent では、ストリーム メタデータがない場合、受信ビットレートを計算できます。  PassThru チャネルまたはダブル プッシュ シナリオでのマルチビットレートの取り込みでは、取り込み URL 内のクエリ文字列に "videodatarate" と "audiodatarate" を追加してみることができます。 近似値で機能する場合があります。 単位は Kbit です。 たとえば、`rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` のように入力します。 |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| [説明]|指定されたコーデックはサポートされていません。|
>| 推奨されている解決方法| LiveEvent で、サポートされていないコーデックが受信されました。 たとえば、RTMP の取り込みで、非 AVC ビデオ コーデックが LiveEvent によって受信されました。  エンコーダーのプリセットを確認してください。 |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| [説明] |実際のメディア データが配信される前に、メディアの説明情報が受信されませんでした。 |
>| 推奨されている解決方法|エンコーダーからのストリームの説明 (ヘッダーまたは FLV タグ) が LiveEvent によって受信されていません。 これはプロトコル違反です。 エンコーダーの製造元に問い合わせてください。 |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| [説明]|オーディオまたはビデオの種類に対する品質の数が、許容される上限を超えました。 |
>| 推奨されている解決方法|ライブ イベントのモードが Live Encoding の場合、エンコーダーではシングル ビットレートのビデオとオーディオをプッシュする必要があります。  同じビットレートからの冗長プッシュが許可されることに注意してください。 エンコーダーのプリセットまたは出力の設定を検査して、シングル ビットレートのストリームが出力されることを確認します。 |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| [説明]|ライブ イベントまたはチャンネル サービスの受信ビットレートの合計が、許容される上限を超えました。 |
>| 推奨されている解決方法|エンコーダーが受信ビットレートの最大値を超えました。 この制限により、関与しているエンコーダーからすべての受信データが集計されます。 エンコーダーのプリセットまたは出力の設定を確認して、ビットレートを下げてください。 |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| [説明]|RTMP エンコーダーからのビデオまたはオーディオ FLVTag のタイムスタンプが無効です。 |
>| 推奨されている解決方法|非推奨になりました。 |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| [説明]|受信エンコーダーによって、ライブ イベントまたはチャンネルのエンコードで許容されている上限の 30 fps を超えるフレーム レートでストリームが取り込まれました。 |
>| 推奨されている解決方法|エンコーダーのプリセットを確認して、フレーム レートを 36 fps 未満に下げてください。 |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| [説明]|受信エンコーダーが、次の許容される解像度を超えるストリームを取り込みました。ライブ イベント/チャンネルのエンコード: 1920x1088、パススルー ライブ イベント/チャンネル: 4096 x 2160 |
>| 推奨されている解決方法|エンコーダーのプリセットを確認し、制限を超えないようにビデオ解像度を下げてください。 |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| [説明]|ライブ イベントで一度に大量の音声データまたは動画データを受け取りましたが、キー フレームがありません。 正しいデータで再試行できるようにエンコーダーが切断されました。 |
>| 推奨されている解決方法|エンコーダーで、すべてのキー フレーム間隔 (GOP) に対してキー フレームが送信されるようにします。  "固定ビットレート (CBR)" や "キー フレームの配置" のような設定を有効にします。 場合によっては、関与するエンコーダーをリセットするとうまく機能することがあります。 それでも解決しない場合は、エンコーダーの製造元にお問い合わせください。 |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

[LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) イベントの次のいずれかのエラーが表示される場合があります。

> [!div class="mx-tdCol2BreakAll"]
>| エラー | Information |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| [説明]|RTMP セッションは、アイドル状態のまま許容制限時間を超えてタイムアウトしました。 |
>|推奨されている解決方法|これは、通常、エンコーダーで入力フィードの受信が停止され、プッシュするデータがないためにセッションがアイドルになったときに発生します。エンコーダーまたは入力フィードの状態が正常な状態であるか確認してください。 |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|[説明]| RTMP エンコーダーからのビデオまたはオーディオ FLVTag のタイムスタンプが無効です。 |
>| 推奨されている解決方法| 非推奨になりました。 |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| [説明]|エンコーダーからデータが送信される速度が速すぎます。 |
>| 推奨されている解決方法|これは、エンコーダーによって、短期間に多数のフラグメントが急激に送信された場合に発生します。  理論的には、ネットワークの問題のためにエンコーダーがしばらくの間データをプッシュできず、ネットワークが利用可能になったときにデータが急激に送信された場合に発生する可能性があります。 エンコーダー ログまたはシステム ログで原因を確認してください。 |
>|**不明なエラー コード** |
>| [説明]| これらのエラー コードは、メモリ エラーから、ハッシュ マップにおけるエントリの重複まで多岐にわたります。 これは、エンコーダーによって、短期間に多数のフラグメントが送出された場合に発生する可能性があります。  これは、エンコーダーがネットワーク問題のためにデータをしばらくプッシュできず、その後ネットワークが使用可能になったときすべての遅延フラグメントを一度に送信したときにも発生する可能性があります。 |
>|推奨されている解決方法| エンコーダー ログを確認してください。|

## <a name="other-error-codes"></a>その他のエラー コード

> [!div class="mx-tdCol2BreakAll"]
>| エラー | Information |拒否または切断イベント|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||はい|
>| 説明|これは一般的なエラーです。 ||
>|推奨されている解決方法| [なし] :||
>|**MPI_SYSTEM_MAINTENANCE** ||はい|
>| 説明|サービスの更新またはシステムのメンテナンスのためにエンコーダーが切断されました。 ||
>|推奨されている解決方法|エンコーダーで "自動接続" が有効になっていることを確認します。 これにより、エンコーダーは、メンテナンス中でない冗長ライブ イベント エンドポイントに再接続できます。 ||
>|**MPE_BAD_URL_SYNTAX** ||はい|
>| 説明|取り込み URL の形式が正しくありません。 ||
>|推奨されている解決方法|取り込み URL の形式が正しいことを確認してください。 RTMP の場合は、次のようになります: `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||はい|
>| 説明|エンコーダーによってセッションが切断されました。  ||
>|推奨されている解決方法|これはエラーではありません。 エンコーダーから開始された切断で、グレースフル切断も含まれます。 予期しない切断である場合は、エンコーダー ログを確認してください。 |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||いいえ|
>| 説明|受信データ速度が、予期されたビットレートと一致しません。 ||
>|推奨されている解決方法|これは、受信データ速度が遅すぎるか速すぎる場合に出される警告です。 エンコーダー ログまたはシステム ログを確認してください。||
>|**MPE_INGEST_DISCONTINUITY** ||いいえ|
>| 説明| 受信データに不連続性があります。||
>|推奨されている解決方法| これは、ネットワークの問題またはシステム リソースの問題のために、エンコーダーでデータが消失していることを示す警告です。 エンコーダー ログまたはシステム ログを確認してください。 システム リソース (CPU、メモリ、またはネットワーク) も監視してください。 システムの CPU 使用率が高すぎる場合は、ビットレートを下げるか、システム グラフィックス カードのハードウェア エンコーダー オプションを使用します。||

## <a name="see-also"></a>関連項目

[ストリーミング エンドポイント (配信元) エラー コード](stream-streaming-endpoint-error-codes-reference.md)

## <a name="next-steps"></a>次のステップ

[チュートリアル:Media Services を使用したライブ ストリーム配信](stream-live-tutorial-with-api.md)

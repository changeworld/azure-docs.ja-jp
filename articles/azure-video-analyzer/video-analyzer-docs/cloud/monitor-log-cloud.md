---
title: 監視とログ記録 - Azure Video Analyzer サービス
description: この記事では、Azure Video Analyzer サービスでの監視とログ記録の概要について説明します。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4a2a6b37c02aad5779085514bc5854df4d8a823
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400544"
---
# <a name="monitor-and-log"></a>監視とログ記録

![クラウドのアイコン](media/env-icon/cloud.png)  
または、[エッジでの監視とログ](../edge/monitor-log-edge.md)に関する記事を参照してください。

---

この記事では、Azure Video Analyzer サービスによって生成されるイベントとログについて説明します。 また、サービスによって生成されるログを使用する方法と、サービス イベントを監視する方法についても説明します。

## <a name="taxonomy-of-events"></a>イベントの分類

次の図は、Video Analyzer サービスによって出力されるイベントまたはテレメトリ データに使用される一般的な分類を示しています。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-taxonomy-cloud.png" alt-text="イベントの分類を示す図。":::

## <a name="event-schema"></a>イベント スキーマ

 Video Analyzer サービスによって生成されるイベントは、システム プロパティ、アプリケーション プロパティ、および本文で構成されます。

### <a name="common-properties"></a>共通プロパティ

すべてのイベントには、次の一連の共通プロパティがあります。

| プロパティ      | プロパティの種類       | データ型 | 説明                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `trace-id`    | システム              | guid      | 一意のイベント ID。                                             |
| `resourceId`  | applicationProperty | string    | Azure Video Analyzer アカウントの ARM パス。               |
| `subject`     | applicationProperty | string    | イベントを出力するエンティティのサブパス。                    |
| `time`        | applicationProperty | string    | イベントが生成された時刻。                                |
| `category`    | システム              | string    | 監査、運用、診断。                              |
| `operationName`| applicationProperty| string    | イベントの種類の識別子 (次のセクションを参照してください)。          |
| `level`        | システム             | string    | イベント レベル (情報、警告、エラー、重大)。           |
| `body`        | body                | object    | 特定のイベント データ。                                       |
| `operationVersion` | システム         | string    | イベント データ バージョン {Major}.{Minor}                           |

## <a name="event-types"></a>イベントの種類
Video Analyzer サービスは、次の種類のイベント データを出力します。

**運用時:** ユーザーの操作によって、または [パイプライン](../pipeline.md)の実行中に生成されるイベント。
* ボリューム: 少ないものと予想されます (1 分間に 2、3 回、またはそれ未満)。 例:

| Event        | Level               | 簡単な説明                                              |
| ------------- | ------------------- |  ------------------------------------------------------------ |
|RecordingStarted   |Informational| メディア記録を開始しました|
|RecordingAvailable |Informational| メディア記録を利用できます|
|RecordingStopped   |Informational| メディア記録を停止しました|
|PipelineStateChanged   |Informational| 変更されたパイプラインの状態|

   *サンプルの操作イベント*
      
```json
{
  "time": "2021-10-06T21:19:36.0988630Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Operational",
  "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
  "operationVersion": "1.0",
  "level": "Informational",
  "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
  "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipeline/sinks/videoSink",
    "body": {
      "type": "video",
      "location": "/videos/livetest1",
      "startTime": "2021-10-06T21:19:32.520Z"
    }
  }
}
```

**診断:** 問題やパフォーマンスを診断するのに役立つイベント。
   * ボリューム: 高 (1 分間に数回) にできますが、ストレージ コストに影響を与える可能性があります。 診断が必要な場合、またはトラブルシューティングの目的でのみ、これらのイベントを有効にすることをお勧めします。 例:
    
| Event             | Level           | 簡単な説明                                            |
| ----------------- | ----------------| ------------------------------------------------------------ |
|AuthenticationError|エラー|サーバー/クライアントの認証エラー|
|AuthorizationError |   エラー|  サーバー/クライアントの承認エラー|
|FormatError        |   エラー|  パッケージ化、フォーマット、またはエンコードの問題|
|MediaSessionEstablished|   Informational|  SDP またはその他のセッション情報|
|NetworkError        |  エラー|  DNS、ネットワーク エラー|
|ProtocolError        | エラー|  RTSP またはその他のプロトコル エラー|
|StorageError        |  エラー|  ストレージの読み取り/書き込みエラー|
|RtspPlaybackSessionEstablished|    Informational| RTSP 再生セッションが確立されました|
|RtspPlaybackSessionClosed| Informational|  RTSP 再生セッションが閉じています|

*サンプル診断イベント*
  
```json
{
  "time": "2021-10-06T21:19:34.1290000Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Diagnostics",
  "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
  "operationVersion": "1.0",
  "level": "Informational",
  "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipesample/sources/rtspSource",
    "body": {
        "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n"
    }
  }
}

```
**監査:** イベントは、API アクセスをログに記録するために使用されます。 
  * ボリューム: 低。監査が必要な場合にのみ、これらのイベントを有効にすることをお勧めします。

*サンプルの監査ログ*
  
```json
{
  "time": "2021-10-07T23:53:31.6792370Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Audit",
  "operationName": "Microsoft.VideoAnalyzer.Audit.ResourceGet",
  "level": "Warning",
  "uri": "https://{GUID}.api.{region}.videoanalyzer.azure.net/videos/batchjobsinknode",
  "resultType": "Failed",
  "resultSignature": "403",
  "identity": [
    {
      "alg": "RS256",
      "kid": "{KID}",
      "typ": "JWT"
    },
    {
      "sub": "livetest1",
      "aud": [ "https://{GUID}.streaming.{region}.videoanalyzer.azure.net/{GUID}", "wss://{GUID}.rtsp-tunnel.{region}.videoanalyzer.azure.net/{GUID}" ],
      "exp": 1633410145,
      "iss": "https://{region}.videoanalyzer.azure.net/"
    }
  ],
  "traceContext": "{\n  \"traceId\": \"4bb0dcf5-5c6d-4aa3-8c03-3f3d7e2c6210\"\n}",
  "properties": { "subject": "/videos/batchjobsinknodesample" }
}

```
## <a name="metrics"></a>メトリック

Video Analyzer サービスは、[パイプライン](../pipeline.md)のメトリックを生成します。 Azure portal を使用してメトリックにアクセスするには、Video Analyzer アカウントの [管理] ウィンドウで [監視] セクションに移動します。

| メトリックの名前                           | Type    | Dimension                                                                              | 説明                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| イングレス バイト数              | カウンタ   | パイプラインの種類、状態、トポロジ名                                 | パイプラインが受信した合計バイト数。 RTSP ソースでのみサポートされます。           |
| パイプライン                  | カウンタ   | パイプラインの種類、状態、トポロジ名                        | パイプラインをさまざまな状態で提供します。 一定の間隔で出力されます。 |

## <a name="monitoring-of-events"></a>イベントの監視

Video Analyzer サービスによって生成されたイベントをストレージ アカウントに保存し、Azure Monitor を使用してそれらを使用することができます。

**Azure Monitor イベントのコレクション**

現在、Video Analyzer サービスでは、ストレージ アカウントへのテレメトリ イベントの書き込みと、それらのイベントを使用するための Azure Monitor の使用がサポートされています。 [Azure Monitor](../../../azure-monitor/overview.md) を使用して、サービスによって生成されたイベントを使用します。お客様には、Azure Monitor を介して組み込みの監視エクスペリエンスが提供されます。

次の手順に従って、Azure Monitor で監視とイベントの収集を有効にします。
* Azure portal で、Video Analyzer アカウントの **[監視しています]** セクションに移動し、 **[診断設定]** を選択します。
* **[診断設定を追加する]** をクリックして、次のログのコレクションを有効にします。
    *   運用時
    *   診断
    *   Audit
* 有効にするログのカテゴリと、対応する保持期間を選択します。
* 送信先の詳細については、 **[ストレージ アカウントへのアーカイブ]** を選択し、これらのイベント ログが格納されるストレージ アカウントを指定します。
    > [!IMPORTANT]
    > 現在、Video Analyzer サービスでは、Azure Storage 以外の宛先への診断の送信はサポートされていません。

* 診断設定を構成したら、 **[保存]** をクリックします。
* 診断ログにアクセスするには、ストレージ エクスプローラーに移動し、ストレージ アカウントを展開すると、BLOB コンテナーが表示されます。 "insights-logs-category" コンテナーには、JSON ファイル形式のログが含まれます。 
* 目的のログ ファイルをダウンロードします。ダウンロードしたログ ファイルの内容は、次の例のようになります。

```json
{
    "time": "2021-10-06T21:19:36.0988630Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Operational",
    "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "operationVersion": "1.0",
    "level": "Informational",
    "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
    "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipesample/sinks/videoSink",
        "body": {
            "type": "video",
            "location": "/videos/livetest1",
            "startTime": "2021-10-06T21:19:32.520Z"
        }
    }
}
{
    "time": "2021-10-06T21:19:34.1290000Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Diagnostics",
    "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "operationVersion": "1.0",
    "level": "Informational",
    "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipe/sources/rtspSource",
        "body": {
            "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n" 
        }
    }
```

**アクティビティ ログ** は、パイプライン操作に対しても自動的に生成されます。また、Azure portal の Video Analyzer アカウントの [アクティビティ ログ] セクションに移動することでアクセスできます。 アカウントに対して行われた ARM API 呼び出しの履歴と関連する詳細を確認できます。

![アクティビティ ログのサンプル](./media/activity-log.png)


## <a name="next-steps"></a>次のステップ

[Azure Video Analyzer サービスのトラブルシューティング](./troubleshoot.md)

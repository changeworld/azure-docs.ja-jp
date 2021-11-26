---
title: Azure Video Analyzer サービスのトラブルシューティング
description: この記事では、Azure Video Analyzer サービスのトラブルシューティング ステップについて説明します。
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3760b17d71dc3526d9a16283084095174d7bb724
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398404"
---
# <a name="troubleshoot-azure-video-analyzer-service"></a>Azure Video Analyzer サービスのトラブルシューティング

![クラウドのアイコン](media/env-icon/cloud.png)  
または、[エッジでのトラブルシューティング](../edge/troubleshoot.md)に関する記事を参照してください。

---

この記事では、サービスの使用中に発生する可能性のある一般的なエラー シナリオのトラブルシューティング ステップについて説明します。

## <a name="enable-diagnostics"></a>診断を有効化する

[監視とログ](./monitor-log-cloud.md)は、Video Analyzer サービス イベントの分類と、問題をデバッグするのに役立つログの生成方法を理解するのに役立ちます。
- Azure portal で、Video Analyzer アカウントの **[監視]** セクションに移動し、 **[診断設定]** を選択します。 
- **[診断設定を追加する]** をクリックして、目的のイベントの種類 `Diagnostics`、`Audit`、`Operational` のログを有効にします。 詳細については、[こちら](./monitor-log-cloud.md)を参照してください。


## <a name="view-diagnostics"></a>診断を表示する

診断を有効にすると、次のようにしてログにアクセスできます。

> [!TIP]
> イベントを出力するために、少なくとも 1 つのライブ パイプラインをアクティブ化することをお勧めします。 

- ポータルで、ログの書き込み先のストレージ アカウントを見つけます
- そのストレージ アカウントの管理ブレードを開きます
- ストレージ エクスプローラーに移動し、ストレージ アカウントを展開すると、BLOB コンテナー "insights-logs-category" が表示されます。 この BLOB には、JSON ファイル形式のログが含まれます。 これらのログをダウンロードして問題を調査することができます

## <a name="view-metrics"></a>メトリックを表示する 

また、Video Analyzer は、次のように問題を特定するのに役立つ、取り込みとパイプラインのメトリックも出力します。
- IngressBytes - パイプラインが受信した合計バイト数。 値が着実に増加している場合は、パイプラインが正常であり、RTSP カメラからビデオデータを受信していることを示します
- Pipelines - パイプラインの状態と数を確認するのに役立ちます。

## <a name="view-activity-logs"></a>アクティビティ ログを表示する

**アクティビティ ログ** は、自動的に生成され、Azure portal の Video Analyzer アカウントの管理ブレードの [アクティビティ ログ] セクションに移動することでアクセスできます。 アカウントに対して行われた ARM API 呼び出しの履歴と関連する詳細を確認できます。

## <a name="common-error-scenarios"></a>一般的なエラー シナリオ

ここでは、Video Analyzer サービスで発生する一般的なエラーについて説明します。

### <a name="unable-to-play-video-after-activating-live-pipeline"></a>ライブ パイプラインをアクティブ化した後でビデオを再生できない

- Video Analyzer ウィジェットを使用してビデオを再生している場合は、Azure portal を使用してアクセスできるかどうかをお試しください。 ビデオがウィジェットではなく Azure portal で再生される場合は、[下記](#playback-error-with-the-widget)のウィジェットのチェックのセクションに進んでください

- Azure portal でビデオが再生されていない場合は、Video Analyzer サービスが RTSP カメラからビデオ データを受信しているかどうかを確認します。 ポータルの [監視] -> [メトリック] セクションに移動して、"イングレス バイト" メトリックを選択します。 集計が増加している場合は、RTSP カメラとサービス間の接続が正常であり、イングレス バイトの合計がグラフの下に表示されます。 

- サービスが RTSP カメラからのビデオ データを受信していない場合は、次のステップとして、[関連する診断ログを表示](#view-diagnostics)します。 [ProtocolError](#diagnostic-logs-have-a-protocolerror-with-code-401) などのエラーが表示される可能性があり、後で説明するようにさらにトラブルシューティングを行うことができます。

### <a name="diagnostic-logs-have-a-protocolerror-with-code-401"></a>診断ログにコード401 の ProtocolError がある

- 診断ログに、次のようにコード 401 に設定された Microsoft.VideoAnalyzer.Diagnostics.ProtocolError が表示される場合、最初のステップとして RTSP 資格情報を再確認する必要があります。 例として、次のようなイベントがあります。

   ```
   {
       "time": "2021-10-15T02:56:18.7890000Z",
       "resourceId": "/SUBSCRIPTIONS/{GUID}/RESOURCEGROUPS/8AVA/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/AVASAMPLEZ2OHI3VBIRQPC",
       "region": "westcentralus",
       "category": "Diagnostics",
       "operationName": "Microsoft.VideoAnalyzer.Diagnostics.ProtocolError",
       "operationVersion": "1.0",
       "level": "Error",
       "traceContext": "{\n  \"traceId\": \"f728d155-b4fd-4aec-8307-bbe2a324f4c3\"\n}",
       "properties": {
           "subject": "/livePipelines/your-pipeline/sources/rtspSource",
           "body": {
               "code": "401",
               "target": "rtsp://127.0.0.1:33643/some-path",
               "protocol": "rtsp"
           }
       }
   }

   ```

- ライブ パイプラインを使用してインターネット経由でアクセスできるカメラに接続している場合は、ライブ パイプラインの作成時に使用された RTSP の URL、ユーザー名、パスワードを確認します。 ライブ パイプラインで GET を呼び出すと、URL とユーザー名を表示できますが、パスワードはエコーバックされません。 ライブ パイプラインの作成に使用されたコードを確認する必要があります。

- [リモート デバイス アダプター](./use-remote-device-adapter.md)を使用している場合は、次のステップをお試しください。

   - [IoT ハブが Video Analyzer アカウントにアタッチされている](../create-video-analyzer-account.md#post-deployment-steps)ことを確認します。 これは、リモート デバイス アダプターを使用するために必要です。
   - エッジ モジュールで `remoteDeviceAdapterList` ダイレクトメソッドを実行し、IP アドレスを確認します。 要求と応答の例は[こちら](../edge/direct-methods.md)にあります
   - 問題が発生しているライブ パイプラインで使用しているリモート デバイス アダプターの応答を確認し、[この記事](use-remote-device-adapter.md)の例と比較します。 カメラの IP アドレスが正しいことを確認します
   - Azure portal -> Video Analyzer アカウント-> [ライブ] -> [パイプライン] -> [ライブ パイプラインの編集] の順に移動し、RTSP のユーザー名とパスワードを再入力します。 指定した RTSP の URL が `rtsp://localhost:554/…` で始まることを確認します。 ここでは、`localhost` を使用する必要があります。

- 上記のステップを実行しても問題が解決せず、ビデオの再生がまだ機能していない場合は、Azure portal にログインし、サポート チケットを開いてください。 Video Analyzer のエッジ モジュールからログを添付することが必要な場合は、[エッジのトラブルシューティング ドキュメント](../edge/troubleshoot.md#common-error-resolutions)の 'support-bundle コマンドの使用' のセクションを参照してください

### <a name="unable-to-record-to-a-video-resource"></a>ビデオ リソースに記録できない

Video Analyzer では、個別の RTSP カメラから録音するときに、個別のビデオ リソースを使用する必要があります。 カメラの設定 (解像度など) を変更した場合は、新しいビデオ リソースに切り替える必要もあります。 一部のサンプル パイプライン トポロジでは、ビデオ シンク ノードのプロパティにビデオ リソースの名前がハードコーディングされています。 これらのトポロジを別のカメラで直接使用すると、この問題が発生します。 一意性を確保するために、ビデオ シンク ノードの `videoName` プロパティを変更します。

### <a name="interrupted-recording-or-playback"></a>記録または再生が中断する

ライブ パイプラインを作成するときに、RTSP カメラがビデオをサービスに送信する最大ビットレート (`bitrateKbps`) を指定する必要があります。
カメラがこの制限を超えた場合、Video Analyzer サービスはカメラからすぐに切断されます。 ビットレートが一時的に急増した場合に備えて、カメラへの接続を再試行することができます。 この状況を特定するには、エラー コードが `SourceBitrateExceeded` の Microsoft.VideoAnalyzer.Diagnostics.RtspIngestionSessionEnded イベントを診断ログで検索します。
これを解決するには、カメラのビットレート設定を減らすか、またはライブ パイプラインの `bitrateKbps` 値をカメラの設定と一致するまで増やします。

### <a name="playback-error-with-the-widget"></a>ウィジェットでの再生エラー

Video Analyzer ウィジェットで 'アクセスが許可されていません' というエラーが表示される場合は、監査ログに警告イベントが表示されます。

- クライアント API JWT トークンとそれに対応するアクセス ポリシーが生成されていることを確認し、[creating-a-token](../access-policies.md) と [creating-an-access-policy](../access-policies.md#creating-an-access-policy) に関するドキュメントを参照してください。 アクセス ポリシーが正しく設定されておらず、JWT トークンがポリシーと一致しない場合、プレイヤーは機能しません。 
- 問題が解決されない場合は、ウィジェットのログを収集し、Azure portal を使用してサポート チケットを提出してください
- ウィジェット ログの収集:
    - F12 キーを押してブラウザー開発者ツールを有効にし、[コンソール] タブの [すべてのレベル] ログを有効にします。   
    - 設定アイコンから、[ユーザー設定] --> [コンソール] --> [タイムスタンプを表示] を選択します。 ログを保存します。

## <a name="collect-logs-for-submitting-a-support-ticket"></a>サポート チケットを送信するためのログを収集する
   
セルフガイドのトラブルシューティング ステップで問題が解決せず、さらにヘルプが必要な場合は、Azure portal を使用してサポート チケットを開き、関連する詳細とログを含めてください。 videoanalyzerhelp@microsoft.com 宛にメールで問い合わせることもできます。
   
> [!WARNING]
> ログには、ご使用の IP アドレスなどの、個人を特定できる情報 (PII) が含まれている可能性があります。 ログのすべてのローカル コピーは、Microsoft による調査が完了し、サポート チケットが閉じられるとすぐに削除されます。
   
## <a name="next-steps"></a>次のステップ

[FAQ](./faq.yml)

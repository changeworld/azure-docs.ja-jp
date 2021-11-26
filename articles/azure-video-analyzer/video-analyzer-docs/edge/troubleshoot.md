---
title: Azure Video Analyzer のトラブルシューティング - Azure
description: この記事では、Azure Video Analyzer エッジ モジュールのトラブルシューティングの手順について説明します。
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38451ae2fe8d42e335a887d093bbab9322d13d81
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398157"
---
# <a name="troubleshoot-azure-video-analyzer"></a>Azure Video Analyzer のトラブルシューティング

![Edge アイコン](media/env-icon/edge.png)  
または、[サービスにおけるトラブルシューティング](../cloud/troubleshoot.md) に関する記事を確認してください。

---

この記事では、Azure Video Analyzer エッジ モジュールのトラブルシューティングの手順について説明します。

## <a name="troubleshoot-deployment-issues"></a>デプロイに関する問題のトラブルシューティング

### <a name="diagnostics"></a>診断

Video Analyzer をデプロイする一環として、IoT Hub や IoT Edge の各デバイスなどの Azure リソースを設定します。 問題を診断するための最初の手順として、次の指示に従って、エッジ デバイスが適切に設定されていることを必ず確認してください。

1. [`check` コマンドを実行する](../../../iot-edge/troubleshoot.md#run-the-check-command)。
1. [IoT Edge のバージョンを確認する](../../../iot-edge/troubleshoot.md#check-your-iot-edge-version)。
1. [IoT Edge Security Manager の状態とそのログを確認する](../../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)。
1. [IoT Edge ハブを通過するメッセージを表示する](../../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)。
1. [コンテナーを再起動する](../../../iot-edge/troubleshoot.md#restart-containers)。
1. [ファイアウォール規則とポート構成規則を確認する](../../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="issues-when-using-arm-template"></a>ARM テンプレートを使用するときの問題

ARM テンプレート (`Deploy to Azure` ボタン) を使用しているときに問題が発生した場合は、この[クイックスタート](get-started-detect-motion-emit-events-portal.md)の手順に従って、必要なリソースを手動でデプロイしてください。 

### <a name="pre-deployment-issues"></a>デプロイ前の問題

エッジ インフラストラクチャが正常な場合は、配置マニフェスト ファイルの問題を調べることができます。 Video Analyzer モジュールを他の IoT モジュールと共に IoT Edge デバイスにデプロイするには、IoT Edge ハブ、IoT Edge エージェント、その他のモジュールとそれらのプロパティを含む配置マニフェストを使用します。 次のコマンドを使用すれば、マニフェスト ファイルをデプロイできます。

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id avasample-iot-edge-device --content <path-to-deployment_manifest.json>
```

JSON コードが整形式ではない場合、次のエラーが表示されます: &nbsp;&nbsp;&nbsp; **Failed to parse JSON from file: '\<deployment manifest.json\>' for argument 'content' with exception: "Extra data: line 101 column 1 (char 5325)"**

このエラーが発生した場合は、かっこの不足やファイルの構造に関するその他の問題がないか、JSON を確認することをお勧めします。 ファイル構造を検証するには、[JSON Viewer プラグイン搭載の Notepad++](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) などのクライアントや、[JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/) などのオンライン ツールを使用できます。

### <a name="during-deployment-iot-edge-runtime-response"></a>デプロイ時: IoT Edge ランタイムの応答

IoT Edge デバイスに Video Analyzer モジュールが正しくデプロイされたら、Azure portal を使用して [IoT Edge ランタイム](../../../iot-edge/iot-edge-runtime.md)の診断を実行できます。

1. Azure portal で、IoT Edge デバイスに接続されている IoT ハブにアクセスします。
1. **[デバイスの自動管理]** を探し、 **[IoT Edge]** を選択します。
1. エッジ デバイスの一覧で、診断するデバイスを選択し、[管理] ブレードを開きます。

   ![エッジ デバイスの一覧が表示された Azure portal のスクリーンショット](./media/troubleshoot/ava-sample-device.png)

1. IoT Edge ランタイム応答コードが _200-OK_ かどうかを確認します。 その他の応答コードは次のとおりです。

   - 400 - デプロイ構成が正しくない形式であるか、無効です。
   - 417 - デバイスにデプロイ構成セットがありません。
   - 412 - デプロイ構成にあるスキーマ バージョンが無効です。
   - 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
   - 500 - IoT Edge ランタイムでエラーが発生しました。

   > [!TIP]
   > お使いの環境で Azure IoT Edge モジュールを実行するときに問題が発生した場合は、トラブルシューティングと診断のガイドとして **[Azure IoT Edge の標準的な診断手順](../../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** に関する記事を参照してください。

### <a name="post-deployment-reported-properties-and-direct-methods"></a>デプロイ後: 報告されるプロパティとダイレクト メソッド

Azure portal で、Video Analyzer エッジ モジュールの [管理] ブレードを選択して開きます。
まず、報告されるプロパティと必要なプロパティを確認します。これは、モジュールのプロパティがデプロイと同期されているかどうかを理解するのに役立ちます。 同期されていない場合は、IoT Edge デバイスを再起動できます。 **[デプロイで指定]** と **[デバイス別に報告]** 列が [はい] である場合、**[ダイレクト メソッド]** メニュー オプションをクリックして、Video Analyzer モジュールでダイレクト メソッドを呼び出すことができます。

[ダイレクト メソッド](direct-methods.md)のガイドを使用して、いくつかのメソッド (特に `pipelineTopologyList` などの単純なもの) を呼び出すことができます。 このガイドでは、予想される要求、応答のペイロードとエラーコードも指定します。 単純なダイレクト メソッドが正常に完了したら、Video Analyzer エッジ モジュールが正常に機能していることを確認できています。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/troubleshoot/direct-method.png" alt-text="IoT Edge モジュールの [ダイレクト メソッド] ペインのスクリーンショット。" lightbox="./media/troubleshoot/direct-method.png":::

以下の点にご注意ください。
1. 状態 `501 code` のコードが表示された場合は、ダイレクト メソッド名が正確であることを確認してください。 メソッド名と要求ペイロードが正確である場合は、成功コード 200 で結果を受け取ります。
1. 要求ペイロードが不正確な場合は、状態 `400 code` と、ダイレクト メソッド呼び出しに関する問題の診断に役立つエラー コードとメッセージを示す応答ペイロードが表示されます。

### <a name="post-deployment-diagnostic-logs-for-issues"></a>デプロイ後: 問題の診断ログ

IoT Edge モジュールのコンテナー ログには、ライブ ビデオの分析中に発生した問題のデバッグに役立つ診断情報が含まれています。 [コンテナーのログで問題を確認](../../../iot-edge/troubleshoot.md#check-container-logs-for-issues)し、問題を自己診断できる場合があります。

上記のすべてのチェックを実行しても問題が解決しない場合は、Azure チームがさらに分析できるように、[`support bundle` コマンドを使用](../../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)して、IoT Edge デバイスからログを収集してください。 [サポート ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)で Microsoft に問い合わせて、収集したログを送信していただくことができます。

## <a name="common-error-resolutions"></a>よくあるエラーの解決方法

Video Analyzer エッジ モジュールは、IoT Edge エージェントおよびハブ モジュールと連携して動作します。 デプロイで発生する一般的なエラーのいくつかは、基になる IoT インフラストラクチャの問題によって発生します。 このようなエラーには次のものが含まれます。

- [約 1 分後に IoT Edge エージェントが停止する](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)。
- [IoT Edge エージェントがモジュールのイメージにアクセスできない (403)](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403)。
- [IoT Edge エージェント モジュールで "空の構成ファイル" がレポートされ、デバイスでモジュールが開始しない](../../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
- [IoT Edge ハブが起動に失敗する](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)。
- [IoT Edge セキュリティ デーモンが無効なホスト名で失敗する](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
- [Video Analyzer またはその他のカスタム IoT Edge モジュールが、404 エラーによりエッジ ハブにメッセージを送信できない](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
- [IoT Edge モジュールがデプロイに成功したのに、デバイスに表示されなくなる](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device)。

  > [!TIP]
  > お使いの環境で Azure IoT Edge モジュールを実行するときに問題が発生した場合は、トラブルシューティングと診断のガイドとして **[Azure IoT Edge の標準的な診断手順](../../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** に関する記事を参照してください。

支援が必要な追加の問題がある場合は、 **[ログを収集し、サポート チケットを送信](#collect-logs-for-submitting-a-support-ticket)** してください。 **[videoanalyzerhelp@microsoft.com](mailto:videoanalyzerhelp@microsoft.com)** 宛にメールで問い合わせることもできます。

### <a name="working-with-external-modules"></a>外部モジュールの操作

パイプライン拡張プロセッサを使用すると、パイプラインを拡張して、HTTP または gRPC プロトコルを使用して他の IoT Edge モジュールとの間でデータを送受信することができます。 [具体的な例](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/httpExtension)として、このライブ パイプラインは、Yolo v3 などの外部推論モジュールに画像としてビデオ フレームを送信し、HTTP プロトコルを使用して JSON ベースの分析結果を受け取ることができます。 このようなトポロジでは、イベントの送信先は主に IoT ハブです。 ハブで推論イベントが表示されない場合は、次の点を確認します。

- ライブ パイプラインの公開先となるハブと、調査中のハブが同じであるかどうかを確認します。 複数のデプロイを作成すると、ハブが複数作成され、イベントに対して間違ったハブを誤って確認していることがあります。
- Azure portal で、外部モジュールがデプロイされて実行されているかどうかを確認します。 この例の図では、rtspsim、yolov3、tinyyolov3、logAnalyticsAgent は、avaedge モジュールの外部で実行される IoT Edge モジュールです。

  [ ![Azure IoT Hub のモジュールの実行状態を表示するスクリーンショット。](./media/troubleshoot/iot-hub-azure.png) ](./media/troubleshoot/iot-hub-azure.png#lightbox)

- 正しい URL エンドポイントにイベントを送信しているかどうかを確認します。 外部 AI コンテナーは、URL と、POST 要求からデータを受信して返すポートを公開します。 この URL は、HTTP 拡張プロセッサの `endpoint: url` プロパティとして指定されます。 [トポロジ URL](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) に示されているように、エンドポイントは推論の URL パラメーターに設定されています。 このパラメーターの既定値、または渡された値が正確であることを確認します。 クライアント URL (cURL) を使用することにより、それが機能しているかどうかをテストできます。

  例として、ローカル コンピューター上で実行されている Yolo v3 コンテナー (IP アドレス: 172.17.0.3) があるとします。

  ```shell
  curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
  ```

  返される結果:

  ```json
  {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
  ```

  > [!TIP]
  > **[Docker 検査コマンド](https://docs.docker.com/engine/reference/commandline/inspect/)** を使用して、マシンの IP アドレスを検索します。

- パイプライン拡張プロセッサを使用するライブ パイプラインを 1 つ以上実行する場合、ビデオ フィードの 1 秒あたりのフレーム数 (fps) を管理するために、`samplingOptions` フィールドを使用する必要があります。

  - エッジ マシンの CPU やメモリの使用率が高い特定の状況では、特定の推論イベントが失われることがあります。 この問題に対処するには、`samplingOptions` フィールドで `maximumSamplesPerSecond` プロパティの値を低く設定します。 パイプラインの各インスタンスで、これを 0.5 ("maximumSamplesPerSecond": "0.5") に設定し、その後インスタンスを再実行して、ハブで推論イベントを確認できます。

### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>複数のダイレクトメソッドの並列 –タイムアウト エラー

Video Analyzer では、複数のトポロジと複数のパイプラインを設定できる、ダイレクト メソッド ベースのプログラミング モデルが用意されています。 トポロジとパイプラインのセットアップの一環として、IoT Edge モジュールで複数のダイレクト メソッド呼び出しを行います。 これらの複数のメソッド呼び出し (特に、パイプラインを開始および停止するもの) を並列で行うと、次のようなタイムアウト エラーが発生する可能性があります。

Assembly Initialization method Microsoft.Media.VideoAnalyzer.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync threw exception. Microsoft.Azure.Devices.Common.Exceptions.IotHubException: <br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution.`

ダイレクト メソッドは、並列で "_呼び出さない_" ことをお勧めします。 順番に呼び出すようにします。つまり、前のダイレクト メソッド呼び出しが終了してから次を実行します。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>サポート チケットを送信するためのログを収集する

セルフガイドによるトラブルシューティング手順で問題が解決しない場合は、Azure portal にアクセスして、[サポート チケットを開いてください](../../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> ログには、ご使用の IP アドレスなどの、個人を特定できる情報 (PII) が含まれている可能性があります。 ログのすべてのローカル コピーは、Microsoft による調査が完了し、サポート チケットが閉じられるとすぐに削除されます。

チケットに追加する必要のある関連するログを収集するには、以下の指示に順番に従い、サポート要求の **[詳細]** ウィンドウにログ ファイルをアップロードします。

1. [詳細ログを収集するように Video Analyzer モジュールを構成する](#configure-video-analyzer-module-to-collect-verbose-logs)
1. [デバッグ ログを有効にします](#video-analyzer-edge-module-debug-logs)
1. 問題を再現します
1. Video Analyzer エッジ モジュールを再起動します。 
    > [!NOTE]
    > この手順は、エッジ モジュールを適切に終了し、イベントを一切漏らすことなくすべてのログ ファイルを使用可能な形式で取得するために必要です。   
    
    IoT Edge デバイスで、次のコマンドの `<avaedge>` を Video Analyzer エッジ モジュールの名前に置き換えて使用してます。
    
    ```cmd
    sudo iotedge restart <avaedge>
    ```

   Azure portal からリモートでモジュールを再起動することもできます。 詳細については、[「Azure portal から IoT Edge デバイスを監視およびトラブルシューティングする」](../../../iot-edge/troubleshoot-in-portal.md)を参照してください。
1. ポータルの **[IoT Hub]** ページから仮想マシンに接続します

   1. _debugLogs_ フォルダー内のすべてのファイルを zip 圧縮します。 これらのログ ファイルは、自己診断を目的としたものではありません。 これらは、Azure エンジニアリング チームがお客様の問題を分析するために使用するものです。

      - 次のコマンドで、 **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** の部分を、前に **手順 2** で設定したエッジ デバイス上のデバッグ ログの場所に忘れずに置き換えてください。

        ```
        sudo apt install zip unzip
        zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE
        ```

   1. この _debugLogs.zip_ ファイルをサポート チケットに添付します。

1. [サポート バンドル コマンド](#use-the-support-bundle-command)を実行し、ログを収集してサポート チケットにアタッチします。

### <a name="configure-video-analyzer-module-to-collect-verbose-logs"></a>詳細ログを収集するように Video Analyzer モジュールを構成する

次のように `logLevel` と `logCategories` を設定して、詳細ログを収集するように Video Analyzer モジュールを構成します。

```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

これは次のいずれかで行えます。

- **Azure portal** で、Video Analyzer モジュールのモジュール ID ツインのプロパティを更新します。[![モジュール ID ツインのプロパティ。](media/troubleshoot/module-twin.png)](media/troubleshoot/module-twin.png#lightbox)
- または、**配置マニフェスト** ファイルで、Video Analyzer モジュールのプロパティ ノードにこれらのエントリを追加できます。

### <a name="use-the-support-bundle-command"></a>support-bundle コマンドを使用する

IoT Edge デバイスからログを収集する必要がある場合、最も簡単な方法は `support-bundle` コマンドを使用することです。 このコマンドでは、次の内容を収集します。

- モジュール ログ
- IoT Edge Security Manager とコンテナー エンジンのログ
- IoT Edge check JSON 出力
- 有用なデバッグ情報

1. ログの収集時間の長さを指定するには、 _--since_ フラグを指定して `support-bundle` コマンドを実行します。 たとえば、2h と指定すると、過去 2 時間のログを取得します。 このフラグの値を変更することで、別の期間のログを取得できます。

   ```shell
   sudo iotedge support-bundle --since 2h
   ```

   このコマンドを実行すると、コマンドを実行したディレクトリに、_support_bundle.zip_ という名前のファイルが作成されます。

1. この _support_bundle.zip_ ファイルをサポート チケットに添付します。

### <a name="video-analyzer-edge-module-debug-logs"></a>Video Analyzer エッジ モジュールのデバッグ ログ

デバッグ ログを生成するように Video Analyzer エッジ モジュールを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、IoT ハブに移動します。
1. 左側のペインで、 **[IoT Edge]** を選択します。
1. デバイスの一覧で、ターゲット デバイスの ID を選択します。
1. ペインの上部にある **[モジュールの設定]** を選択します。

   ![Azure portal の [モジュールの設定] ボタンのスクリーンショット。](media/troubleshoot/set-modules.png)

1. **[IoT Edge モジュール]** セクションで、 **[avaedge]** を探して選択します。
1. **[モジュール ID ツイン]** を選択します。 編集可能なペインが開きます。
1. **[desired key]\(必要なキー\)** の下に、次のキーと値のペアを追加します。

   `"DebugLogsDirectory": "/var/lib/videoanalyzer/logs"`

   > [!NOTE]
   > このコマンドにより、エッジ デバイスとコンテナーの間でログ フォルダーがバインドされます。 デバイス上の別の場所にあるログを収集するには、次のようにします。
   >
   > 1. **[バインド]** セクションでデバッグ ログの場所のバインドを作成し、 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** と **$DEBUG _LOG_LOCATION** を目的の場所 `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION` に置き換えます。
   > 2. 次のコマンドを使用します。 **$DEBUG _LOG_LOCATION** は、前の手順で使用した場所に置き換えます: `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`

1. **[保存]** を選択します。

1. **[モジュール ID ツイン]** の値を _null_ に設定すると、ログ収集を停止できます。 **[モジュール ID ツイン]** ページに戻り、次の内容をパラメーターとして更新します。

   `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>ログ記録に関するベスト プラクティス

[監視とログ記録](monitor-log-edge.md)は、分類と、Video Analyzer の問題をデバッグするのに役立つログの生成方法を理解するのに役立ちます。

gRPC サーバーの実装は言語間で異なるので、サーバーには、内部でログ記録を追加する標準的な方法はありません。

たとえば、.NET Core を使用して gRPC サーバーを構築する場合、gRPC サービスは **Grpc** カテゴリ下にログを追加します。 gRPC から詳細なログを有効にするには、次の項目をログ記録の LogLevel サブセクションに追加することで、appsettings.json ファイルのデバッグ レベルに Grpc プレフィックスを構成します。

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information",
      "Grpc": "Debug"
       }
  }
}
```

ConfigureLogging を使用して、Startup.cs ファイルでこれを構成することもできます。

```
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {

           logging.AddFilter("Grpc", LogLevel.Debug);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });

```

[.NET の gRPC でのログ記録と診断](/aspnet/core/grpc/diagnostics?view=aspnetcore-5.0&preserve-view=true)には、gRPC サーバーから複数の診断ログを収集するためのいくつかのガイダンスが用意されています。

### <a name="a-failed-grpc-connection"></a>失敗した gRPC 接続

パイプラインがアクティブであり、カメラからストリーミングされている場合、接続は Video Analyzer によって維持されます。

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>CPU および GPU リソースがボトルネックになっているときに、これらのリソースの負荷を監視し分散する

Video Analyzer エッジ モジュールは、ハードウェア リソースを監視したり、監視を提供したりすることはありません。 開発者は、ハードウェア製造元の監視ソリューションを使用する必要があります。 ただし、Kubernetes コンテナーを使用する場合は、[Kubernetes ダッシュボード](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)を使用してデバイスを監視できます。

また、.NET Core ドキュメントの gRPC は、「[パフォーマンスのベストプラクティス](/aspnet/core/grpc/performance?view=aspnetcore-5.0&preserve-view=true)」および「[負荷分散](/aspnet/core/grpc/performance?view=aspnetcore-5.0&preserve-view=true#load-balancing)」に関する重要な情報も共有します。

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>どのフレームも受信しない場合および "不明" プロトコル エラーを受信している場合の推論サーバーのトラブルシューティング

問題に関する詳細情報を取得するために実行できるいくつかの操作があります。

- Video Analyzer モジュールの目的のプロパティに "**mediaPipeline**" ログ カテゴリを含め、ログ レベルが `Information` に設定されていることを確認します。
- ネットワーク接続をテストするには、エッジ デバイスから次のコマンドを実行できます。

  ```
  sudo docker exec avaedge /bin/bash -c "apt update; apt install -y telnet; telnet <inference-host> <inference-port>"
  ```

  コマンドによって短い文字列の乱雑なテキストが出力された場合、Telnet は正常に推論サーバーへの接続を開き、バイナリ gRPC チャネルを開くことができました。 これが表示されない場合、Telnet はネットワーク エラーを報告します。

- 推論サーバーでは、gRPC ライブラリで追加のログ記録を有効にすることができます。 これにより、gRPC チャネル自体に関する追加情報が提供されます。 この操作は言語によって異なりますが、ここでは [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) での手順を説明します。

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>最初のバッファーの結果を返さずに gRPC のバッファーからより多くのイメージを選択する

gRPC データ転送コントラクトの一部として、Video Analyzer が gRPC 推論サーバーに送信するすべてのメッセージを確認する必要があります。 イメージ フレームの受信を確認しないと、データ コントラクトが中断され、望ましくない状況になる場合があります。

Video Analyzer エッジ モジュールで gRPC サーバーを使用するには、最適なパフォーマンスを得るために共有メモリを使用できます。 これには、プログラミング言語/環境によって公開された Linux 共有メモリ機能を使用する必要があります。

1. Linux 共有メモリ ハンドルを開きます。
1. フレームを受信したときに、共有メモリ内のアドレス オフセットにアクセスします。
1. フレーム処理の完了を確認し、メモリが Video Analyzer エッジ モジュールによって再利用できるようにします。

   > [!NOTE]
   > Video Analyzer がフレームを受信したことを確認するときに長時間の遅延が発生した場合、共有メモリがいっぱいになり、データがドロップされる場合があります。

1. 各フレームは、推論サーバー上の任意のデータ構造 (リスト、配列など) に格納します。
1. この場合、必要な数のイメージ フレームがあれば、処理ロジックを実行できます。
1. 準備ができたら、推論の結果を Video Analyzer エッジ モジュールに戻します。

## <a name="next-steps"></a>次のステップ

[チュートリアル:クラウドへのイベントベースのビデオ記録とクラウドからの再生](record-event-based-live-video.md)

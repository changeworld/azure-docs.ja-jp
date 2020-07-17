---
title: Live Video Analytics on IoT Edge のトラブルシューティング - Azure
description: この記事では、Live Video Analytics on IoT Edge のトラブルシューティング手順について説明します。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045580"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Live Video Analytics on IoT Edge のトラブルシューティング

この記事では、Live Video Analytics on IoT Edge のトラブルシューティング手順について説明します。

## <a name="troubleshoot-deployment-issues"></a>デプロイに関する問題のトラブルシューティング

### <a name="diagnostics"></a>診断

Live Video Analytics をデプロイする一環として、IoT Hub や IoT Edge デバイスなどの Azure リソースを設定します。 問題を診断するための最初の手順として、次の指示に従って、エッジが適切に設定されていることを確認します。

1. ["check" コマンドを実行する](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [IoT Edge のバージョンを確認する](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [IoT Edge Security Manager の状態とそのログを確認する](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [IoT Edge ハブを通過するメッセージを表示する](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [コンテナーを再起動する](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [ファイアウォール規則とポート構成規則を確認する](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>デプロイ前の問題

エッジ インフラストラクチャが正常な場合は、配置マニフェスト ファイルの問題を調べることができます。 Live Video Analytics on IoT Edge モジュールを他の IoT モジュールと共にデプロイするには、エッジ ハブ、エッジ エージェント、その他のモジュール、それらのプロパティを含む配置マニフェストを使用します。 JSON の形式が適切でない場合は、次のようなエラーが表示されます。 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Failed to parse json from file: '<deployment manifest.json>' for argument 'content' with exception:"Extra data: line 101 column 1 (char 5325)"

このエラーが発生した場合は、JSON ファイルでかっこの不足やファイルの構造に関するその他の問題がないか確認することをお勧めします。 [JSON Viewer プラグイン搭載の Notepad++](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) などのクライアント、または https://jsonformatter.curiousconcept.com/ などのオンライン ツールを使用して、ファイルの構造を検証できます。

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>デプロイ– メディア グラフのダイレクト メソッドを使用した診断 

Live Video Analytics on IoT Edge モジュールがエッジ デバイスに正しく配置されると、[ダイレクト メソッド](direct-methods.md)を呼び出すことによって、メディア グラフを作成して実行できます。 ポータルを使用して、ダイレクト メソッド経由でメディア グラフの診断を実行できます。

1. ポータルで、エッジ デバイスに接続されている IoT Hub にアクセスします。
    1. [IoT Hub] ブレードで [デバイスの自動管理] -> [IoT Edge] を探します。
    1. [IoT Edge] をクリックすると、エッジ デバイスの一覧が表示されます。 診断するデバイスをクリックします。
         
        ![エッジ デバイス](./media/troubleshoot-how-to/lva-sample-device.png)
    1. 応答コードが 200-OK であるかどうかを確認します。 [IoT Edge ランタイム](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime)には、次のようなさまざまな応答コードがあります。
        1. 400 - デプロイ構成が正しくない形式であるか、無効です。
        1. 417 - デバイスにデプロイ構成セットがありません。
        1. 412 - デプロイ構成にあるスキーマ バージョンが無効です。
        1. 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
        1. 500 - IoT Edge ランタイムでエラーが発生しました。
    1. デバイスをクリックすると、デプロイ済みの期待される IoT Edge モジュールとその状態の一覧も表示されます
    1. [デプロイで指定] 列と [デバイス別に報告] 列に [はい] が示されている場合は、Live Video Analytics on IoT Edge モジュールでダイレクト メソッドを呼び出すことができます。 モジュールをクリックすると表示される画面では、必要なプロパティと報告されたプロパティを確認し、ダイレクト メソッドを呼び出すことができます。 
        1. 報告されるプロパティと必要なプロパティを確認すると、モジュールのプロパティがデプロイと同期されているかどうかを理解するのに役立ちます。 そうでない場合は、エッジを再起動することができます 
        1. [ダイレクト メソッド](direct-methods.md)のガイドを使用して、いくつかのメソッド (特に GraphTopologyList のような単純なメソッド) を呼び出します。 このガイドでは、予想される要求、応答のペイロードとエラーコードも指定します。 単純なダイレクト メソッドが正常に完了したら、Live Video Analytics エッジ モジュールが問題なく動作することを確認できます。
        
        ![ダイレクト メソッド](./media/troubleshoot-how-to/direct-method.png) 
1. 状態 501 のコードが表示された場合は、ダイレクト メソッド名が正確であるかどうかを確認してください。 メソッド名と要求ペイロードが正確である場合は、成功コード = 200 と共に結果が返されます。 要求ペイロードが不正確な場合は、状態 = 400 と、ダイレクト メソッド呼び出しに関する問題の診断に役立つエラー コードとメッセージを示す応答ペイロードが表示されます。 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>デプロイ後 - 実行時の問題に関するログの診断 

エッジ モジュールのコンテナー ログには、モジュールの実行時に<!--<todo:add link to diagnostics doc>--> 発生する問題のデバッグに役立つ診断情報が必要です。 [コンテナー ログで問題を確認し](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues)、自己診断できます。ただし、上記のすべてのチェックを実行し、それでも問題が解決しない場合は、["サポート バンドル" コマンドを使用して](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) IoT Edge デバイスからログを収集し、Azure チームがさらに分析できます。 サポートについて弊社まで[お問い合わせになり](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)、収集したログをお送りいただくことができます。

## <a name="common-error-resolutions"></a>よくあるエラーの解決方法

Live Video Analytics は、エッジ デバイス上で IoT Edge モジュールとしてデプロイされ、IoT Edge エージェントおよびハブ モジュールと連携して動作します。 Live Video Analytics のデプロイで直面する一般的なエラーのいくつかは、基になる IoT インフラストラクチャの問題に起因します。 IoT Edge エージェントとハブで発生する一般的なエラーには、次のようなものがあります。

1. [約 1 分後に IoT Edge エージェントが停止する](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute)。
1. [IoT Edge エージェントがモジュールのイメージにアクセスできない (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403)。
1. [Edge エージェント モジュールで "空の構成ファイル" がレポートされ、デバイスでモジュールが開始しない](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
1. [IoT Edge ハブが起動に失敗する](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start)。
1. [IoT Edge セキュリティ デーモンが無効なホスト名で失敗する](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
1. [Live Video Analytics またはその他のカスタム IoT Edge モジュールがエッジ ハブへのメッセージ送信に 404 エラーで失敗する](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
1. [デプロイに成功した IoT Edge モジュールがデバイスから消える](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device)。

### <a name="edge-set-up-script-issues"></a>エッジ セットアップ スクリプトの問題

Microsoft のドキュメントの一部として、Live Video Analytics Edge を開始するために、エッジとクラウドのリソースをデプロイするための[セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)を用意しました。 このセクションでは、そのスクリプトで直面する可能性のあるエラーとそのデバッグ方法について説明しています。

スクリプトを実行すると、いくつかのリソースを部分的に作成しますが、次のメッセージが表示されて失敗します。

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
この問題を解決するには、次の手順に従います。

1. 次のコマンドを実行します。

    ```
    az --version
    ```
1. 次の拡張機能がインストールされていることを確認します。 このガイドの執筆時点では、拡張機能のバージョンは次のようになっていました。

    | 拡張機能 | Version |
    |---|---|
    |azure-cli   |      2.5.1*|
    |command-modules-nspkg         |   2.0.3|
    |core  |    2.5.1*|
    |nspkg    | 3.0.4|
    |telemetry| 1.0.4|
    |拡張機能:    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 拡張機能のいずれかが上記のリリース番号よりも古い場合は、コマンドを使用して拡張機能を最新バージョンに更新します。

    ```
    az extension update --name <Extension name>
    ```

    たとえば、`az extension update --name azure-iot` のように指定します。

### <a name="sample-app-issues"></a>サンプル アプリの問題

今回のリリースでは、開発者コミュニティがブートストラップされるように .NET サンプル コードを提供しました。 このセクションでは、サンプル コードの実行中に発生する可能性のあるエラーをキャプチャし、そのようなエラーをデバッグする方法を説明しています。

1. Program.cs は、ダイレクト メソッドの呼び出しで次のエラーが発生して失敗します。

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. VS Code 環境に [Azure IoT ツール](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)がインストールされていること、および IoT Hub セットアップへの接続があることを確認します (Ctrl + Shift + P キーを押してから [IoT Hub の選択] 方法を選択して、サブスクリプションと IoT Hub に接続します)。
1. VS Code を使用してエッジ モジュールでダイレクトメソッドを呼び出すことができるかどうかを確認します (たとえば、次のペイロード { "@apiVersion":"1.0"} を使用して GraphToplogyList を呼び出します)。次の応答が返されます。 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 上記のエラーが発生した場合は、次の操作を試してください。
    1. エッジ デバイスでコマンド プロンプトに移動し、入力します。
    
    ```
    sudo systemctl restart iotedge
    ```

    これにより、エッジ デバイスとすべてのモジュールが再起動されます。 しばらく待ってから次を実行してモジュールが実行中であることを確認してから、ダイレクト メソッドの使用を再試行します。

    ```
    sudo iotedge list
    ```
    1. 上記も失敗する場合は、VM またはマシンを再起動してみてください。
    1. すべて失敗する場合は、次を実行して、すべての[関連ログ](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command)を含む ZIP ファイルを取得し、[サポート チケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)に添付します。

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. エラー応答 400 コードが表示された場合は、メソッド呼び出しペイロードが[ダイレクト メソッド](direct-methods.md)のガイドに従って適切な形式になっていることを確認してください。
1. 状態 200 コードが表示された場合は、ハブが正常に機能しること、モジュールのデプロイが適切で応答していることを示しています。 次の手順では、アプリの構成が正確かどうかを確認します。 アプリの構成は、appsettings.json ファイルの次のフィールドで構成されています。 deviceId と moduleId が正確であることをもう一度確認してください。 これを確認する簡単な方法は、VSCode の Azure IoT Hub 拡張機能セクションを使用することです。 appsettings.json ファイルと IoT Hub セクションの値が一致する必要があります。
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)

1. 最後に、appsettings.json 内で、IoT Hub デバイス接続文字列ではなく IoT Hub 接続文字列を指定していることを確認します (これらは[形式](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)が異なります)。

### <a name="live-video-analytics-working-with-external-modules"></a>Live Video Analytics による外部モジュールの操作

HTTP 拡張プロセッサ経由の Live Video Analytics では、メディア グラフを拡張し、他の IoT Edge モジュールに対して REST を使用して HTTP 経由でデータを送受信することができます。  [具体的な例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)として、メディア グラフでは、Yolo V3 のような外部推論モジュールへビデオ フレームをイメージとして送信し、JSON ベースの分析結果を受け取ることができます。 このようなトポロジでは、イベントの最終的な送信先は主に IoT Hub です。 ハブで推論イベントが表示されない場合は、次の点を確認してください。

1. メディア グラフの公開先となるハブと、調査しているハブが同じであるかどうかを確認します。 複数のデプロイを作成すると、複数のハブが作成され、間違ったハブのイベントを誤って確認していることがあります。
1. VSCode を使用して、外部モジュールがデプロイされて実行されているかどうかを確認します。 この例の図では、rtspsim と cv は、lvaEdge モジュールの外部で実行されている IoT Edge モジュールです。

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)
1. 正しい URL エンドポイントにイベントを送信しているかどうかを確認します。 外部 AI コンテナーは、URL と、POST 要求からデータを受信して返すポートを公開します。 この URL は、Http 拡張プロセッサの endpoint: url プロパティとして指定されます。 [topology](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) に示されているように、url は推論の url パラメーターに設定されています。 パラメーターの既定値 (http://yolov3/score) または渡された値が正確であることを確認したら、curl を使用して動作しているかどうかをテストできます。  
    1. 例として、ローカル コンピューター上で実行されている yolo v3 コンテナーがあり、コンテナーの IP アドレスは 172.17.0.3 です (docker inspect を使用して IP アドレスを検索します)。

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    返される結果:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Http 拡張プロセッサを利用しているグラフのインスタンスを 1 つ以上実行している場合は、ビデオ フィードの 1 秒あたりのフレーム数 (fps) を管理するため、各 Http 拡張プロセッサの前にフレーム レート フィルターが必要です。 エッジ マシンの CPU/メモリの使用率が高い特定の状況では、特定の推論イベントが失われることがあります。 これに対処するには、フレーム レート フィルターの maximumFps プロパティの値を低く設定します。 グラフの各インスタンスで 0.5 ("maximumFps":0.5) に設定して再実行し、ハブで推論イベントを確認できます。
    1. また、より強力な CPU とより多くのメモリを搭載するパワフルなエッジ マシンを入手する方法もあります。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>複数のダイレクトメソッドの並列 –タイムアウト エラー 

Live Video Analytics on IoT Edge では、複数のトポロジと複数のグラフ インスタンスの設定を可能にする、ダイレクト メソッド ベースのプログラミング モデルが用意されています。 トポロジとグラフのセットアップの一環として、エッジ モジュールで複数のダイレクト メソッド呼び出しを起動します。 これらの複数のメソッド呼び出し (特に、グラフを開始する呼び出しやグラフを停止する呼び出し) を起動すると、次のようなタイムアウト エラーが発生する可能性があります。 

Assembly Initialization method Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync threw exception. Microsoft.Azure.Devices.Common.Exceptions.IotHubException:Microsoft.Azure.Devices.Common.Exceptions.IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

ダイレクト メソッドは並列で呼び出さず、直列で呼び出すことをお勧めします。つまり、ダイレクト メソッド呼び出しが終了した後にのみ、次のダイレクト メソッド呼び出しを起動します。

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>サポート チケットを送信するためのログの収集

セルフガイドによるトラブルシューティング手順で問題が解決しない場合は、Azure portal にアクセスして[サポート チケットを開いてください](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

チケットに追加する必要のある関連ログを収集するには、以下の手順を実行します。 ログ ファイルは、サポート リクエストの **[詳細]** タブからアップロードできます。

### <a name="support-bundle"></a>Support-bundle

IoT Edge デバイスからログを収集する必要がある場合、最も簡単な方法は `support-bundle` コマンドを使用することです。 このコマンドでは、次の内容を収集します。

- モジュール ログ
- IoT Edge Security Manager とコンテナー エンジンのログ
- Iotedge check JSON 出力
- 有用なデバッグ情報

#### <a name="use-the-iot-edge-security-manager"></a>IoT Edge Security Manager を使用する
 
IoT Edge Security Manager は、デバイスの起動およびプロビジョニング時の IoT Edge システムの初期化などの操作を担当します。 IoT Edge が開始されていない場合、セキュリティ マネージャーのログに有用な情報が提供されることがあります。 IoT Edge Security Manager の詳細なログを表示するには、次の手順を実行します。

1. IoT Edge デバイスの IoT Edge デーモン設定を編集します。

    ```
    sudo systemctl edit iotedge.service
    ```

1. 次の行を更新します。

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. 次のコマンドを実行して、IoT Edge Security Daemon を再起動します。

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. ログを取得する過去の期間を指定するには、--since フラグを指定して `support-bundle` コマンドを実行します。 たとえば、2h は過去 2 時間のログを取得します。 このフラグの値を変更することで、別の期間のログを取得できます。

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>LVA デバッグ ログ

デバッグ ログを生成するように IoT Edge モジュールの LVA を構成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. メニューから **[IoT Edge]** を選択します。
1. デバイスの一覧でターゲット デバイスの ID をクリックします。
1. 上部メニューの **[モジュールの設定]** リンクをクリックします。

  ![Azure portal の [モジュールの設定]](media/troubleshoot-how-to/set-modules.png)

5. [IoT Edge モジュール] セクションで、 **[lvaEdge]** を見つけてクリックします。
1. **[コンテナーの作成オプション]** をクリックします。
1. [Binds]\(バインド\) セクションで、次のコマンドを追加します。

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    これにより、エッジ デバイスとコンテナーの間でログ フォルダーがバインドされます。

1. **[更新]** ボタンをクリックします。
1. ページの下部にある **[確認および作成]** ボタンをクリックします。 簡単な検証が行われ、検証の正常終了後のメッセージが緑色のバナーの下に表示されます。
1. **[作成]** ボタンをクリックします。
1. 次に、 **[モジュール ID ツイン]** を更新して、DebugLogsDirectory パラメーターがログ収集先のディレクトリを指すようにします。
    1. **[モジュール]** テーブルの下にある **[lvaEdge]** を選択します。
    1. **[モジュール ID ツイン]** リンクをクリックします。 これは、ページの上部にあります。 これにより、編集可能なウィンドウが開きます。
    1. **[desired key]\(必要なキー\)** の下に、次のキーと値のペアを追加します。

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. **[Save]** をクリックします。

1. 問題を再現します。
1. ポータルの [IoT Hub] ページから仮想マシンに接続します。
1. `/var/local/mediaservices/logs` フォルダーに移動し、このフォルダーのバイナリ コンテンツを zip 形式にして、Microsoft と共有します。 (これらのログ ファイルは、自己診断を目的としたものではありません。 これらは、Azure のエンジニアリングが問題を分析するためのものです)。

1. **[モジュール ID ツイン]** で、その値を再び *null* に設定することで、ログ収集を停止できます。 **[モジュール ID ツイン]** ページに戻り、次の内容をパラメーターとして更新します。

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>次のステップ

[チュートリアル:クラウドへのイベントベースのビデオ記録とクラウドからの再生](event-based-video-recording-tutorial.md)

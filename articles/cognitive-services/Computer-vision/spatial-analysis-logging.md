---
title: 空間分析コンテナーのテレメトリとログ
titleSuffix: Azure Cognitive Services
description: 空間分析では、各コンテナーに対する共通の構成フレームワークの洞察、ロギング、およびセキュリティの設定が用意されています。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589501"
---
# <a name="telemetry-and-troubleshooting"></a>テレメトリとトラブルシューティング

空間分析には、システムの正常性を監視し、問題の診断に役立つ一連の機能が含まれています。

## <a name="enable-visualizations"></a>視覚化の有効化

動画フレームで AI 分析情報イベントの視覚化を有効にするには、デスクトップ マシン上で `.debug` バージョンの[空間分析操作](spatial-analysis-operations.md)を使用する必要があります。 Azure Stack Edge デバイス上では視覚化を使用できません。 使用できるデバッグ操作は 4 つあります。

デバイスが Azure Stack Edge デバイスでない場合は、[デスクトップ マシン](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)のデプロイ マニフェスト ファイルを編集して、`DISPLAY` 環境変数に正しい値を使用します。 ホスト コンピューターの `$DISPLAY` 変数と一致している必要があります。 デプロイ マニフェストを更新した後、コンテナーを再デプロイします。

デプロイが完了したら、 `.Xauthority` ファイルをホストコンピューターからコンテナーにコピーし、再起動することが必要になる場合があります。 次のサンプルでは、 `peopleanalytics` は、ホストコンピューター上のコンテナーの名前になります。

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>システム正常性テレメトリの収集

Telegraf は、空間分析で動作するオープン ソースのイメージであり、Microsoft Container Registry で入手できます。 次の入力を受け取り、Azure Monitor に送信します。 指定されたカスタム入出力を使用して、telegraf モジュールを作成できます。 空間分析の telegraf モジュール構成は、配置マニフェスト (前述のリンク) の一部です。 このモジュールはオプションであり、必要がない場合は、マニフェストから削除できます。 

入力: 
1. 空間分析メトリック
2. ディスク メトリック
3. CPU メトリック
4. Docker メトリック
5. GPU メトリック

出力:
1. Azure Monitor

指定された空間分析の telegraf モジュールは、空間分析コンテナーから送信されたすべてのテレメトリ データを Azure Monitor に公開します。 サブスクリプションへの Azure Monitor の追加に関しては、[Azure Monitor](../../azure-monitor/overview.md) を参照してください。

Azure Monitor を設定した後、モジュールがテレメトリを送信できるようにするための資格情報を作成する必要があります。 Azure ポータルを使用して新しいサービス プリンシパルを作成するか、以下の Azure CLI コマンドを使用してサービス プリンシパルを作成できます。

> [!NOTE] 
> このコマンドを実行するには、サブスクリプションに対する所有者特権が必要です。 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

[Azure Stack Edge デバイス](https://go.microsoft.com/fwlink/?linkid=2142179)、[デスクトップ マシン](https://go.microsoft.com/fwlink/?linkid=2152270)、または [GPU 搭載 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) の配置マニフェストで、*telegraf* モジュールを探し、次の値を前の手順のサービス プリンシパル情報に置き換え、再デプロイします。

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

telegraf モジュールがデプロイされると、報告されたメトリックには、Azure Monitor サービスを使用して、または Azure portal の IoT Hub で **[監視]** を選択してアクセスできます。

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor テレメトリ レポート":::

### <a name="system-health-events"></a>システムの正常性イベント

| イベント名                  | 説明    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | ユーザーが空間分析モジュールの状態を "*実行中*" から "*停止*" に変更したときに送信されます。  |
| archon_error                | コンテナー内でいずれかのプロセスがクラッシュしたときに送信されます。 これは重大なエラーです。      |
| InputRate                   | グラフが動画入力を処理する速度。 5 分ごとに報告されます。              |
| OutputRate                  | グラフが AI Insights を出力する速度。 5 分ごとに報告されます。                |
| archon_allGraphsStarted     | すべてのグラフの起動が終了したときに送信されます。                                           |
| archon_configchange         | グラフの構成が変更されたときに送信されます。                                              |
| archon_graphCreationFailed  | 報告された `graphId` のグラフを開始できなかったときに送信されます。                           |
| archon_graphCreationSuccess | 報告された `graphId` のグラフが正常に開始されたときに送信されます。                      |
| archon_graphCleanup         | 報告された `graphId` のグラフをクリーンアップして終了したときに送信されます。                      |
| archon_graphHeartbeat       | スキルのすべてのグラフに対して 1 分ごとに送信されるハートビート。                                   |
| archon_apiKeyAuthFail       | 次の理由により、24 時間以上、Computer Vision リソース キーがコンテナーの認証に失敗したときに送信されます。クォータ外で、無効、オフラインです。 |
| VideoIngesterHeartbeat      | 動画が動画ソースからストリーミングされることを示すために、1 時間ごとに送信され、その時間のエラーの数を示します。 各グラフに対して報告されます。 |
| VideoIngesterState          | 動画のストリーミングのために、 *停止* または *開始* を報告します。  各グラフに対して報告されます。              |

##  <a name="troubleshooting-an-iot-edge-device"></a>IoT Edge デバイスのトラブルシューティング

`iotedge` コマンド ライン ツールを使用して、実行中のモジュールの状態とログを確認できます。 次に例を示します。
* `iotedge list`:実行中のモジュールの一覧を報告します。 
  また、 `iotedge logs edgeAgent` でエラーを確認することもできます。 `iotedge` が動かない場合は、`iotedge restart edgeAgent` を使用して再起動することができます。
* `iotedge logs <module-name>`
* 特定のモジュールを再起動するための `iotedge restart <module-name>` 

## <a name="collect-log-files-with-the-diagnostics-container"></a>診断コンテナーでログ ファイルを収集

空間分析では、ランタイムの問題を診断したり、サポート チケットに含めたりする場合に使用できる Docker デバッグ ログが生成されます。 空間分析診断モジュールは、Microsoft Container Registry からダウンロードできます。 [Azure Stack Edge デバイス](https://go.microsoft.com/fwlink/?linkid=2142179)、[デスクトップ マシン](https://go.microsoft.com/fwlink/?linkid=2152270)、または [GPU 搭載 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) のマニフェスト配置ファイルで、*diagnostics* モジュールを探します。

"Env" セクションで、次の構成を追加します。

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Azure Blob Storage など、リモー トエンドポイントにアップロードされたログを最適化するには、ファイル サイズを小さくすることをお勧めします。 推奨される Docker ログ構成については、次の例を参照してください。

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>ログ レベルの構成

ログ レベルの構成を使用すると、生成されるログの詳細を制御できます。 サポートされているログ レベルは、`none`、`verbose`、`info`、`warning` および `error` です。 ノードとプラットフォームの既定のログの詳細レベルは `info` です。 

ログ レベルをグローバルに変更するには、 `ARCHON_LOG_LEVEL` 環境変数を、許可されている値のいずれかに設定します。
次のように `platformLogLevel` と `nodesLogLevel` の値を設定することによって、グローバル、デプロイされたすべてのスキル、または特定のスキルごとに、IoT Edge Module Twin ドキュメントを通じて設定することもできます。

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>ログの収集

> [!NOTE]
> `diagnostics` モジュールは、ログの内容には影響しません。既存のログの収集、フィルター処理、およびアップロードにのみ役立ちます。
> このモジュールを使用するには、Docker API バージョン 1.40 以降が必要です。

[Azure Stack Edge デバイス](https://go.microsoft.com/fwlink/?linkid=2142179)、[デスクトップ マシン](https://go.microsoft.com/fwlink/?linkid=2152270)、または [GPU 搭載 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) のサンプル配置マニフェスト ファイルには、ログを収集してアップロードする `diagnostics` という名前のモジュールが含まれています。 このモジュールは既定で無効になっているため、ログにアクセスする必要がある場合は IoT Edge モジュール構成を使用して有効にする必要があります。 

`diagnostics` コレクションはオンデマンドで、IoT Edge ダイレクト メソッドによって制御され、ログを Azure Blob Storage に送信できます。

### <a name="configure-diagnostics-upload-targets"></a>診断アップロード ターゲットの構成

IoT Edge ポータルから、デバイスを選択し、 **diagnostics** モジュールを選択します。 [Azure Stack Edge デバイス](https://go.microsoft.com/fwlink/?linkid=2142179)、[デスクトップ マシン](https://go.microsoft.com/fwlink/?linkid=2152270)、[GPU 搭載 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) のサンプル配置マニフェスト ファイルで、`env` という名前の診断用の **[環境変数]** セクションを探し、次の情報を追加します。

**Azure Blob Storage へのアップロードの構成**

1. まだ作成していない場合は、自身の Azure Blob Storage アカウントを作成します。
2. Azure ポータルのストレージ アカウント用に **接続文字列** を取得します。 これは、**アクセス キー** にあります。
3. 空間分析ログは、*rtcvlogs* という名前の Blob Storage コンテナーに自動的にアップロードされます。ファイル名の形式は `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` です。

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>空間分析ログのアップロード

ログは、`diagnostics` モジュールの `getRTCVLogs` IoT Edge メソッドを使用してオンデマンドでアップロードされます。 


1. IoT Hub ポータルのページにアクセスし、 **[エッジ デバイス]** を選択して、デバイスと診断モジュールを選択します。 
2. モジュールの詳細ページに移動し、[***ダイレクト メソッド***] タブをクリックします。
3. [メソッド名] に `getRTCVLogs` と入力し、ペイロードに json 形式の文字列を入力します。 空のペイロードである `{}` を入力できます。 
4. 接続とメソッドのタイムアウトを設定し、 **[メソッドの呼び出し]** をクリックます。
5. ターゲット コンテナーを選択し、 **[ロギング構文]** で説明しているパラメーターを使用してペイロード json 文字列を作成します。 **[ メソッドの呼び出し]** をクリックして、要求を実行します。

>[!NOTE]
> 空のペイロードで `getRTCVLogs` メソッドを呼び出すと、デバイスにデプロイされているすべてのコンテナーの一覧が返されます。 メソッド名では、大文字と小文字が区別されます。 不正なメソッド名を指定すると、501 エラーが発生します。

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text=" getRTCVLogs メソッドの呼び出し":::
![getRTCVLogs ダイレクト メソッド ページ](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>ログの構文

次の表は、ログのクエリを実行するときに使用できるパラメーターを示しています。

| Keyword | 説明 | 既定値 |
|--|--|--|
| StartTime | 目的のログの開始時刻 (ミリ秒UTC)。 | `-1` の場合、コンテナーのランタイムの開始になります。 `[-1.-1]` の時間範囲が使用されている場合、API は過去 1 時間のログを返します。|
| EndTime | 目的のログの終了時刻 (ミリ秒UTC)。 | `-1`、現在の時刻。 `[-1.-1]` の時間範囲が使用されている場合、api は過去 1 時間のログを返します。 |
| ContainerId | ログをフェッチするターゲットとなるコンテナー。| `null` (コンテナー ID が存在しない場合)。 API は、使用可能なすべてのコンテナー情報を ID と共に返します。|
| DoPost | アップロード操作を実行します。 これが `false` に設定されている場合は、要求された操作を実行し、アップロードを実行せずにアップロード サイズを返します。 `true` に設定すると、選択したログの非同期アップロードが開始されます | `false` の場合、アップロードしないでください。|
| スロットル | バッチごとにアップロードするログのライン数を示します | `1000` の場合、このパラメーターを使用してポスト速度を調整します。 |
| フィルター | アップロードするログをフィルター処理します | `null` の場合、空間分析ログの構造 `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` に基づいて、キー値のペアとしてフィルターを指定できます。 例: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

次の表は、クエリ応答の属性の一覧になります。

| Keyword | 説明|
|--|--|
|DoPost| *true* または *false*。 ログがアップロードされたかどうかを示します。 ログをアップロードしない場合、API からは "***同期的に** _" 情報が返されます。 ログをアップロードする場合、API からは 200 が返されます。要求が有効な場合は、"_非同期に_" ログのアップロードが開始されます。|
|TimeFilter| ログに適用される時刻フィルターです。|
|ValueFilters| ログに適用されるキーワード フィルターです。 |
|TimeStamp| メソッド実行開始時刻。 |
|ContainerId| 対象のコンテナー ID。 |
|FetchCounter| ログ ラインの合計数。 |
|FetchSizeInByte| ログ ラインの合計量 (バイト)。 |
|MatchCounter| ログ ラインの有効数。 |
|MatchSizeInByte| ログ データの合計量 (バイト) |
|FilterCount| フィルターを適用した後のログ ラインの合計数。 |
|FilterSizeInByte| フィルターを適用した後のログ データの合計量 (バイト)。 |
|FetchLogsDurationInMiliSec| フェッチ操作の期間。 |
|PaseLogsDurationInMiliSec| フィルター操作の期間。 |
|PostLogsDurationInMiliSec| ポスト操作の期間。 |

#### <a name="example-request"></a>要求の例 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>応答の例 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

フェッチ ログのライン、時刻、およびサイズを確認します。これらの設定が適切であれば、 ***DoPost*** を `true` に置き換え、同じフィルターを使用してログを宛先にプッシュします。 

問題を解決するときに、Azure Blob Storage からログをエクスポートできます。 

## <a name="common-issues"></a>一般的な問題

モジュール ログで次のメッセージが表示される場合は、お使いの Azure サブスクリプションを承認される必要があることを意味する場合があります。 

"Container is not in a valid state. Subscription validation failed with status 'Mismatch'. Api Key is not intended for the given container type." (コンテナーは有効な状態ではありません。サブスクリプションの検証は、状態 '不一致' で失敗しました。API キーは、指定されたコンテナーの種類を対象としていません。)

詳細については、「[コンテナーを実行するための承認を要求する](spatial-analysis-container.md#request-approval-to-run-the-container)」を参照してください。

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Azure Stack Edge デバイスの問題解決

次のセクションでは、Azure Stack Edge デバイスの状態のデバッグと検証について説明します。

### <a name="access-the-kubernetes-api-endpoint"></a>Kubernetes API エンドポイントにアクセスします。 

1. デバイスのローカル UI で **[デバイス]** ページに移動します。 
2. **[デバイスのエンドポイント]** で、Kubernetes API サービス エンドポイントをコピーします。 このエンドポイントは `https://compute..[device-IP-address]` という形式の文字列です。
3. エンドポイント文字列を保存します。 後で Kubernetes クラスターにアクセスするために `kubectl` を構成するときにこれを使用します。

### <a name="connect-to-powershell-interface"></a>PowerShell インターフェイスに接続する

Windows クライアントからリモートで接続します。 Kubernetes クラスターが作成されたら、このクラスターを使用してアプリケーションを管理できます。 そのためには、デバイスの PowerShell インターフェイスに接続する必要があります。 デバイスにリモート接続する手順は、クライアントのオペレーティング システムによって異なります。 次のステップは、PowerShell を実行している Windows クライアント用です。

> [!TIP]
> * 開始する前に、Windows クライアントで Windows PowerShell 5.0 以降が実行されていることを確認してください。
> * PowerShell は、[Linux](/powershell/scripting/install/installing-powershell-core-on-linux) でも利用できます。

1. Windows PowerShell セッションを管理者として実行します。 
    1. Windows リモート管理サービスがクライアントで実行されていることを確認します。 コマンド プロンプトで、「`winrm quickconfig`」と入力します。

2. デバイスの IP アドレスに変数を割り当てます。 たとえば、`$ip = "<device-ip-address>"` のようにします。

3. 次のコマンドを使用して、デバイスの IP アドレスをクライアントの信頼できるホストの一覧に追加します。 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. デバイスの Windows PowerShell セッションを開始します。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. パスワードの入力を求められたら、入力します。 ローカル Web インターフェイスへのサインインに使用するパスワードと同じものを使用します。 ローカル Web インターフェイスの既定のパスワードは `Password1` です。 

### <a name="access-the-kubernetes-cluster"></a>Kubernetes クラスターへのアクセス

Kubernetes クラスターが作成されたら、`kubectl` コマンド ライン ツールを使用してこのクラスターにアクセスできます。

1. 新しい名前空間を作成します。 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. ユーザーを作成し、構成ファイルを取得します。 このコマンドは、Kubernetes クラスターの構成情報を出力します。 *config* という名前のファイルにこの情報をコピーし、保存します。ファイル拡張子を付けてファイルを保存しないでください。
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. *config* ファイルをローカル コンピューター上のユーザー プロファイルの *.kube* フォルダーに追加します。    

4. 作成したユーザーに名前空間を関連付けます。

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. 次のコマンドを使用して Windows クライアントに `kubectl` をインストールします。
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. システム上の hosts ファイルに DNS エントリを追加します。 
    1. メモ帳を管理者として実行し、`C:\windows\system32\drivers\etc\hosts` にある *hosts* ファイルを開きます。 
    2. ローカル UI の **[デバイス]** ページから、デバイスの IP アドレスと DNS ドメインを使用して、hosts ファイルにエントリを作成します。 使用するエンドポイントは、 次のようになります`https://compute.asedevice.microsoftdatabox.com/10.100.10.10`。

7. Kubernetes ポッドに接続できることを確認します。

    ```powershell
    kubectl get pods -n "iotedge"
    ```

コンテナー ログを取得するには、次のコマンドを実行します。

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>便利なコマンド

|command  |説明  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Kubernetes 構成ファイルを生成します。 このコマンドを使用する場合は、 *config* という名前のファイルに情報をコピーします。ファイル拡張子を付けてファイルを保存しないでください。        |
| `Get-HcsApplianceInfo` | デバイスに関する情報を返します。 |
| `Enable-HcsSupportAccess` | サポート セッションを開始するためのアクセス資格情報を生成します。 |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>空間分析のサポート チケットを申請する方法 

空間分析コンテナーで発生している問題の解決策を見つけるためのサポートが必要な場合は、次の手順に従ってサポート チケットに入力して送信してください。 Microsoft のチームから折り返し追加のガイダンスをお送りします。 

### <a name="fill-out-the-basics"></a>基本情報を入力する 
新しいサポート チケットは、[[新しいサポート リクエスト]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) ページで作成します。 画面の指示に従って、次のパラメーターを入力します。

![サポートの基本](./media/support-ticket-page-1-final.png)

1. **[問題の種類]** を `Technical` に設定します。
2. 空間分析コンテナーのデプロイに使用しているサブスクリプションを選択します。
3. `My services` を選択し、`Cognitive Services` をサービスとして選択します。
4. 空間分析コンテナーのデプロイに使用しているリソースを選択します。
5. 直面している問題の詳細な説明を簡潔に記述します。 
6. 問題の種類として `Spatial Analysis` を選択します。
7. ドロップダウン リストで、適切なサブタイプを選択します。
8. **[次へ]** を選択して、次のページに移動します。

### <a name="recommended-solutions"></a>推奨されるソリューション
次の段階では、選択した問題の種類に対し、推奨されるソリューションが提供されます。 これらのソリューションは最も一般的な問題を解決しますが、自分のソリューションには役に立たない場合は、 **[次へ: 詳細]** を選択して次の手順に進みます。

### <a name="details"></a>詳細
このページで、直面している問題に関する追加情報を追加します。 エンジニアが問題を絞り込むことができるように、できるだけ詳細を含めるようにしてください。 ご希望の連絡方法を選択し、問題の重大度を含めて、当社から適切に連絡がとれるようにします。次の手順に進むには、 **[次へ: 確認と作成]** を選択します。 

### <a name="review-and-create"></a>確認と作成 
サポート リクエストの詳細を見直し、すべての情報が正確で問題を効果的に表していることを確認します。 準備ができたら、 **[作成]** を選択して、当社チームにチケットを送信します。 チケットが受信されると、確認メールを受け取ります。当社チームは、できるだけ早くお客様に連絡を取ります。 Azure portal でチケットの状態を表示できます。

## <a name="next-steps"></a>次のステップ

* [人数カウント Web アプリをデプロイする](spatial-analysis-web-app.md)
* [空間分析の操作を構成する](./spatial-analysis-operations.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)

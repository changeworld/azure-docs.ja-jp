---
title: メトリックの収集と転送 - Azure IoT Edge
description: Azure Monitor を使用して、IoT Edge の組み込みのメトリックをリモートで監視します
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c7e7cc2434c9c55043ae6e504d868a103da35db
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226245"
---
# <a name="collect-and-transport-metrics-preview"></a>メトリックの収集と転送 (プレビュー)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Monitor と組み込みのメトリックの統合を使用すると、IoT Edge フリートをリモートで監視できます。 デバイスでこの機能を有効にするには、metrics-collector モジュールをデプロイに追加し、モジュールのメトリックを収集して Azure Monitor に転送するように構成します。

## <a name="architecture"></a>アーキテクチャ

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

[![IoT Hub を使用したメトリック監視アーキテクチャ](./media/how-to-collect-and-transport-metrics/arch.png)](./media/how-to-collect-and-transport-metrics/arch.png#lightbox)

| Note | 説明 |
|-|-|
|  1 | すべてのモジュールは、[Prometheus データ モデル](https://prometheus.io/docs/concepts/data_model/)を使用してメトリックを出力する必要があります。 [組み込みのメトリック](how-to-access-built-in-metrics.md)を使用すると、既定で広範なワークロードの可視化が可能になりますが、カスタム モジュールを使用し、シナリオ固有のメトリックを出力して監視ソリューションを強化することもできます。 オープンソース ライブラリを使用してカスタム モジュールをインストルメントする方法については、「[カスタム メトリックを追加する](how-to-add-custom-metrics.md)」を参照してください。 |
|  2️ | [metrics-collector モジュール](https://aka.ms/edgemon-metrics-collector)は、ワークロード モジュールのメトリックを収集し、それらをオフデバイスに転送する、Microsoft が提供する IoT Edge モジュールです。 メトリック コレクションでは "*プル*" モデルが使用されます。 収集頻度、エンドポイント、フィルターは、モジュールから送信されるデータを制御するように構成できます。 詳細については、この記事の後半の[「メトリック コレクターの構成」セクション](#metrics-collector-configuration)を参照してください。 |
|  3️ | metrics-collector モジュールからクラウドにメトリックを送信するには、2 つのオプションがあります。 "*オプション 1*" では、メトリックが Log Analytics に送信されます <sup>1</sup>。収集されたメトリックは、`InsightsMetrics` という固定のネイティブ テーブルを使用して、指定された Log Analytics ワークスペースに取り込まれます。 このテーブルのスキーマは、Prometheus メトリック データ モデルと互換性があります。<br><br> このオプションを使用するには、送信ポート 443 でワークスペースにアクセスする必要があります。 Log Analytics ワークスペースの ID とキーは、モジュール構成の一部として指定する必要があります。 制限付きネットワークで有効にするには、この記事の後半の「[制限付きネットワーク アクセス シナリオで有効にする](#enable-in-restricted-network-access-scenarios)」を参照してください。
|  4️ | 各メトリック エントリには、[モジュール構成](#metrics-collector-configuration)の一部として指定された `ResourceId` が含まれています。 この関連付けにより、指定したリソース (たとえば、IoT Hub) に自動的にメトリックがリンクされます。 その結果、[キュレーション IoT Edge ブック テンプレート](how-to-explore-curated-visualizations.md)では、リソースに対してクエリを発行することでメトリックを取得できます。 <br><br> また、この方法では、複数の IoT Hub で 1 つの Log Analytics ワークスペースをメトリック データベースとして安全に共有することもできます。 |
|  5️ | "*オプション 2*" では、メトリックが IoT Hub に送信されます <sup>1</sup>。コレクター モジュールは、収集されたメトリックを UTF-8 でエンコードされた JSON [device-to-cloud メッセージ](../iot-hub/iot-hub-devguide-messages-d2c.md)として `edgeHub` モジュール経由で送信するように構成できます。 このオプションを使用すると、IoT Hub エンドポイントへの外部アクセスのみを許可されている、ロックダウンされた IoT Edge デバイスの監視のロックが解除されます。 また、子デバイスが親デバイスにのみアクセスできる入れ子になった構成内の子 IoT Edge デバイスを監視することもできます。 |
|  6️ | IoT Hub を使用してメトリックをルーティングする場合は、(1 回限りの) クラウド ワークフローを設定する必要があります。 このワークフローでは、metrics-collector モジュールから受信したメッセージを処理し、Log Analytics ワークスペースに送信します。 このワークフローを使用すると、この省略可能なパスを介して受信したメトリックに対しても、[キュレーションされた視覚化](how-to-explore-curated-visualizations.md)と[アラート](how-to-create-alerts.md)の機能が有効になります。 このクラウド ワークフローを設定する方法の詳細については、「[IoT Hub を通じてメトリックをルーティングする](#route-metrics)」セクションを参照してください。 |

<sup>1</sup> 現在、"*オプション 1*" を使用して IoT Edge デバイスから Log Analytics にメトリックを直接転送する方が、必要なセットアップが最小限となる簡単なパスです。 特定のシナリオで IoT Edge デバイスが IoT Hub とのみ通信する "*オプション 2*" のアプローチが必要な場合を除いて、1 つ目のオプションが推奨されます。

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

[![IoT Central を使用したメトリック監視アーキテクチャ](./media/how-to-collect-and-transport-metrics/arch-iot-central.png)](./media/how-to-collect-and-transport-metrics/arch-iot-central.png#lightbox)

| Note | 説明 |
|-|-|
|  1 | すべてのモジュールは、[Prometheus データ モデル](https://prometheus.io/docs/concepts/data_model/)を使用してメトリックを出力する必要があります。 [組み込みのメトリック](how-to-access-built-in-metrics.md)を使用すると、既定で広範なワークロードの可視化が可能になりますが、カスタム モジュールを使用し、シナリオ固有のメトリックを出力して監視ソリューションを強化することもできます。 オープンソース ライブラリを使用してカスタム モジュールをインストルメントする方法については、「[カスタム メトリックを追加する](how-to-add-custom-metrics.md)」を参照してください。 |
|  2️ | [metrics-collector モジュール](https://aka.ms/edgemon-metrics-collector)は、ワークロード モジュールのメトリックを収集し、それらをオフデバイスに転送する、Microsoft が提供する IoT Edge モジュールです。 メトリック コレクションでは "*プル*" モデルが使用されます。 収集頻度、エンドポイント、フィルターは、モジュールから送信されるデータを制御するように構成できます。 詳細については、この記事の後半の[「メトリック コレクターの構成」セクション](#metrics-collector-configuration)を参照してください。 |
|  3️ | metrics-collector モジュールからクラウドにメトリックを送信するには、2 つのオプションがあります。 *オプション 1* ではメトリックが Log Analytics に送信されます。 収集されたメトリックは、`InsightsMetrics` という固定のネイティブ テーブルを使用して、指定された Log Analytics ワークスペースに取り込まれます。 このテーブルのスキーマは、Prometheus メトリック データ モデルと互換性があります。<br><br> このオプションを使用するには、送信ポート 443 でワークスペースにアクセスする必要があります。 Log Analytics ワークスペースの ID とキーは、モジュール構成の一部として指定する必要があります。 制限付きネットワークで有効にするには、この記事の後半の「[制限付きネットワーク アクセス シナリオで有効にする](#enable-in-restricted-network-access-scenarios)」を参照してください。
|  4️ | 各メトリック エントリには、[モジュール構成](#metrics-collector-configuration)の一部として指定された `ResourceId` が含まれています。 この関連付けにより、指定したリソース (たとえば、IoT Central) に自動的にメトリックがリンクされます。 その結果、[キュレーション IoT Edge ブック テンプレート](how-to-explore-curated-visualizations.md)では、リソースに対してクエリを発行することでメトリックを取得できます。 <br><br> また、この方法では、複数の IoT Central アプリケーションで 1 つの Log Analytics ワークスペースをメトリック データベースとして安全に共有することもできます。 |
|  5️ | *オプション 2* ではメトリックが IoT Central に送信されます。 このオプションを使用すると、オペレーターはメトリックとデバイス テレメトリを 1 つの場所で表示できます。 コレクター モジュールは、収集されたメトリックを UTF-8 でエンコードされた JSON [device-to-cloud メッセージ](../iot-hub/iot-hub-devguide-messages-d2c.md)として `edgeHub` モジュール経由で送信するように構成できます。 このオプションを使用すると、IoT Central エンドポイントへの外部アクセスのみを許可されている、ロックダウンされた IoT Edge デバイスの監視のロックが解除されます。 また、子デバイスが親デバイスにのみアクセスできる入れ子になった構成内の子 IoT Edge デバイスを監視することもできます。 |

---

## <a name="metrics-collector-module"></a>メトリック コレクター モジュール

Microsoft 提供の metrics-collector モジュールを IoT Edge デプロイに追加して、モジュール メトリックを収集し、それらを Azure Monitor に送信できます。 モジュール コードはオープンソースであり、[IoT Edge GitHub リポジトリ](https://github.com/Azure/iotedge/tree/release/1.1/edge-modules/azure-monitor)で利用できます。

metrics-collector モジュールは、Linux X64、ARM32、ARM64、Windows X64 (バージョン 1809) をサポートするマルチアーキテクチャの Docker コンテナー イメージとして提供されます。 これは、 **[`mcr.microsoft.com/azureiotedge-metrics-collector`](https://aka.ms/edgemon-metrics-collector)** で一般公開されています。

また、[IoT Edge のモジュール マーケットプレース](https://aka.ms/edgemon-module-marketplace)でも利用できます。

## <a name="metrics-collector-configuration"></a>メトリック コレクターの構成

metrics-collector のすべての構成は、環境変数を使用して行われます。 少なくとも、次の表で「**必須**」とマークされている変数を指定する必要があります。

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

| 環境変数名 | 説明 |
|-|-|
| `ResourceId` | デバイスが通信する IoT Hub のリソース ID。 詳細については、「[リソース ID](#resource-id)」セクションを参照してください。  <br><br>  **必須** <br><br> 既定値: *none* |
| `UploadTarget` |  メトリックを HTTPS 経由で Azure Monitor に直接送信するか、D2C メッセージとして IoT Hub に送信するかを制御します。 詳細については、「[ターゲットをアップロードする](#upload-target)」を参照してください。 <br><br>**AzureMonitor** または **IoTMessage** のいずれかを指定できます  <br><br>  **必要なし** <br><br> 既定値: *AzureMonitor* |
| `LogAnalyticsWorkspaceId` | [Log Analytics ワークスペース ID](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>*UploadTarget* が *AzureMonitor* の場合にのみ **必須** <br><br>既定値: *none* |
| `LogAnalyticsSharedKey` | [Log Analytics ワークスペース キー](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>*UploadTarget* が *AzureMonitor* の場合にのみ **必須**   <br><br> 既定値: *none* |
| `ScrapeFrequencyInSecs` | メトリックを収集して転送する定期的な実行間隔 (秒)。<br><br>  例: *600* <br><br>  **必要なし** <br><br> 既定値: *300* |
| `MetricsEndpointsCSV` | Prometheus メトリックを収集するエンドポイントのコンマ区切りの一覧。 メトリックを収集するモジュール エンドポイントはすべて、この一覧に含まれている必要があります。<br><br>  例: *http://edgeAgent:9600/metrics , http://edgeHub:9600/metrics, http://MetricsSpewer:9417/metrics* <br><br>  **必要なし** <br><br> 既定値: *http://edgeHub:9600/metrics , http://edgeAgent:9600/metrics* |
| `AllowedMetrics` | 収集するメトリックの一覧。他のメトリックはすべて無視されます。 空の文字列に設定すると無効になります。 詳細については、「[許可リストと禁止リスト](#allow-and-disallow-lists)」を参照してください。 <br><br>例: *metricToScrape{quantile=0.99}[endpoint=http://MetricsSpewer:9417/metrics ]*<br><br>  **必要なし** <br><br> 既定値: "*空*" |
| `BlockedMetrics` | 無視するメトリックの一覧。 *AllowedMetrics* をオーバーライドします。そのため、メトリックが両方の一覧に含まれている場合、そのメトリックは報告されません。 詳細については、「[許可リストと禁止リスト](#allow-and-disallow-lists)」を参照してください。 <br><br>   例: *metricToIgnore{quantile=0.5}[endpoint=http://VeryNoisyModule:9001/metrics ], docker_container_disk_write_bytes*<br><br>  **必要なし**  <br><br>既定値: "*空*" |
| `CompressForUpload` | メトリックをアップロードするときに圧縮を使用する必要があるかどうかを制御します。 すべてのアップロード ターゲットに適用されます。<br><br>  例: *true* <br><br>    **必要なし** <br><br>  既定値: *true* |
| `AzureDomain` | メトリックを Log Analytics に直接取り込むときに使用する最上位の Azure ドメインを指定します。 <br><br>  例: *azure.us* <br><br>    **必要なし** <br><br>  既定値: *azure.com* |

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

| 環境変数名 | 説明 |
|-|-|
| `ResourceId` | デバイスが通信する IoT Central アプリケーションのリソース ID。 詳細については、「[リソース ID](#resource-id)」セクションを参照してください。  <br><br>  **必須** <br><br> 既定値: *none* |
| `UploadTarget` |  メトリックを HTTPS 経由で Azure Monitor に直接送信するか、D2C メッセージとして IoT Central に送信するかを制御します。 詳細については、「[ターゲットをアップロードする](#upload-target)」を参照してください。 <br><br>**AzureMonitor** または **IoTMessage** のいずれかを指定できます  <br><br>  **必要なし** <br><br> 既定値: *AzureMonitor* |
| `LogAnalyticsWorkspaceId` | [Log Analytics ワークスペース ID](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>*UploadTarget* が *AzureMonitor* の場合にのみ **必須** <br><br>既定値: *none* |
| `LogAnalyticsSharedKey` | [Log Analytics ワークスペース キー](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。 <br><br>*UploadTarget* が *AzureMonitor* の場合にのみ **必須**   <br><br> 既定値: *none* |
| `ScrapeFrequencyInSecs` | メトリックを収集して転送する定期的な実行間隔 (秒)。<br><br>  例: *600* <br><br>  **必要なし** <br><br> 既定値: *300* |
| `MetricsEndpointsCSV` | Prometheus メトリックを収集するエンドポイントのコンマ区切りの一覧。 メトリックを収集するモジュール エンドポイントはすべて、この一覧に含まれている必要があります。<br><br>  例: *http://edgeAgent:9600/metrics , http://edgeHub:9600/metrics, http://MetricsSpewer:9417/metrics* <br><br>  **必要なし** <br><br> 既定値: *http://edgeHub:9600/metrics , http://edgeAgent:9600/metrics* |
| `AllowedMetrics` | 収集するメトリックの一覧。他のメトリックはすべて無視されます。 空の文字列に設定すると無効になります。 詳細については、「[許可リストと禁止リスト](#allow-and-disallow-lists)」を参照してください。 <br><br>例: *metricToScrape{quantile=0.99}[endpoint=http://MetricsSpewer:9417/metrics ]*<br><br>  **必要なし** <br><br> 既定値: "*空*" |
| `BlockedMetrics` | 無視するメトリックの一覧。 *AllowedMetrics* をオーバーライドします。そのため、メトリックが両方の一覧に含まれている場合、そのメトリックは報告されません。 詳細については、「[許可リストと禁止リスト](#allow-and-disallow-lists)」を参照してください。 <br><br>   例: *metricToIgnore{quantile=0.5}[endpoint=http://VeryNoisyModule:9001/metrics ], docker_container_disk_write_bytes*<br><br>  **必要なし**  <br><br>既定値: "*空*" |
| `CompressForUpload` | メトリックをアップロードするときに圧縮を使用する必要があるかどうかを制御します。 すべてのアップロード ターゲットに適用されます。<br><br>  例: *true* <br><br>    **必要なし** <br><br>  既定値: *true* |
| `AzureDomain` | メトリックを Log Analytics に直接取り込むときに使用する最上位の Azure ドメインを指定します。 <br><br>  例: *azure.us* <br><br>    **必要なし** <br><br>  既定値: *azure.com* |

IoT Edge と IoT Central の詳細については、「[Azure IoT Edge デバイスを Azure IoT Central アプリケーションに接続する](../iot-central/core/concepts-iot-edge.md)」を参照してください。

---

### <a name="resource-id"></a>Resource ID

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

metrics-collector モジュールには、IoT Edge デバイスが属する IoT Hub の Azure Resource Manager ID が必要です。 **ResourceID** 環境変数の値としてこの ID を指定します。

リソース ID の形式は次のとおりです。

```input
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Devices/IoTHubs/<iot hub name>
```

リソース ID は、Azure Portal 内の IoT Hub の **[プロパティ]** ページにあります。

:::image type="content" source="./media/how-to-collect-and-transport-metrics/resource-id.png" alt-text="IoT Hub のプロパティからリソース ID を取得します。":::

または、次のように [az resource show](/cli/azure/resource#az_resource_show) コマンドを使用して ID を取得します。

```azurecli-interactive
az resource show -g <resource group> -n <hub name> --resource-type "Microsoft.Devices/IoTHubs"
```

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

metrics-collector モジュールには、IoT Edge デバイスが属する IoT CentralI アプリケーションの Azure Resource Manager ID が必要です。 **ResourceID** 環境変数の値としてこの ID を指定します。

リソース ID の形式は次のとおりです。

```input
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.IoTCentral/IoTApps/<iot central app name>
```

リソース ID は、Azure Portal 内の IoT Central アプリケーションの **[プロパティ]** ページにあります。

:::image type="content" source="./media/how-to-collect-and-transport-metrics/resource-id-iot-central.png" alt-text="IoT Central のプロパティからリソース ID を取得します。":::

または、次のように [az resource show](/cli/azure/resource#az_resource_show) コマンドを使用して ID を取得します。

```azurecli-interactive
az resource show -g <resource group> -n <application name> --resource-type "Microsoft.IoTCentral/IoTApps"
```

---

### <a name="upload-target"></a>ターゲットをアップロードする

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

**UploadTarget** 構成オプションでは、メトリックを Azure Monitor に直接送信するか、IoT Hub に送信するかが制御されます。

**UploadTarget** を **IoTMessage** に設定した場合、モジュール メトリックは IoT メッセージとして発行されます。 これらのメッセージは、エンドポイント `/messages/modules/<module name>/outputs/metricOutput` から UTF8 でエンコードされた json として出力されます。 その形式は次のとおりです。

```json
[{
    "TimeGeneratedUtc": "<time generated>",
    "Name": "<prometheus metric name>",
    "Value": <decimal value>,
    "Label": {
        "<label name>": "<label value>"
    }
}, {
    "TimeGeneratedUtc": "2020-07-28T20:00:43.2770247Z",
    "Name": "docker_container_disk_write_bytes",
    "Value": 0.0,
    "Label": {
        "name": "AzureMonitorForIotEdgeModule"
    }
}]
```

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

**UploadTarget** 構成オプションでは、メトリックを Azure Monitor に直接送信するか、IoT Central に送信するかが制御されます。

**UploadTarget** を **IoTMessage** に設定した場合、モジュール メトリックは IoT メッセージとして発行されます。 これらのメッセージは、エンドポイント `/messages/modules/<module name>/outputs/metricOutput` から UTF8 でエンコードされた json として出力されます。 その形式は次のとおりです。

```json
[{
    "TimeGeneratedUtc": "<time generated>",
    "Name": "<prometheus metric name>",
    "Value": <decimal value>,
    "Label": {
        "<label name>": "<label value>"
    }
}, {
    "TimeGeneratedUtc": "2020-07-28T20:00:43.2770247Z",
    "Name": "docker_container_disk_write_bytes",
    "Value": 0.0,
    "Label": {
        "name": "AzureMonitorForIotEdgeModule"
    }
}]
```

---

### <a name="allow-and-disallow-lists"></a>許可リストと禁止リスト

`AllowedMetrics` と `BlockedMetrics` の構成オプションは、メトリック セレクターのスペースまたはコンマ区切りの一覧です。 メトリックは一覧と照合され、いずれかの一覧の 1 つまたは複数のメトリックに一致した場合は、追加または除外されます。

メトリック セレクターでは、[PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) クエリ言語のサブセットに似た形式を使用します。

```query
metricToSelect{quantile=0.5,otherLabel=~Re[ge]*|x}[http://VeryNoisyModule:9001/metrics]
```

メトリック セレクターは、次の 3 つの部分で構成されます。

メトリック名 (`metricToSelect`)。

* メトリック名には、ワイルドカード `*` (任意の文字列) と `?` (任意の 1 文字) を使用できます。 たとえば、`*CPU` は `maxCPU` と `minCPU` に一致しますが、`CPUMaximum` とは一致しません。 `???CPU` は `maxCPU` と `minCPU` に一致しますが、`maximumCPU` とは一致しません。
* メトリック セレクターのこのコンポーネントは必須です。

ラベルに基づくセレクター (`{quantile=0.5,otherLabel=~Re[ge]*|x}`)。

* 中かっこには複数のメトリック値を含めることができます。 複数の値はコンマで区切る必要があります。
* セレクター内の少なくともすべてのラベルが存在し、一致する場合にメトリックが一致します。
* PromQL と同様に、次の照合演算子を使用できます。
  * `=` は、指定された文字列と厳密に等しいラベルに一致します (大文字と小文字が区別されます)。
  * `!=` は、指定された文字列と厳密に等しくはないラベルに一致します。
  * `=~` は、ラベルを指定された正規表現に一致させます。 例: `label=~CPU|Mem|[0-9]*`
  * `!~` は、指定された正規表現に適合しないラベルに一致します。
  * 正規表現は完全に固定されます (各正規表現の先頭と末尾には、^ と $ が自動的に追加されます)。
  * メトリック セレクターのこのコンポーネントはオプションです。

エンドポイント セレクター (`[http://VeryNoisyModule:9001/metrics]`)。

* URL は、`MetricsEndpointsCSV` に示されている URL と完全に一致している必要があります。
* メトリック セレクターのこのコンポーネントはオプションです。

メトリックは、選択されたセレクターのすべての部分に一致する必要があります。 これは名前が一致し、"*なおかつ*" 同じラベルの値がすべて一致し、"*なおかつ*" 指定されたエンドポイントから取得されている必要があります。 たとえば、`mem{quantile=0.5,otherLabel=foobar}[http://VeryNoisyModule:9001/metrics]` はセレクター `mem{quantile=0.5,otherLabel=~foo|bar}[http://VeryNoisyModule:9001/metrics]` と一致しません。 複数のセレクターを使用すると、「かつ」のような動作ではなく、「または」のような動作が作成されます。

たとえば、カスタム メトリック `mem` にはモジュール `module1` のすべてのラベルを許可するが、`module2` の同じメトリックにはラベル `agg=p99` のみを許可するには、`AllowedMetrics` に次のセレクターを追加します。

```query
mem{}[http://module1:9001/metrics] mem{agg="p99"}[http://module2:9001/metrics]
```

または、すべてのラベルまたはエンドポイントにカスタム メトリック `mem` と `cpu` を許可するには、`AllowedMetrics` に次を追加します。

```query
mem cpu
```

## <a name="enable-in-restricted-network-access-scenarios"></a>制限付きネットワーク アクセス シナリオで有効にする

メトリックを Log Analytics ワークスペースに直接送信している場合は、次の URL への発信アクセスを許可します。

* `https://<LOG_ANALYTICS_WORKSPACE_ID>.ods.opinsights.azure.com/*`
* `https://<LOG_ANALYTICS_WORKSPACE_ID>.oms.opinsights.azure.com/*`

### <a name="proxy-considerations"></a>プロキシに関する考慮事項

metrics-collector モジュールは .NET Core で記述されています。 そのため、システム モジュールと同じガイダンスを使用して、[プロキシ サーバー経由の通信を許可](how-to-configure-proxy-support.md#configure-deployment-manifests)します。

ローカル モジュールからのメトリックの収集には、http プロトコルを使用します。 `NO_PROXY` 環境変数を設定することによって、プロキシ サーバー経由の通信からローカル通信を除外します。

`NO_PROXY` の値を、除外するホスト名のコンマ区切りの一覧に設定します。 ホスト名にはモジュール名を使用します。 例: *edgeHub,edgeAgent,myCustomModule*。

## <a name="route-metrics"></a>ルート メトリック

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

場合によっては、Log Analytics に直接送信するのではなく、IoT Hub でメトリックを取り込む必要があります。 たとえば、[入れ子になった構成で IoT Edge デバイス](tutorial-nested-iot-edge.md)を監視するときに、子デバイスが親デバイスの IoT Edge ハブにのみアクセスできるようにする場合などです。 もう 1 つの例は、IoT Hub に対してのみ送信ネットワーク アクセスを有効にした IoT Edge デバイスをデプロイする場合です。

このシナリオで監視を有効にするために、metrics-collector モジュールは、edgeHub モジュールを介して device-to-cloud (D2C) メッセージとしてメトリックを送信するように構成できます。 この機能を有効にするには、コレクター[構成](#metrics-collector-configuration)で `UploadTarget` 環境変数を `IoTMessage` に設定します。

>[!TIP]
>コレクター モジュールから IoT Hub にメトリック メッセージを配信するために、必ず edgeHub ルートを追加してください。 `FROM /messages/modules/replace-with-collector-module-name/* INTO $upstream` のように表示されます。

このオプションを選択すると、IoT Hub に到着したメトリック メッセージを Log Analytics ワークスペースに配信するための[追加のセットアップ](how-to-collect-and-transport-metrics.md#sample-cloud-workflow)が必要になります。 この設定がないと、[キュレーションされた視覚化](how-to-explore-curated-visualizations.md)や[アラート](how-to-create-alerts.md)など、統合のもう 1 つの部分は機能しません。

>[!NOTE]
>このオプションを使用する場合は、追加のコストに注意してください。 メトリック メッセージは、IoT Hub メッセージ クォータに対してカウントされます。 また、Log Analytics インジェストとクラウド ワークフロー リソースに対しても課金されます。

### <a name="sample-cloud-workflow"></a>サンプル クラウド ワークフロー

IoT Hub から Log Analytics にメトリック メッセージを配信するクラウド ワークフローは、[IoT Edge のログ記録と監視のサンプル](https://github.com/Azure-Samples/iotedge-logging-and-monitoring-solution#monitoring-architecture-reference)の一部として利用できます。 このサンプルは、既存のクラウド リソースにデプロイすることも、運用環境のデプロイの参照として使用することもできます。

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

場合によっては、Log Analytics に直接送信するのではなく、IoT Central でメトリックを取り込む必要があります。 たとえば、[入れ子になった構成で IoT Edge デバイス](tutorial-nested-iot-edge.md)を監視するときに、子デバイスが親デバイスの IoT Edge ハブにのみアクセスできるようにする場合などです。 もう 1 つの例は、IoT Central に対してのみ送信ネットワーク アクセスを有効にした IoT Edge デバイスをデプロイする場合です。

このシナリオで監視を有効にするために、metrics-collector モジュールは、edgeHub モジュールを介して device-to-cloud (D2C) メッセージとしてメトリックを送信するように構成できます。 この機能を有効にするには、コレクター[構成](#metrics-collector-configuration)で `UploadTarget` 環境変数を `IoTMessage` に設定します。

次のデプロイ マニフェストの例は、構成を示しています。

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            // ...
          },
          "edgeHub": {
            // ...
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            // ...
          },
          "AzureMonitorForIotEdgeModule": {
            "settings": {
                "image": "mcr.microsoft.com/azureiotedge-metrics-collector:1.0",
                "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"json-file\",\"Config\":{\"max-size\":\"4m\",\"max-file\":\"7\"}}}}"
            },
            "type": "docker",
            "env": {
              "UploadTarget": {
                "value": "IotMessage"
              },
              "ResourceId": {
                "value": "/subscriptions/{your subscription id}/IOTC/providers/Microsoft.IoTCentral/IoTApps/{your app name}"
              },
              "MetricsEndpointsCSV": {
                "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics"
              },
              "ScrapeFrequencyInSecs": {
                "value": "30"
              },
              "AllowedMetrics": {
                "value": ""
              },
              "BlockedMetrics": {
                "value": ""
              },
              "CompressForUpload": {
                "value": "false"
              },
              "TransformForIoTCentral": {
                "value": "true"
              }
            },
            "status": "running",
            "restartPolicy": "always",
            "version": "1.0"
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "temperatureupload": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO $upstream",
          "metricupload": "FROM /messages/modules/AzureMonitorForIotEdgeModule/outputs/metricOutput INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      // ...
    },
    "AzureMonitorForIotEdgeModule": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "scrapeFrequencySecs": 30,
        "metricsFormat": "Json",
        "syncTarget": "IoTHub"
      }
    }
  }
}
```

>[!TIP]
>コレクター モジュールから IoT Central にメトリック メッセージを配信するために、必ず edgeHub ルートを追加してください。 `FROM /messages/modules/replace-with-collector-module-name/* INTO $upstream` のように表示されます。

IoT Edge デバイスからのメトリックを IoT Central アプリケーションで表示するには:

* **IoT Edge メトリック標準インターフェイス** を継承インターフェイスとして [デバイス テンプレート](../iot-central/core/concepts-device-templates.md)に追加します。

    :::image type="content" source="media/how-to-collect-and-transport-metrics/add-metrics-interface.png" alt-text="IoT Edge メトリック標準インターフェイスを追加します。":::

* インターフェイスで定義されたテレメトリ値を使用して、IoT Edge デバイスを監視するために必要な[ダッシュボード](../iot-central/core/howto-manage-dashboards.md)をビルドします。

    :::image type="content" source="media/how-to-collect-and-transport-metrics/iot-edge-metrics-telemetry.png" alt-text="テレメトリとして使用可能な IoT Edge メトリック。":::

>[!NOTE]
>このオプションを使用する場合は、追加のコストに注意してください。 メトリック メッセージは、IoT Central メッセージ クォータに対してカウントされます。

---

## <a name="next-steps"></a>次のステップ

Azure Monitor によって有効になる[キュレーションされた視覚化](how-to-explore-curated-visualizations.md)の種類を探索します。

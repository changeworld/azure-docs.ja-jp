---
title: Azure Event Hubs を使用して Apache Spark アプリケーションのログとメトリックを収集する
description: このチュートリアルでは、Synapse Apache Spark 診断エミッタ拡張機能を使用して、Apache Spark アプリケーションのログ、イベント ログ、メトリックを Azure Event Hubs に送信する方法について説明します。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: 2370e3895cc70a4303c9d91d300b47b32913ac7a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131996321"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-event-hubs"></a>Azure Event Hubs を使用して Apache Spark アプリケーションのログとメトリックを収集する 

Synapse Apache Spark 診断エミッタ拡張機能は、Apache Spark アプリケーションで 1 つ以上の宛先 (Azure Log Analytics、Azure Storage、Azure Event Hubs など) にログ、イベント ログ、メトリックを送信できるようにするライブラリです。 

このチュートリアルでは、Synapse Apache Spark 診断エミッタ拡張機能を使用して、Apache Spark アプリケーションのログ、イベント ログ、メトリックを Azure Event Hubs に送信する方法について説明します。

## <a name="collect-logs-and-metrics-to-azure-event-hubs"></a>ログとメトリックを Azure Event Hubs に収集する

### <a name="step-1-create-an-azure-event-hub-instance"></a>手順 1: Azure Event Hub インスタンスを作成する

診断ログとメトリックを Azure Event Hubs に収集する場合は、既存の Azure Event Hubsインスタンスを使用できます。
または、お持ちでない場合は、[イベント ハブを作成する](../../event-hubs/event-hubs-create.md)ことができます。

### <a name="step-2-create-an-apache-spark-configuration-file"></a>手順 2: Apache Spark 構成ファイルの作成

`diagnostic-emitter-azure-event-hub-conf.txt` を作成し、そのファイルに次の内容をコピーします。 または、Apache Spark プール構成用の[サンプル テンプレート ファイル](https://go.microsoft.com/fwlink/?linkid=2169375)をダウンロードします。

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureEventHub
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.secret <connection-string>
```

構成ファイルで、パラメーター `<connection-string>` を入力します。
パラメーターの詳細については、[Azure EventHub の構成](#available-configurations)に関する記事を参照してください

### <a name="step-3-upload-the-apache-spark-configuration-file-to-apache-spark-pool"></a>手順 3: Apache Spark 構成ファイルを Apache Spark プールにアップロードする

1. Synapse Studio で Apache Spark プールに移動します **([管理] -> [Apache Spark プール])**
2. Apache Spark プールの右側にある **[...]** ボタンをクリックし、 **[Apache Spark 構成]** を選択します
3. **[アップロード]** をクリックし、".txt" 構成ファイルを選択して、 **[適用]** をクリックします。

## <a name="available-configurations"></a>利用可能な構成

| 構成                                                               | 説明                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `spark.synapse.diagnostic.emitters`                                         | 必須。 診断エミッタのコンマ区切りの宛先名。                                                                                                                              |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | 必須。 組み込みの宛先の種類。 Azure Event Hubs の宛先を有効にするには、値を `AzureEventHub` にする必要があります。                                                                                    |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | 省略可能。 コンマ区切りの選択されたログ カテゴリ。 指定できる値には、`DriverLog`、`ExecutorLog`、`EventLog`、`Metrics` が含まれます。 設定しない場合、既定値は **すべての** カテゴリです。              |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | 省略可能。 Azure Eventhub インスタンスの接続文字列。 このフィールドは、パターン `Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>;EntityPath=<PathName>` と一致する必要があります |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | `.secret` が指定されていない場合は必須です。 シークレット (接続文字列) が格納されている [Azure Key Vault](../../key-vault/general/overview.md) の名前。                                                                  |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | `.secret.keyVault` を指定した場合は必須。 シークレット (接続文字列) が格納されている Azure Key Vault のシークレット名。                                                                         |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.linkedService` | 省略可能。 Azure Key Vault のリンクされたサービス名。 Synapse パイプラインで有効にした場合、AKV からシークレットを取得するために必要です。 (AKV に対する読み取りアクセス許可が MSI に付与されていることを確認してください)。 |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | 省略可能。 コンマ区切りの Spark イベント名。収集するイベントを指定できます。 例: `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | 省略可能。 コンマ区切りの log4j ロガー名。収集するログを指定できます。 例: `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | 省略可能。 コンマ区切りの Spark メトリック名のサフィックス。収集するメトリックを指定できます。 例: `jvm.heap.used` |


> [!NOTE]
>
> Azure Eventhub インスタンス接続文字列には、常に `EntityPath` が含まれている必要があります。これは、Azure Event Hubs インスタンスの名前です。

## <a name="log-data-sample"></a>ログ データの例

JSON 形式のログ レコードの例を次に示します。

```json
{
    "timestamp": "2021-01-02T12:34:56.789Z",
    "category": "Log|EventLog|Metrics",
    "workspaceName": "<my-workspace-name>",
    "sparkPool": "<spark-pool-name>",
    "livyId": "<livy-session-id>",
    "applicationId": "<application-id>",
    "applicationName": "<application-name>",
    "executorId": "<driver-or-executor-id>",
    "properties": {
        // The message properties of logs, events and metrics.
        "timestamp": "2021-01-02T12:34:56.789Z",
        "message": "Registering signal handler for TERM",
        "logger_name": "org.apache.spark.util.SignalUtils",
        "level": "INFO",
        "thread_name": "main"
        // ...
    }
}
```

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>データ流出の防止が有効になっている Synapse ワークスペース

Azure Synapse Analytics ワークスペースでは、ワークスペースのデータ流出の防止を有効化することがサポートされています。 流出の防止を設定すると、ログとメトリックを宛先エンドポイントに直接送信できません。 このシナリオでは、さまざまな宛先エンドポイントに対応する[マネージド プライベート エンドポイント](../../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)を作成するか、[IP ファイアウォール規則を作成](../../synapse-analytics/security/synapse-workspace-ip-firewall.md)できます。





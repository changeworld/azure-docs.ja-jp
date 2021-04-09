---
title: Metrics Advisor Python クイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2ea2e24049a2ff57e284894bc13c44d617f3c661
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445085"
---
[リファレンス ドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [パッケージ (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Azure サブスクリプションを作成したら、Azure portal で <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor リソースを作成"  target="_blank">Metrics Advisor リソースを作成</a>して、Metrics Advisor インスタンスをデプロイします。  
* 時系列データを含む独自の SQL データベース。
  
> [!TIP]
> * Python Metrics Advisor のサンプルは、[GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples) にあります。
> * Metrics Advisor リソースによってサービス インスタンスがデプロイされ、使用できるようになるまでには、10 分から 30 分ほどかかる可能性があります。 デプロイに成功したら、 **[リソースに移動]** をクリックします。 デプロイ後、Web ポータルと REST API の両方で Metrics Advisor インスタンスの使用を開始できます。
> * REST API の URL は Azure portal で確認できます (自分のリソースの **[概要]** セクション)。 次のようになります。
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python ファイルを作成して次のライブラリをインポートします。

```python
import os
import datetime
```

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Metrics Advisor リソースが正常にデプロイされたら、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプションのキーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 <br><br>API キーを取得するには、[https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) にアクセスする必要があります。 リソースに適切な **ディレクトリ**、**サブスクリプション**、**ワークスペース** を選択し、 **[Get started]\(作業の開始\)** を選択します。 その後、[https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) から API キーを取得できるようになります。   
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>オブジェクト モデル

次のクラスによって、Metrics Advisor Python SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **使用目的**: <br> - インシデントの一覧表示 <br> - インシデントの根本原因の一覧表示 <br> - 元の時系列データと、サービスによってエンリッチされた時系列データの取得 <br> - アラートの一覧表示 <br> - モデルを調整するためのフィードバックの追加 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **次のことを実行できます**: <br> - データ フィードを管理する <br> - 異常検出構成を作成、設定、取得、一覧表示、削除する <br> - 異常アラート構成を作成、設定、取得、一覧表示、削除する <br> - フックを管理する  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Metrics Advisor によってデータソースから取り込まれるもの。`DataFeed` には、次の行が含まれます:** <br> - タイムスタンプ <br> - 0 個以上のディメンション <br> - 1 つ以上のメジャー  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | `DataFeedMetric` は、特定のビジネス プロセスの状態を監視および評価するために使用される定量化可能なメジャーです。 ディメンションに分割された複数の時系列値を組み合わせることができます。 たとえば、web health のメトリックには、user count と en-us market のディメンションが含まれている場合があります。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Metrics Advisor クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [データ フィードを追加する](#add-a-data-feed)
* [インジェストの状態を確認する](#check-the-ingestion-status)
* [検出構成とアラート構成を設定する](#configure-anomaly-detection)
* [アラート構成を作成する](#create-an-alert-configuration)
* [異常検出の結果を照会する](#query-the-alert)

## <a name="authenticate-the-client"></a>クライアントを認証する

この例のクライアントは、エンドポイントを使用する `MetricsAdvisorAdministrationClient` オブジェクトと、キーを含む `MetricsAdvisorKeyCredential` オブジェクトです。 このコード サンプルをコピーする必要はありません。 後で作成するメソッドによって、クライアントがインスタンス化されます。 代替クライアントは、`MetricsAdvisorClient` という名前です。このクライアントの詳細情報については、[リファレンス ドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient)を参照してください。

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>データ フィードを追加する

新しいメソッドで、次の例のように import ステートメントを作成します。 `sql_server_connection_string` を、実際の SQL Server 接続文字列に置き換え、`query` を、1 つのタイムスタンプでデータを返すクエリに置き換えます。 また、カスタム データに基づいて `DataFeedmetric` と `DataFeedDimension` の値を調整する必要もあります。

> [!IMPORTANT]
> クエリからは、各タイムスタンプで、ディメンションの各組み合わせに対して多くても 1 つのレコードが返される必要があります。 また、クエリによって返されるすべてのレコードで、タイムスタンプが同じになっている必要があります。 Metrics Advisor によってタイムスタンプごとにこのクエリが実行され、データが取り込まれます。 詳細と例については、[クエリの FAQ セクション](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)を参照してください。 

自分のキーとエンドポイントを使用してクライアントを作成し、`client.create_data_feed()` を使用して名前、ソース、粒度、およびスキーマを構成します。 さらに、インジェスト時間、ロールアップ設定なども指定できます。


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>インジェストの状態を確認する

新しいメソッドで、次の例のように import ステートメントを作成します。 `data_feed_id` を、作成したデータ フィードの ID に置き換えます。 自分のキーとエンドポイントを使用してクライアントを作成し、`client.list_data_feed_ingestion_status()` を使用してインジェストの進行状況を取得します。 最後のアクティブや成功のタイムスタンプなどの詳細を出力します。


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>異常検出を構成する

新しいメソッドで、次の例のように import ステートメントを作成します。 `metric_id` を、構成するメトリックの ID に置き換えます。 自分のキーとエンドポイントを使用してクライアントを作成し、`client.create_detection_configuration` を使用して新しい検出構成を作成します。 しきい値の条件によって、異常検出のパラメーターを指定します。

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>フックを作成する

新しいメソッドで、次の例のように import ステートメントを作成します。 自分のキーとエンドポイントを使用してクライアントを作成し、`client.create_hook()` を使用してフックを作成します。 説明、アラートの送信先の電子メール一覧、およびアラートを受け取るための外部リンクを入力します。  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>アラート構成を作成する

新しいメソッドで、次の例のように import ステートメントを作成します。 `detection_configuration_id` を、異常検出構成の ID に置き換え、`hook_id` を、前に作成したフックに置き換えます。 自分のキーとエンドポイントを使用してクライアントを作成し、`client.create_alert_configuration()` を使用してアラート構成を作成します。 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>アラートに対してクエリを実行する

新しいメソッドで、次の例のように import ステートメントを作成します。 `alert_id` をアラートの ID に置き換え、`alert_config_id` をアラート構成 ID に置き換えます。 自分のキーとエンドポイントを使用してクライアントを作成し、`client.list_anomalies` を使用してアラートの異常を一覧表示します。 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `python` コマンドを使用して、アプリケーションを実行します。

```console
python quickstart-file.py
```
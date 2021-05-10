---
title: チュートリアル - Azure Synapse Spark アプリケーション レベルのメトリックの接続と監視
description: チュートリアル - Synapse Prometheus コネクタを使用して、既存のオンプレミス Prometheus サーバーを Azure Synapse ワークスペースと統合し、ほぼリアルタイムの Azure Spark アプリケーション メトリックを取得する方法について説明します。
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: d22975199eedae353f2dc12588671ae4b54c85ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109320"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>チュートリアル: Azure Synapse Spark アプリケーション レベルのメトリックの接続と監視

## <a name="overview"></a>概要

このチュートリアルでは、Synapse Prometheus コネクタを使用して、既存のオンプレミス Prometheus サーバーを Azure Synapse ワークスペースと統合し、ほぼリアルタイムの Azure Spark アプリケーション メトリックを取得する方法について説明します。 

このチュートリアルでは、Azure Synapse REST メトリック API についても説明します。 独自の監視および診断ツールキットを構築したり、監視システムと統合したりするために、REST API を使用して Spark アプリケーション メトリック データを取得することができます。

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>オンプレミスの Prometheus サーバーで Azure Synapse Prometheus コネクタを使用する

[Azure Synapse Prometheus コネクタ](https://github.com/microsoft/azure-synapse-spark-metrics)はオープンソースのプロジェクトです。 Synapse Prometheus コネクタでは、以下を可能にするために、ファイルベースのサービス探索方法を使用します。
 - AAD サービス プリンシパルを介して Synapse ワークスペースに対する認証を行います。
 - ワークスペースの Apache Spark アプリケーションの一覧を取得します。 
 - Prometheus のファイルベースの構成を使用して Spark アプリケーションのメトリックを取得します。 

### <a name="1-prerequisite"></a>1.前提条件

Prometheus サーバーが Linux VM にデプロイされている必要があります。

### <a name="2-create-a-service-principal"></a>2.サービス プリンシパルの作成

オンプレミスの Prometheus サーバーで Azure Synapse Prometheus コネクタを使用するには、次の手順に従ってサービス プリンシパルを作成する必要があります。

#### <a name="21-create-a-service-principal"></a>2.1 サービス プリンシパルを作成する

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

結果は次のようになります。

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

アプリ ID、パスワード、テナント ID をメモしておきます。

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2.2 対応するアクセス許可を、前の手順で作成したサービス プリンシパルに追加する

![SRBAC アクセス許可付与のスクリーンショット](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Synapse Administrator として [Azure Synapse Analytics ワークスペース](https://web.azuresynapse.net/) にログインします

2. Synapse Studio の左側のペインで、 **[管理] > [アクセス制御]** を選択します

3. 左上にある [追加] ボタンをクリックして、**ロールの割り当てを追加** します

4. [スコープ] で、 **[ワークスペース]** を選択します

5. [ロール] で、 **[Synapse Compute Operator]** を選択します

6. [ユーザーの選択] で、 **<service_principal_name>** を入力してサービス プリンシパルをクリックします

7. **[適用]** をクリックします (アクセス許可が有効になるまで 3 分間待機します。)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3.Azure Synapse Prometheus コネクタをダウンロードする

コマンドを使用して、Azure Synapse Prometheus コネクタをインストールします。

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4.Azure Synapse ワークスペースの構成ファイルを作成する

config フォルダーに config.yaml ファイルを作成し、workspace_name、tenant_id, service_principal_name、service_principal_password の各フィールドに入力します。
yaml 構成では、複数のワークスペースを追加できます。

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5.Prometheus 構成を更新する

Prometheus の scrape_config で次の構成セクションを追加し、<your_workspace_name> はワークスペース名に、<path_to_synapse_connector> は、クローンした synapse-prometheus-connector フォルダーに置き換えます

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6.Prometheus サーバー VM でコネクタを開始する

次のようにして、Prometheus サーバー VM でコネクタ サーバーを起動します。

```
python main.py
```

数秒待つと、コネクタが動作を開始します。 Prometheus サービス検出ページで "synapse-prometheus-connector" を確認できます。

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Azure Synapse Prometheus または REST メトリック API を使用してメトリック データを収集する

### <a name="1-authentication"></a>1.認証
クライアント資格情報フローを使用して、アクセス トークンを取得できます。 メトリック API にアクセスするには、サービス プリンシパルに対する Azure AD アクセス トークン (API にアクセスするための適切なアクセス許可があるもの) を取得する必要があります。

| パラメーター     | 必須 | 説明                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | True     | Azure サービス プリンシパル (アプリケーション) のテナント ID                                                          |
| grant_type    | True     | 要求されている付与の種類を指定します。 クライアント資格情報付与フローでは、値は client_credentials である必要があります。 |
| client_id     | True     | Azure portal または Azure CLI で登録した、アプリケーションのアプリケーション (サービス プリンシパル) ID。        |
| client_secret | True     | アプリケーションに対して生成されたシークレット (サービス プリンシパル)                                                  |
| resource      | True     | Synapse リソース URI ("https://dev.azuresynapse.net" である必要があります)                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

応答は次のようになります。

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2.Azure Synapse ワークスペースで実行中のアプリケーションを一覧表示する

[監視 - Spark ジョブの一覧の取得](/rest/api/synapse/data-plane/monitoring/getsparkjoblist)に関するドキュメントに従って、Synapse ワークスペースの Spark アプリケーションの一覧を取得します。


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3.Prometheus または REST API を使用して Spark アプリケーションのメトリックを収集する


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Prometheus API を使用して Spark アプリケーションのメトリックを収集する

Prometheus API によって、指定された Spark アプリケーションの最新のメトリックを取得します

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| パラメーター          | 必須 | 説明                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | True     | ワークスペース開発エンドポイント (例: https://myworkspace.dev.azuresynapse.net )。 |
| livyApiVersion     | True     | 要求の有効な api-version。 現時点では 2019-11-01-preview                    |
| sparkPoolName      | True     | Spark プールの名前。                                                                   |
| sessionID          | True     | セッションの識別子。                                                               |
| sparkApplicationId | True     | Spark アプリケーション ID                                                                      |

サンプルの要求: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

応答例:

状態コード: 200 応答は次のようになります。

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>REST API を使用して Spark アプリケーションのメトリックを収集する

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| パラメーター          | 必須 | 説明                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | True     | ワークスペース開発エンドポイント (例: https://myworkspace.dev.azuresynapse.net )。 |
| livyApiVersion     | True     | 要求の有効な api-version。 現時点では 2019-11-01-preview                    |
| sparkPoolName      | True     | Spark プールの名前。                                                                   |
| sessionID          | True     | セッションの識別子。                                                               |
| sparkApplicationId | True     | Spark アプリケーション ID                                                                      |

要求のサンプル

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

サンプルの応答の状態コード: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4.独自の診断および監視ツールを構築する

Prometheus API と REST API は、実行中の Spark アプリケーションに関する豊富なメトリック データを提供します。Prometheus API と REST API を使用して、アプリケーション関連のメトリック データを収集できます。 また、よりニーズに合った独自の診断および監視ツールを構築できます。

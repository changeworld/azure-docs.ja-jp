---
title: Azure Container Registry を監視する
description: Azure Monitor の機能を使用して Azure コンテナー レジストリを監視する方法については、最初にこちらを参照してください
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 08/13/2021
ms.openlocfilehash: ff82571b791f20cf46150be06e583af29f13dbd5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586970"
---
# <a name="monitor-azure-container-registry"></a>Azure Container Registry を監視する

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Container Registry によって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

## <a name="monitor-overview"></a>監視の概要

Azure portal の各レジストリ の **[概要]** ページには、プッシュ操作やプル操作など、最近のリソースの使用状況とアクティビティの簡単なビューが含まれています。 この概要情報は役立ちますが、そこに表示されるデータはごくわずかです。 

:::image type="content" source="media/monitor-service/metrics-overview.png" alt-text="レジストリ メトリックの概要"::: 

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Azure Container Registry では、[Azure Monitor](../azure-monitor/overview.md) を使用して監視データが作成されます。Azure Monitor は Azure のフルスタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。

まず「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」の記事にある次の概念の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事を基に、Azure Container Registry に関して収集される特定のデータについて説明します。また、Azure のツールを使用してデータの収集を構成し、このデータを分析するための例を示します。

## <a name="monitoring-data"></a>データの監視 

Azure Container Registry では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 

Azure Container Registry によって作成されるメトリックとログの詳細については、「[Azure Container Registry の監視データのリファレンス](monitor-service-reference.md)」を参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 Azure Container Registry についてのカテゴリの一覧については、[Azure Container Registry 監視データ リファレンス](monitor-service-reference.md#resource-logs)に関する記事を参照してください。

> [!TIP]
> ポータルでレジストリに移動することで、レジストリの診断設定を作成することもできます。 メニューで、 **[監視]** の **[診断設定]** を選択します。

次の図は、レジストリの診断設定を有効にするときのオプションを示しています。

:::image type="content" source="media/monitor-service/diagnostic-settings.png" alt-text="Container Registry の診断設定":::

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics-preview"></a>メトリックの分析 (プレビュー)

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、Azure コンテナー レジストリのメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 

> [!TIP]
> ポータルでレジストリに移動することで、メトリックス エクスプローラーに移動することもできます。 メニューで **[監視]** の **[メトリック (プレビュー)]** を選択します。

Azure Container Registry について収集されるプラットフォーム メトリックの一覧については、[Azure Container Registry の監視データのリファレンスのメトリック](monitor-service-reference.md#metrics)に関する説明を参照してください  

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

### <a name="azure-cli"></a>Azure CLI

次の Azure CLI コマンドを使用して、Azure Container Registry のメトリックに関する情報を取得できます。

* [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) - メトリックの定義とディメンションの一覧が表示されます
* [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) - メトリックの値が取得されます

### <a name="rest-api"></a>REST API 

Azure Container Registry のメトリックに関する情報をプログラムで取得するには、Azure Monitor REST API を使用します。

* [メトリックの定義とディメンションの一覧を表示する](/rest/api/monitor/metricdefinitions/list)
* [メトリック値の取得](/rest/api/monitor/metrics/list)

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。  

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)に関する記事をご覧ください。 Azure Container Registry のリソース ログのスキーマについては、[Azure Container Registry データ リファレンス](monitor-service-reference.md#schemas)に関する記事を参照してください。 

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、Azure のプラットフォーム ログであり、サブスクリプション レベルのイベントの分析情報が提供されます。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  

Azure Container Registry について収集されるリソース ログの種類の一覧については、「[Azure Container Registry の監視データのリファレンス](monitor-service-reference.md#resource-logs)」を参照してください。  

Azure Monitor ログによって使用され、Log Analytics によってクエリ可能なテーブルの一覧については、[Azure Container Registry データ監視のリファレンス](monitor-service-reference.md#azure-monitor-logs-tables)に関する記事を参照してください。  

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> **Azure Container Registry** のメニューから **[ログ]** を選択すると、クエリのスコープが現在のレジストリに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のレジストリのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

たとえば、次のクエリを使用すると、**ContainerRegistryRepositoryEvents** テーブルから直近の 24 時間のデータを取得できます。

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

次の画像は、サンプル出力を示しています。

:::image type="content" source="media/monitor-service/azure-monitor-query.png" alt-text="ログ データのクエリ":::

次に、レジストリ リソースの監視に使用できるクエリを示します。 

### <a name="error-events-from-the-last-hour"></a>過去 1 時間のエラー イベント

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>直近の 100 件のレジストリ イベント

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>リポジトリを削除したユーザーまたはオブジェクトの ID

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>タグを削除したユーザーまたはオブジェクトの ID

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>リポジトリ レベルの操作エラー

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>レジストリ認証エラー

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。


<!-- only include next line if applications run on your service and work with App Insights. 

If you are creating or running an application which run on <*service*> [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) may offer additional types of alerts.
-->

次の表に、Azure Container Registry の一般的および推奨される警告ルールを示します。

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->
| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
| メトリック | シグナル: ストレージが使用された<br/>演算子:より大きい<br/>集計の種類: 平均<br/>しきい値: 5 GB|  使用されたレジストリ ストレージが指定した値を超えた場合にアラートを生成します。|
| | | |

### <a name="example-send-email-alert-when-registry-storage-used-exceeds-a-value"></a>例: 使用されたレジストリ ストレージが値を超えた場合、メール アラートを送信する

1. Azure portal で、お使いのレジストリに移動します。
1. **[監視]** の **[メトリック (プレビュー)]** を選択します。
1. メトリックス エクスプローラーの **[メトリック]** で、 **[使用済みストレージ]** を選択します。
1. **[新しいアラート ルール]** を選択します。
1. **[スコープ]** で、アラート ルールを作成するレジストリ リソースを確認します。
1. **[条件]** で、 **[条件の追加]** を選択します。
    1. **[シグナル名]** で、 **[使用済みストレージ]** を選択します。
    1. **[グラフの期間]** で、 **[直近 24 時間]** を選択します。
    1. **[アラート ロジック]** の **[しきい値]** で、*5* などの値を選択します。 **[単位]** で、*GB* などの値を選択します。
    1. 残りの設定は既定値のままにして、 **[完了]** を選択します。
1. **[アクション]** で、 **[アクション グループの追加]**  >  **[+ アクション グループの作成]** を選択します。
    1. アクション グループの詳細を入力します。
    1. **[通知]** タブで **[Email/SMS message/Push/Voice]\(メール/SMS メッセージ/プッシュ/音声\)** を選択し、 *admin@contoso.com* などの受信者を入力します。 **[Review + create]\(レビュー + 作成\)** を選択します。
1. アラート ルールの名前と説明を入力し、重大度を選択します。
1. **[アラート ルールの作成]** を選択します。

## <a name="next-steps"></a>次のステップ

- Azure Container Registry によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、「[Azure Container Registry の監視データのリファレンス](monitor-service-reference.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
- レジストリ内のストレージ使用量やその他のリソース消費のスナップショットを取得する方法については、[レジストリの使用量の表示](container-registry-skus.md#show-registry-usage)に関するページを参照してください。
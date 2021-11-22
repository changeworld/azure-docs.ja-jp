---
title: Microsoft Sentinel クエリとアクティビティの監査 | Microsoft Docs
description: この記事では、Microsoft Sentinel で実行されるクエリとアクティビティを監査する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: d1524139049b77cfeeff58563904489ae49728ef
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518054"
---
# <a name="audit-microsoft-sentinel-queries-and-activities"></a>Microsoft Sentinel のクエリとアクティビティを監査する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel ワークスペースで実行されるクエリとアクティビティの監査データを表示する方法について説明します。たとえば、セキュリティ運用 (SOC) ワークスペースでの内部および外部のコンプライアンス要件に対応します。

Microsoft Sentinel では次のアクセスが提供されます。

- **AzureActivity** テーブル。警告ルールの編集など、Microsoft Sentinel で実行されるすべてのアクションに関する詳細を示します。 **AzureActivity** テーブルでは、特定のクエリ データは記録されません。 詳細については、「[Azure アクティビティ ログを使用した監査](#auditing-with-azure-activity-logs)」を参照してください。

- **LAQueryLogs** テーブル。Microsoft Sentinel から実行されるクエリを含め、Log Analytics で実行されるクエリに関する詳細を示します。 詳細については、「[LAQueryLogs を使用した監査](#auditing-with-laquerylogs)」を参照してください。

> [!TIP]
> この記事で説明されている手動クエリに加えて、Microsoft Sentinel には、SOC 環境でのアクティビティの監査に役立つ組み込みブックが用意されています。
>
> Microsoft Sentinel の **[ブック]** 領域で、**ワークスペース監査** ブックを検索します。

## <a name="auditing-with-azure-activity-logs"></a>Azure アクティビティ ログを使用した監査

Microsoft Sentinel の監査ログは、[Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に保持されます。ここでは、Microsoft Sentinel ワークスペースで実行されるすべてのアクションが **AzureActivity** テーブルに含まれています。

Microsoft Sentinel で SOC 環境のアクティビティを監査する場合は、**AzureActivity** テーブルを使用できます。

**AzureActivity テーブルに対してクエリを実行するには**:

1. [Azure アクティビティ](./data-connectors-reference.md#azure-activity) データソースを接続して、"AzureActivity" という名前の **[ログ]** 画面の新しいテーブルに対してストリーミング監査イベントを開始します。

1. 次に、他のテーブルの場合と同様に、KQL を使用してデータに対してクエリを実行します。

    **AzureActivity** テーブルには、Microsoft Sentinel を含むさまざまなサービスのデータが含まれています。 Microsoft Sentinel のデータのみをフィルター処理するには、次のコードを使ってクエリを開始します。

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    たとえば、特定の分析ルールを最後に編集したユーザーを確認するには、次のクエリを使用します (`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` を、確認するルールのルール ID に置き換えます)。

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

レポートに必要な内容に応じて、クエリにパラメーターをさらに追加して、**AzureActivities** テーブルを詳しく調べます。 次のセクションでは、**AzureActivity** テーブル データを使用して監査するときに使用するその他のサンプル クエリについて説明します。

詳細については、「[Azure アクティビティ ログに含まれる Microsoft Sentinel データ](#microsoft-sentinel-data-included-in-azure-activity-logs)」を参照してください。

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>過去 24 時間に特定のユーザーによって実行されたすべてのアクションを検索する

次の **AzureActivity** テーブル クエリでは、過去 24 時間に特定の Azure AD ユーザーによって実行されたすべてのアクションが一覧表示されます。

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>すべての削除操作の検索

次の **AzureActivity** テーブル クエリでは、Microsoft Sentinel ワークスペースで実行されたすべての削除操作が一覧表示されます。

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
```

### <a name="microsoft-sentinel-data-included-in-azure-activity-logs"></a>Azure アクティビティ ログに含まれる Microsoft Sentinel データ

Microsoft Sentinel の監査ログは、[Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に保持され、次の種類の情報が含まれます。

|Operation  |情報の種類  |
|---------|---------|
|**作成日時**     |アラート ルール <br> ケース コメント <br>インシデント コメント <br>保存した検索条件<br>ウォッチリスト    <br>Workbooks     |
|**削除済み**     |アラート ルール <br>ブックマーク <br>データ コネクタ <br>インシデント <br>保存した検索条件 <br>設定 <br>脅威インテリジェンス レポート <br>ウォッチリスト      <br>Workbooks <br>ワークフロー  |
|**更新**     |  アラート ルール<br>ブックマーク <br> ケース <br> データ コネクタ <br>インシデント <br>インシデント コメント <br>脅威インテリジェンス レポート <br> Workbooks <br>ワークフロー       |
|     |         |

また、Azure アクティビティ ログを使用して、ユーザーの認可とライセンスを確認することもできます。

たとえば、次の表は、Azure アクティビティ ログで検出された選択済みの操作と、ログ データのプル元である特定のリソースを示しています。

|操作の名前| リソースの種類|
|----|----|
|ブックの作成または更新 |Microsoft.Insights/workbooks|
|ブックの削除 |Microsoft.Insights/workbooks|
|ワークフローの設定 |Microsoft.Logic/workflows|
|ワークフローの削除 |Microsoft.Logic/workflows|
|保存した検索条件を作成する |Microsoft.OperationalInsights/workspaces/savedSearches|
|保存した検索条件の削除 |Microsoft.OperationalInsights/workspaces/savedSearches|
|警告ルールの更新 |Microsoft.SecurityInsights/alertRules|
|警告ルールの削除 |Microsoft.SecurityInsights/alertRules|
|警告ルールの応答アクションの更新 |Microsoft.SecurityInsights/alertRules/actions|
|警告ルールの応答アクションの削除 |Microsoft.SecurityInsights/alertRules/actions|
|ブックマークの更新 |Microsoft.SecurityInsights/bookmarks|
|ブックマークの削除 |Microsoft.SecurityInsights/bookmarks|
|ケースの更新 |Microsoft.SecurityInsights/Cases|
|ケース調査の更新 |Microsoft.SecurityInsights/Cases/investigations|
|ケース コメントの作成 |Microsoft.SecurityInsights/Cases/comments|
|データ コネクタの更新 |Microsoft.SecurityInsights/dataConnectors|
|データ コネクタの削除 |Microsoft.SecurityInsights/dataConnectors|
|設定の更新 |Microsoft.SecurityInsights/settings|
| | |

詳細については、「[Azure アクティビティ ログ イベント スキーマ](../azure-monitor/essentials/activity-log-schema.md)」を参照してください。

## <a name="auditing-with-laquerylogs"></a>LAQueryLogs を使用した監査

**LAQueryLogs** テーブルは、Log Analytics で実行されるログ クエリの詳細を示します。 Log Analytics は Microsoft Sentinel の基になるデータ ストアとして使われるため、Microsoft Sentinel ワークスペースで LAQueryLogs データを収集するようにシステムを構成できます。

LAQueryLogs データには、次のような情報が含まれます。

- クエリが実行された日時
- Log Analytics でクエリを実行したユーザー
- Microsoft Sentinel など、Log Analytics でクエリの実行に使われたツール
- クエリ テキスト自体
- 各クエリ実行のパフォーマンス データ

> [!NOTE]
> - **LAQueryLogs** テーブルには、Microsoft Sentinel の [ログ] ブレードで実行されたクエリのみが含まれます。 **調査グラフ** を使って、または Microsoft Sentinel の **[検索]** ページで、スケジュールされた分析ルールによって実行されるクエリは含まれません。
> - クエリが実行されてから、**LAQueryLogs** テーブルにデータが設定されるまでに、短い遅延が発生する場合があります。 **LAQueryLogs** テーブルの監査データに対してクエリを実行するには、約 5 分間待つことをお勧めします。

**LAQueryLogs テーブルに対してクエリを実行するには、次の手順に従います**。

1. 現在の Log Analytics ワークスペースでは、**LAQueryLogs** テーブルは既定で有効になっていません。 Microsoft Sentinel で監査するときに **LAQueryLogs** データを使うには、まず、Log Analytics ワークスペースの **[診断設定]** 領域で **LAQueryLogs** を有効にします。

    詳細については、「[Azure Monitor ログでのクエリの監査](../azure-monitor/logs/query-audit.md)」を参照してください。

1. 次に、他のテーブルの場合と同様に、KQL を使用してデータに対してクエリを実行します。

    たとえば、次のクエリは、過去 1 週間に実行されたクエリの数を日単位で示しています。

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

次のセクションでは、Microsoft Sentinel を使って SOC 環境でアクティビティを監査するときに、**LAQueryLogs** テーブルで実行するサンプル クエリをさらに示します。

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>応答が "OK" ではない場合に実行されるクエリの数

次の **LAQueryLogs** テーブル クエリは、実行されたクエリの数を示しています。ここでは、**200 OK** の HTTP 応答以外の何らかのデータが受信されました。 たとえば、この数値には、実行に失敗したクエリが含まれます。

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>CPU 集約型のクエリのユーザーの表示

次の **LAQueryLogs** テーブル クエリでは、使用されている CPU とクエリ時間の長さに基づいて、最も多くの CPU 集約型のクエリを実行したユーザーが一覧表示されます。

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>過去 1 週間に最も多くのクエリを実行したユーザーの表示

次の **LAQueryLogs** テーブル クエリでは、過去 1 週間に最も多くのクエリを実行したユーザーが一覧表示されます。

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-microsoft-sentinel-activities"></a>Microsoft Sentinel アクティビティのアラートの構成

Microsoft Sentinel 監査リソースを使って、プロアクティブ アラートを作成できます。

たとえば、Microsoft Sentinel ワークスペースに機密テーブルがある場合、次のクエリを使って、テーブルに対してクエリが実行されるたびに通知を受け取ることができます。

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```

## <a name="monitor-microsoft-sentinel-with-workbooks-rules-and-playbooks"></a>ブック、ルール、およびプレイブックを使って Microsoft Sentinel を監視する

Microsoft Sentinel の独自の機能を使って、Microsoft Sentinel 内で発生するイベントとアクションを監視します。

- **ブックでの監視**。 次のブックは、ワークスペースのアクティビティを監視するように作成されました。

  - **ワークスペースの監査**。 環境内のどのユーザーがアクションを実行しているか、どのような操作が実行されたかなどについての情報が含まれます。
  - **分析の効率**。 どの分析ルールが使用されているか、最も多く対象となった MITRE 戦術、ルールから生成されたインシデントについての分析情報が得られます。
  - **セキュリティ操作の効率性**。 SOC チームのパフォーマンス、開いているインシデント、終了したインシデントなどのメトリックを表示します。 このブックを使用して、チームのパフォーマンスを表示し、必要な注意が不足している可能性のある領域を強調することができます。
  - **データ収集の正常性を監視**。 停滞または停止している取り込みがないかどうかを監視するのに役立ちます。

  詳細については、「[一般的に使われる Microsoft Sentinel ブック](top-workbooks.md)」を参照してください。

- **インジェストの遅延を監視**。  インジェストの遅延の懸念がある場合、遅延を表す[分析ルールの変数を設定します](ingestion-delay.md)。

  たとえば、次の分析ルールを使用すると、結果に重複が含まれないようにし、ルールの実行時にログが失われないようにすることができます。

  ```kusto
  let ingestion_delay= 2min;let rule_look_back = 5min;CommonSecurityLog| where TimeGenerated >= ago(ingestion_delay + rule_look_back)| where ingestion_time() > (rule_look_back)
  - Calculating ingestion delay
    CommonSecurityLog| extend delay = ingestion_time() - TimeGenerated| summarize percentiles(delay,95,99) by DeviceVendor, DeviceProduct
  ```

  詳細については、「[自動化ルールで Microsoft Sentinel でのインシデント処理を自動化する](automate-incident-handling-with-automation-rules.md)」を参照してください。

- [Connector Health Push Notification Solution](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Send-ConnectorHealthStatus) プレイブックを使用して **データ コネクタの正常性を監視** し、停滞または停止しているインジェストがないかどうかを監視し、コネクタがデータの収集を停止したとき、またはマシンがレポートを停止したときに通知を送信します。

## <a name="next-steps"></a>次のステップ

Microsoft Sentinel で **ワークスペース監査** ブックを使って、SOC 環境のアクティビティを監査します。

詳細については、「[データの視覚化と監視](monitor-your-data.md)」を参照してください。

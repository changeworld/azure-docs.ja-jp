---
title: ログ - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) のデータベース ログにアクセスする方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/13/2021
ms.openlocfilehash: 72996a44892d17a44ea58405e92bb9ac6cc52c68
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546439"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のログ

PostgreSQL データベース サーバーのログは、Hyperscale (Citus) サーバー グループの各ノードに使用できます。 ストレージ サーバーまたは分析サービスにログを送信できます。 ログは、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復に使用できます。

## <a name="capturing-logs"></a>ログのキャプチャ

Hyperscale (Citus) コーディネーターまたはワーカー ノードの PostgreSQL ログにアクセスするには、PostgreSQLLogs 診断設定を有効にする必要があります。 Azure portal で **[診断設定]** を開き、 **[+ 診断設定の追加]** を選択します。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="[診断設定の追加] ボタン":::

新しい診断設定の名前を選択し、 **[PostgreSQLLogs]** ボックスをオンにし、 **[Send to Log Analytics workspace]\(Log Analytics ワークスペースに送信する\)** ボックスをオンにします。  次に、 **[保存]** を選択します。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL のログを選択する":::

## <a name="viewing-logs"></a>ログの表示

ログの表示とフィルター処理には、Kusto クエリを使用します。 Azure portal で Hyperscale (Citus) サーバー グループの **[ログ]** を開きます。 クエリの選択ダイアログが表示されたら、閉じます。

:::image type="content" source="media/howto-hyperscale-logging/logs-dialog.png" alt-text="ダイアログ ボックスが開いた状態の [ログ] ページ":::

すると、クエリを入力する入力ボックスが表示されます。

:::image type="content" source="media/howto-hyperscale-logging/logs-query.png" alt-text="ログのクエリを実行する入力ボックス":::

次のクエリを入力し、 **[実行]** ボタンを選択します。

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
```

上のクエリを実行すると、すべてのノードからのログ メッセージと、その重大度とタイムスタンプが一覧表示されます。 `where` 句を追加して、結果をフィルター処理できます。 たとえば、コーディネーター ノードからのエラーのみを表示するには、次のようにエラー レベルとサーバー名をフィルター処理します。

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
| where LogicalServerName_s == 'example-server-group-c'
| where errorLevel_s == 'ERROR'
```

上の例のサーバー名は、実際のサーバーの名前に置き換えてください。 コーディネーター ノードの名前には `-c` というサフィックスが付き、ワーカー ノードには `-w0`、`-w1` などのサフィックスが付きます。

Azure ログは、さまざまな方法でフィルター処理できます。 メッセージが正規表現と一致する過去 1 日以内のログを検索する方法を次に示します。

```kusto
AzureDiagnostics
| where TimeGenerated > ago(24h)
| order by TimeGenerated desc
| where Message matches regex ".*error.*"
```

## <a name="next-steps"></a>次のステップ

- [Log Analytics クエリの使用方法](../azure-monitor/logs/log-analytics-tutorial.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md) について

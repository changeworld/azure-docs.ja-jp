---
title: ログの構成とアクセス - フレキシブル サーバー - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL のデータベース ログにアクセスする方法 - フレキシブル サーバー
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0b7b09696cbbe12a57d066e452b4c8ea7a7b8f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90931190"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL のログの構成とアクセス - フレキシブル サーバー

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

PostgreSQL のログは、フレキシブル サーバーのすべてのノードで使用できます。 ストレージ サーバーまたは分析サービスにログを送信できます。 ログは、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復に使用できます。

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

お使いの Postgres サーバーの診断設定を有効にするには、Azure portal、CLI、REST API、PowerShell を使用します。 選択するログ カテゴリは **PostgreSQLLogs** です。

Azure portal を使用してリソース ログを有効にするには

1. ポータルで、Postgres サーバーのナビゲーション メニューの *[診断設定]* に移動します。
   
2. *[診断設定の追加]* を選択します。
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="[診断設定の追加] ボタン":::

3. この設定に名前を付けます。 

4. 任意の優先エンドポイント (ストレージ アカウント、イベント ハブ、ログ分析) を選択します。 

5. ログの種類 **PostgreSQLLogs** を選択します。
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="[診断設定の追加] ボタン":::

7. 設定を保存します。

PowerShell、CLI、または REST API を使用してリソース ログを有効にするには、[診断の設定](../../azure-monitor/platform/diagnostic-settings.md)に関する記事をご覧ください。

### <a name="access-resource-logs"></a>リソース ログへのアクセス

ログへのアクセス方法は、選択したエンドポイントによって異なります。 Azure Storage については、[ログ ストレージ アカウント](../../azure-monitor/platform/resource-logs-collect-storage.md)に関する記事を参照してください。 Event Hubs の場合は、[Azure ログのストリーミング](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)に関する記事を参照してください。

Azure Monitor ログの場合は、選択したワークスペースにログが送信されます。 Postgres ログでは **AzureDiagnostics** コレクション モードが使用されるため、AzureDiagnostics テーブルからクエリを実行できます。 表内のフィールドについては、以下で説明します。 クエリとアラートの詳細については、[Azure Monitor のログ クエリ](../../azure-monitor/log-query/log-query-overview.md)の概要に関する記事を参照してください。

次に、作業を開始するために試すことのできるクエリを示します。 クエリに基づいてアラートを構成できます。

過去 1 日の特定のサーバーに関するすべての Postgres ログを検索する

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

localhost 以外のすべての接続の試行を検索する

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

上記のクエリを使うと、このワークスペースにログインしている任意の Postgres サーバーについて過去 6 時間の結果が表示されます。

## <a name="next-steps"></a>次のステップ

- [Log Analytics クエリの使用方法](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) について

---
title: 監査ログ - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブル サーバーでの pgAudit 監査ログの概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d4659e44475c09a1a42c06041e3f180357af9ee2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128556035"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでの監査ログ

Azure Database for PostgreSQL - フレキシブル サーバーでのデータベース アクティビティの監査ログは、PostgreSQL の監査拡張機能 [pgAudit](https://www.pgaudit.org/) を使用して入手できます。 pgAudit は、セッションまたはオブジェクトの詳細な監査ログを提供します。

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

コンピューティングやストレージのスケーリングなどの操作に Azure リソースレベルのログが必要な場合は、[Azure アクティビティ ログ](../../azure-monitor/essentials/platform-logs-overview.md)に関する記事を参照してください。

## <a name="usage-considerations"></a>使用に関する考慮事項
既定では、pgAudit ログ ステートメントは、Postgres の標準ログ記録機能を使用して、通常のログ ステートメントと共に出力されます。 Azure Database for PostgreSQL - フレキシブル サーバーでは、Azure Monitor ログ ストアに送信されるすべてのログを、Log Analytics で後で分析できるように構成することもできます。 Azure Monitor のリソース ログを有効にすると、選択した内容に応じて、ログが (JSON 形式で) Azure Storage、Event Hubs、または Azure Monitor ログに自動的に送信されます。

Azure Storage、Event Hubs、または Azure Monitor ログへのログ記録を設定する方法については、[サーバー ログに関する記事](concepts-logging.md)のリソース ログのセクションを参照してください。

## <a name="installing-pgaudit"></a>pgAudit のインストール

pgAudit をインストールするには、それをサーバーの共有プリロード ライブラリに含める必要があります。 Postgres の `shared_preload_libraries` パラメーターへの変更を有効にするには、サーバーの再起動が必要です。 パラメーターを変更するには、[Azure portal](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md)、または [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate) を使用できます。

[Azure portal](https://portal.azure.com) を使用して以下を実行します。

   1. お使いの Azure Database for PostgreSQL - フレキシブル サーバーを選択します。
   2. サイドバーから、 **[サーバー パラメーター]** を選択します。
   3. `shared_preload_libraries` パラメーターを検索します。
   4. **[pgaudit]** を選択します。
     :::image type="content" source="./media/concepts-audit/shared-preload-libraries.png" alt-text="Azure Database for PostgreSQL のスクリーンショット - pgaudit の shared_preload_libraries を有効にします":::
   5. psql で次のクエリを実行することで、**pgaudit** が shared_preload_libraries に読み込まれていることを確認できます。
        ```SQL
      show shared_preload_libraries;
      ```
      shared_preload_libraries を返す **pgaudit** がクエリに表示されるはずです

   6. クライアント (psql など) を使用してサーバーに接続し、pgAudit 拡張機能を有効にします。
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> エラーが表示される場合は、`shared_preload_libraries` を保存した後にサーバーを再起動したことを確認してください。

## <a name="pgaudit-settings"></a>pgAudit の設定

pgAudit では、セッションまたはオブジェクトの監査ログを構成できます。 [セッション監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)では、実行されたステートメントの詳細なログが出力されます。 [オブジェクト監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)は、特定の関係だけを対象にした監査です。 設定するログ記録の種類を 1 つにするか両方にするか選択できます。 

> [!NOTE]
> pgAudit 設定はグローバルに指定され、データベース レベルまたはロール レベルでは指定できません。

[pgAudit を有効](#installing-pgaudit)にした後は、ログ記録を開始するようそのパラメーターを構成できます。 pgAudit は下の手順で構成できます。 [Azure portal](https://portal.azure.com) を使用して以下を実行します。

   1. Azure Database for PostgreSQL サーバーを選択します。
   2. サイドバーから、 **[サーバー パラメーター]** を選択します。
   3. `pg_audit` パラメーターを探します。
   4. 編集する適切な設定パラメーターを選択してください。 たとえば、ログ記録を開始するには、`pgaudit.log` を `WRITE` に設定します。:::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="Azure Database for PostgreSQL のスクリーンショット - pgaudit でログ記録を構成します":::
   5. **[保存]** ボタンをクリックして変更を保存します


[pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)には、各パラメーターの定義が記載されています。 まずパラメーターをテストし、期待どおりに動作することを確認します。

> [!NOTE]
> `pgaudit.log_client` を ON に設定すると、ログはファイルに書き込まれるのではなく、クライアント プロセス (psql など) にリダイレクトされます。 通常、この設定は無効のままにしておく必要があります。 <br> <br>
> `pgaudit.log_level` が有効になるのは、`pgaudit.log_client` がオンになっている場合のみです。

> [!NOTE]
> pgAudit のドキュメントで説明されているように、Azure Database for PostgreSQL - フレキシブル サーバーでは、`-` (マイナス) 記号のショートカットを使用して、`pgaudit.log` を設定することはできません。 必要なステートメント クラス (READ、WRITE など) はすべて、個別に指定する必要があります。

## <a name="audit-log-format"></a>監査ログの形式
各監査エントリは、ログ行の先頭付近の `AUDIT:` によって示されます。 エントリの残りの部分の形式については、[pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)で詳しく説明されています。

## <a name="getting-started"></a>作業の開始
すぐに始めるには、`pgaudit.log` を `WRITE` に設定し、サーバー ログを開いて出力を確認します。 

## <a name="viewing-audit-logs"></a>監査ログの表示
ログへのアクセス方法は、選択したエンドポイントによって異なります。 Azure Storage については、[ログ ストレージ アカウント](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関する記事を参照してください。 Event Hubs の場合は、[Azure ログのストリーミング](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関する記事を参照してください。

Azure Monitor ログの場合は、選択したワークスペースにログが送信されます。 Postgres ログでは **AzureDiagnostics** コレクション モードが使用されるため、AzureDiagnostics テーブルからクエリを実行できます。 表内のフィールドについては、以下で説明します。 クエリとアラートの詳細については、[Azure Monitor のログ クエリ](../../azure-monitor/logs/log-query-overview.md)の概要に関する記事を参照してください。

このクエリを使用して作業を開始できます。 クエリに基づいてアラートを構成できます。

過去 1 日の特定のサーバーに関するすべての pgAudit エントリを Postgres ログで検索する
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL - フレキシブル サーバーでのログについて確認する](concepts-logging.md)
- [Azure Database for PostgreSQL - フレキシブル サーバーでログ記録を設定し、ログにアクセスする方法を確認する](howto-configure-and-access-logs.md)
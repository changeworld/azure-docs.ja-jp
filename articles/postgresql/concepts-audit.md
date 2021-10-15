---
title: Azure Database for PostgreSQL - Single Server の監査ログ
description: Azure Database for PostgreSQL - Single Server での pgAudit 監査ログの概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 8a60c1db1ca3b3037aded6ed7d7a88f237edfe4e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458702"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server の監査ログ

Azure Database for PostgreSQL - Single Server でのデータベース アクティビティの監査ログは、PostgreSQL の監査拡張機能 [pgAudit](https://www.pgaudit.org/) を介して利用できます。 pgAudit 拡張機能では、詳細なセッションとオブジェクトの監査ログが提供されます。

> [!NOTE]
> pgAudit 拡張機能は Azure Database for PostgreSQL ではプレビュー段階にあります。 これは、汎用サーバーとメモリ最適化サーバー上でのみ有効にすることができます。

コンピューティングやストレージのスケーリングなどの操作に Azure リソースレベルのログが必要な場合は、「[Azure プラットフォーム ログの概要](../azure-monitor/essentials/platform-logs-overview.md)」を参照してください。

## <a name="usage-considerations"></a>使用に関する考慮事項

既定では、pgAudit ログ ステートメントは、Postgres の標準ログ記録機能を使用して、通常のログ ステートメントと共に出力されます。 Azure Database for PostgreSQL では、これらの .log ファイルを Azure portal または Azure CLI を介してダウンロードできます。 ファイルのコレクションの最大ストレージは 1 GB です。 各ファイルは最大 7 日間使用できます。 既定値は 3 日です。 このサービスは、短期的なストレージ オプションです。

または、Azure Monitor ログ ストアに送信されるすべてのログを、Log Analytics で後で分析できるように構成することもできます。 Monitor のリソース ログを有効にすると、選択した内容に応じて、ログが JSON 形式で Azure Storage、Azure Event Hubs、または Azure Monitor のログに自動的に送信されます。

pgAudit を有効にすると、サーバー上に大量のログ記録が生成され、パフォーマンスとログ ストレージに影響を与えます。 長期的なストレージ オプションおよび分析やアラートの機能を提供する、Monitor ログを使用することをお勧めします。 以下のように、追加のログ記録によるパフォーマンスへの影響を軽減するために、標準のログ記録を無効にします。

   1. パラメーター `logging_collector` を **オフ** に設定します。
   1. この変更を適用するには、サーバーを再起動します。

Storage、Event Hubs、または Monitor ログへのログ記録を設定する方法については、「[Azure Database for PostgreSQL - Single Server のログ](concepts-server-logs.md)」のリソース ログのセクションを参照してください。

## <a name="install-pgaudit"></a>pgAudit をインストールする

pgAudit をインストールするには、それをサーバーの共有プリロード ライブラリに含める必要があります。 Postgres の `shared_preload_libraries` パラメーターへの変更を有効にするには、サーバーの再起動が必要です。 パラメーターを変更するには、[ポータル](howto-configure-server-parameters-using-portal.md)、[CLI](howto-configure-server-parameters-using-cli.md)、または [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate) を使用できます。

以下のように[ポータル](https://portal.azure.com)を使用します。

   1. Azure Database for PostgreSQL サーバーを選択します。
   1. 左側の **[設定]** の下で **[サーバー パラメーター]** を選択します。
   1. **shared_preload_libraries** を探します。
   1. **[PGAUDIT]** を選択します。
   
      :::image type="content" source="./media/concepts-audit/share-preload-parameter.png" alt-text="Azure Database for PostgreSQL で PGAUDIT の shared_preload_libraries を有効にしていることを示すスクリーンショット。":::

   1. サーバーを再起動して変更を適用します。
   1. 次のクエリを実行して `pgaudit` が `shared_preload_libraries` にロードされていることを確認します。
   
        ```SQL
      show shared_preload_libraries;
      ```
      `shared_preload_libraries` を返す `pgaudit` がクエリに表示されるはずです。

   1. psql などのクライアントを使用してサーバーに接続し、pgAudit 拡張機能を有効にします。
   
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> エラーが表示される場合は、`shared_preload_libraries` を保存した後にサーバーを再起動したことを確認してください。

## <a name="pgaudit-settings"></a>pgAudit の設定

pgAudit を使用すると、セッションまたはオブジェクトの監査ログを構成できます。 [セッション監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)では、実行されたステートメントの詳細なログが出力されます。 [オブジェクト監査ログ](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)は、特定の関係だけを対象にした監査です。 設定するログ記録の種類を 1 つにするか両方にするか選択できます。

> [!NOTE]
> pgAudit 設定はグローバルに指定され、データベース レベルまたはロール レベルでは指定できません。

[pgAudit のインストール](#install-pgaudit)が完了したら、ログ記録を開始するようそのパラメーターを構成できます。

pgAudit を構成するには、[ポータル](https://portal.azure.com)で以下を行います。

   1. Azure Database for PostgreSQL サーバーを選択します。
   1. 左側の **[設定]** の下で **[サーバー パラメーター]** を選択します。
   1. **pg_audit** パラメーターを探します。
   1. 編集する適切な設定パラメーターを選択してください。 たとえば、ログ記録を開始するには、**pgaudit.log** を **WRITE** に設定します。
   
       :::image type="content" source="./media/concepts-audit/pgaudit-config.png" alt-text="pgAudit を使用した Azure Database for PostgreSQL の構成を示すスクリーンショット。":::
   1. **[保存]** を選択して変更を保存します。

[pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)には、各パラメーターの定義が記載されています。 まずパラメーターをテストし、期待どおりに動作することを確認します。 例:

- 設定 **pgaudit.log_client** を ON にすると、ログはファイルに書き込まれるのではなく、psql などのクライアント プロセスにリダイレクトされます。 一般に、この設定を無効のままにします。
- パラメーター **pgaudit.log_level** は、**pgaudit.log_client** がオンになっている場合にのみ、有効化されます。

> [!NOTE]
> Azure Database for PostgreSQL では、pgAudit のドキュメントで説明されているように、マイナス記号のショートカット (`-`) を使用して、**pgaudit.log** を設定することはできません。 必要なステートメント クラス (READ、WRITE など) はすべて、個別に指定する必要があります。

### <a name="audit-log-format"></a>監査ログの形式

各監査エントリは、ログ行の先頭付近の `AUDIT:` によって示されます。 エントリの残りの部分の形式については、[pgAudit のドキュメント](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)で詳しく説明されています。

実際の監査要件を満たすためにその他のフィールドが必要な場合は、Postgres パラメーター `log_line_prefix` を使用します。 文字列 `log_line_prefix` は、各 Postgres ログ行の先頭に出力されます。 たとえば、次の `log_line_prefix` 設定では、タイムスタンプ、ユーザー名、データベース名、およびプロセス ID が提供されます。

```
t=%m u=%u db=%d pid=[%p]:
```

`log_line_prefix` の詳細を確認するには、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)を参照してください。

### <a name="get-started"></a>はじめに

すぐに使用するには、**pgaudit.log** を **WRITE** に設定します。 次に、ログを開いて出力を確認します。

## <a name="view-audit-logs"></a>監査ログの表示

.log ファイルを使用している場合、監査ログは、PostgreSQL エラー ログと同じファイルに含まれます。 ログ ファイルは、[ポータル](howto-configure-server-logs-in-portal.md)または [CLI](howto-configure-server-logs-using-cli.md) からダウンロードできます。

Azure リソース ログを使用している場合、ログへのアクセス方法は、選択したエンドポイントによって異なります。 Storage については、[Azure リソース ログ](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関するページを参照してください。 Event Hubs についても、[Azure リソース ログ](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関するページを参照してください。

Monitor ログの場合は、選択したワークスペースにログが送信されます。 Postgres ログでは `AzureDiagnostics` コレクション モードが使用されるため、示されているように `AzureDiagnostics` テーブルからクエリを実行できます。 クエリとアラートの詳細については、「[Azure Monitor でのクエリのログ](../azure-monitor/logs/log-query-overview.md)」を参照してください。

このクエリを使用して作業を開始します。 クエリに基づいてアラートを構成できます。

次のように、過去 1 日の特定のサーバーに関するすべての Postgres ログを検索します。

```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>次のステップ

- [Azure Database for PostgreSQL でのログ記録について学習する](concepts-server-logs.md)。
- [Azure portal](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md)、または [REST API](/rest/api/postgresql/singleserver/configurations/createorupdate) を使用してパラメーターを設定する方法を学習する。

---
title: SQL 分析情報のトラブルシューティング (プレビュー)
description: Azure Monitor で SQL 分析情報をトラブルシューティングする方法について学習します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/03/2021
ms.openlocfilehash: bf3b03f7837be31cf025d9ec7ea06a86a5c62e18
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989358"
---
# <a name="troubleshoot-sql-insights-preview"></a>SQL 分析情報のトラブルシューティング (プレビュー)
SQL 分析情報内のデータ収集の問題をトラブルシューティングするには、 **[プロファイルの管理]** タブで監視マシンの状態を確認します。以下の状態があります。

- **Collecting (収集中)** 
- **Not collecting (収集中ではない)** 
- **Collecting with errors (収集中だが、エラーあり)** 
 
状態を選択すると、問題の解決に役立つ可能性があるログと詳細情報が表示されます。 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="監視マシンの状態を示すスクリーンショット。":::

## <a name="status-not-collecting"></a>状態: 収集中ではない 
過去 10 分間、SQL の *InsightsMetrics* にデータがない場合、監視マシンの状態は **[収集中ではない]** になります。 

> [!NOTE]
> [サポートされているバージョンの SQL](sql-insights-overview.md#supported-versions) からデータを収集しようとしていることを確認します。 たとえば、有効なプロファイルと接続文字列を使用してデータの収集を試みても、サポートされていないバージョンの Azure SQL Database からデータの収集を試みると、 **[収集中ではない]** 状態となります。

SQL insights では、次のクエリを使用してこの情報を取得します。

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

問題の根本原因を特定するのに役立つ Telegraf のログがあるかどうかを調べます。 ログ エントリがある場合は、 **[収集中ではない]** を選択し、ログと一般的な問題のトラブルシューティング情報を確認できます。 

ログ エントリがない場合は、2 つの仮想マシン拡張機能によってインストールされた以下のサービスについて、監視仮想マシンのログを調べます。

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - サービス: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - サービス: wli 
  - サービス: ms-telegraf 
  - サービス: td-agent-bit-wli 
  - インストールの失敗を調べる拡張機能のログ: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

### <a name="wli-service-logs"></a>wli サービス ログ 

サービス ログ: `/var/log/wli.log`

最近のログを表示する場合: `tail -n 100 -f /var/log/wli.log`
 
次のエラー ログが表示される場合は、mdsd サービスに問題があります。

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```

### <a name="telegraf-service-logs"></a>Telegraf サービス ログ 

サービス ログ: `/var/log/ms-telegraf/telegraf.log`

最近のログを表示する場合: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`

最近のエラーおよび警告のログを表示するには: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

telegraf が使用する構成は wli サービスによって生成され、次の場所に置かれます: `/etc/ms-telegraf/telegraf.d/wli`
 
正しくない構成が生成された場合は、ms-telegraf サービスが開始されないことがあります。 次のコマンドを使用して、ms-telegraf サービスが実行中かどうかを調べます: `service ms-telegraf status`

telegraf サービスからのエラー メッセージを表示するには、次のコマンドを使用してサービスを手動で実行します。 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd サービス ログ 

Azure Monitor エージェントの[前提条件](../agents/azure-monitor-agent-install.md#prerequisites)を確認してください。 


サービス ログ:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

最近のエラーを表示する場合: `tail -n 100 -f /var/log/mdsd.err`

サポートに連絡する必要がある場合は、次の情報を収集してください。 

- `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 内のログ 
- `/var/log/waagent.log` 内のログ 
- `/var/log/mdsd*` 内のログ
- `/etc/mdsd.d/` 内のファイル
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>監視仮想マシンの構成が無効です

**[収集中ではない]** 状態の原因の 1 つは、監視仮想マシンの構成が無効であることです。 最も単純な形式の構成は次のとおりです。

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

この構成では、監視仮想マシンのプロファイル構成で使用される置換トークンが指定されています。 これを使用すると、Azure Key Vault にあるシークレットを参照することもできるので、どの構成にもシークレットの値を保管する必要がありません (保管しないことを強くお勧めします)。

この構成では、`$telegrafPassword` 置換トークンがデータベース接続文字列に含まれます。 このトークンは SQL インサイトによって、Key Vault から取得した SQL 認証パスワードに置き換えられます。 Key Vault の URI は、`secrets` 下の `telegrafPassword` 構成セクションで指定します。

#### <a name="secrets"></a>シークレット
シークレットは、実行時に Azure のキー コンテナーから値が取得されるトークンです。 シークレットは、キー コンテナー URI とシークレット名を含む値のペアによって定義されます。 SQL インサイトでは、実行時にこの定義からシークレットの値を取得して、ダウンストリーム構成に使用することができます。

シークレットは、複数のキー コンテナーに保存されるシークレットを含め、必要な数だけ定義できます。

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

キー コンテナーにアクセスするためのアクセス許可は、監視仮想マシン上のマネージド ID に提供されます。 このマネージド ID には、監視プロファイル構成で参照されているすべての Key Vault シークレットに対する Get アクセス許可を付与する必要があります。 これは、Azure portal、PowerShell、Azure CLI、または Azure Resource Manager テンプレートから実行できます。

#### <a name="parameters"></a>パラメーター
パラメーターは、JSON テンプレートを介して、プロファイル構成内で参照できるトークンです。 パラメーターには、名前と値があります。 値は、オブジェクトや配列を含む任意の JSON 型にできます。 パラメーターはプロファイル構成内で、その名前によって、`.Parameters.<name>` という規則を使用して参照されます。

パラメーターでは、同じ規則を使用して、Key Vault 内のシークレットを参照できます。 たとえば `sqlAzureConnections` では、`$telegrafPassword` という規則を使用して、シークレット `telegrafPassword` を参照します。

実行時には、すべてのパラメーターとシークレットが解決され、プロファイル構成とマージされて、マシンで使用される実際の構成が作成されます。

> [!NOTE]
> パラメーター名 `sqlAzureConnections`、`sqlVmConnections`、`sqlManagedInstanceConnections` はすべて、このうちの一部に対して接続文字を指定しない場合でも構成に必要です。

## <a name="status-collecting-with-errors"></a>状態: 収集中だが、エラーあり
少なくとも 1 つの最近の *InsightsMetrics* ログがあっても、*Operation* テーブルにエラーもある場合、監視マシンの状態は **[収集中だが、エラーあり]** になります。

SQL Insights では、次のクエリを使用してこの情報を取得します。

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```kusto
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> `WorkloadDiagnosticLogs` にどのデータも表示されない場合は、監視プロファイルを更新する必要が生じることがあります。 Azure portal の SQL Insights 内から、 **[プロファイルの管理]**  >  **[プロファイルの編集]**  >  **[監視プロファイルの更新]** と選択します。

一般的なケースの場合、トラブルシューティングのヒントはログ ビューで提供されます。 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="トラブルシューティング ログ ビュー。":::

## <a name="known-issues"></a>既知の問題

SQL インサイトのプレビュー中、以下に示す既知の問題が発生する場合があります。

* **サーバーまたはデータベースへの接続中の "ログイン失敗" エラー**。 監視 VM 構成または Key Vault に保存した SQL 認証パスワードに特定の特殊文字が使用されていると、監視 VM が SQL サーバーまたはデータベースに接続できない場合があります。 該当する文字には、かっこ、角かっこ、中かっこ、ドル記号、スラッシュ、円記号、ドット (`[ { ( ) } ] $ \ / .`) などがあります。

## <a name="best-practices"></a>ベスト プラクティス

* **監視 VM から Key Vault にアクセスできることを確認します**。 Key Vault を使用して SQL 認証パスワードを保存する場合 (強く推奨)、監視 VM から Key Vault にアクセスできるよう、ネットワークとセキュリティの構成を確認する必要があります。 詳細については、「[ファイアウォールの内側にある Azure Key Vault にアクセスする](/key-vault/general/access-behind-firewall.md)」および「[Azure Key Vault のネットワーク設定を構成する](/key-vault/general/how-to-azure-key-vault-network-security.md)」を参照してください。 監視 VM から Key Vault にアクセスできることは、VM に接続した SSH セッションから次のコマンドを実行して確認できます。 アクセス トークンとシークレットを正常に取得できる必要があります。 `[YOUR-KEY-VAULT-URL]`、`[YOUR-KEY-VAULT-SECRET]`、`[YOUR-KEY-VAULT-ACCESS-TOKEN]` を実際の値に置き換えます。

  ```bash
  # Get an access token for accessing Key Vault secrets
  curl 'http://[YOUR-KEY-VAULT-URL]/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true
  
  # Get Key Vault secret
  curl 'https://[YOUR-KEY-VAULT-URL]/secrets/[YOUR-KEY-VAULT-SECRET]?api-version=2016-10-01' -H "Authorization: Bearer [YOUR-KEY-VAULT-ACCESS-TOKEN]"
  ```

* **監視 VM でソフトウェアを更新します**。 監視 VM で、オペレーティング システムと拡張機能を定期的に更新することを強くお勧めします。 拡張機能で自動アップグレードがサポートされている場合は、そのオプションを有効にしてください。

* **以前の構成を保存します**。 監視プロファイルまたは監視 VM 構成に変更を加える場合は、正常に機能している構成データのコピーを保存してから行うことをお勧めします。 Azure portal の [SQL インサイト] ページから **[プロファイルの管理]**  >  **[プロファイルの編集]** を選択し、 **[現在の監視プロファイル構成]** のテキストをファイルにコピーします。 同様に、監視 VM の **[プロファイルの管理]**  >  **[構成]** を選択し、 **[現在の監視構成]** のテキストをファイルにコピーします。 構成変更後にデータ収集エラーが発生した場合、テキスト差分ツールを使用して新しい構成を既知の正常な構成と比較し、収集に影響を及ぼした可能性がある変更を特定することができます。

## <a name="next-steps"></a>次のステップ

- [SQL insights の有効化](sql-insights-enable.md)に関するページで詳細情報を取得します。

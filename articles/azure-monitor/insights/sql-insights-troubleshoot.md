---
title: SQL insights のトラブルシューティング (プレビュー)
description: Azure Monitor での SQL insights のトラブルシューティング
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 4d4a801d0cf0a2355334272053ff86dd846b6bbf
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030306"
---
# <a name="troubleshooting-sql-insights-preview"></a>SQL insights のトラブルシューティング (プレビュー)
SQL insights でのデータ収集の問題をトラブルシューティングするには、 **[プロファイルの管理]** タブで監視マシンの状態を確認します。ここには、以下の状態のいずれかが表示されています。

- Collecting (収集中) 
- Not collecting (収集中ではない) 
- Collecting with errors (収集中だが、エラーあり) 
 
**[状態]** をクリックすると、問題解決に役立つログと追加の詳細が表示されます。 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="監視マシンの状態":::

## <a name="not-collecting-state"></a>Not collecting (収集中ではない) 状態 
過去10分間、SQL の *InsightsMetrics* にデータがない場合、監視マシンの状態は *[Not collecting]\(収集中ではない\)* になります。 

SQL insights では、次のクエリを使用してこの情報を取得します。

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

問題の根本原因を特定するのに役立つ Telegraf のログがあるかどうかを確認します。 ログ エントリがある場合は、 *[Not collecting]\(収集中ではない\)* をクリックし、ログと一般的な問題のトラブルシューティング情報を確認できます。 


ログがない場合は、監視仮想マシンのログで、2 つの仮想マシン拡張機能によってインストールされた次のサービスを確認する必要があります。

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - サービス: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - サービス: wli 
  - サービス: ms-telegraf 
  - サービス: td-agent-bit-wli 
  - インストールの失敗を確認するための拡張機能のログ: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>wli サービス ログ 

サービス ログ: `/var/log/wli.log`

最近のログを表示する場合: `tail -n 100 -f /var/log/wli.log`
 

次のエラー ログが表示される場合は、**mdsd** サービスに問題があることを示しています。

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Telegraf サービス ログ 

サービス ログ: `/var/log/ms-telegraf/telegraf.log`

最近のログを表示する場合: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`。最近のエラーと警告のログを表示する場合: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Telegraf によって使用される構成は、wli サービスによって生成され、次の場所に配置されます: `/etc/ms-telegraf/telegraf.d/wli`
 
無効な構成が生成された場合は、ms-telegraf サービスが開始されない可能性があります。次のコマンドを使用して、ms-telegraf サービスか実行されているかどうかを確認します: `service ms-telegraf status`

telegraf サービスからのエラー メッセージを表示するには、次のコマンドを使用して手動で実行してください: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd サービス ログ 

Azure Monitor エージェントの[現在の制限](../agents/azure-monitor-agent-overview.md#current-limitations)を確認します。 


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

*Not Collecting (収集中ではない)* 状態の原因の 1 つは、監視仮想マシンの構成が無効であることです。  既定の構成を次に示します。

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

この構成では、監視仮想マシンのプロファイル構成で使用される置換トークンが指定されています。 これを使用すると、Azure Key Vault からシークレットを参照することもできるので、どの構成にもシークレットの値を保持しないようにすることを強くお勧めします。

#### <a name="secrets"></a>シークレット
シークレットは、実行時に Azure Key Vault から値が取得されるトークンです。 シークレットは、Key Vault 参照とシークレット名のペアによって定義されます。 これにより、Azure Monitor ではシークレットの動的な値を取得し、それをダウンストリーム構成の参照として使用できます。

構成には、別々の Key Vault に格納されているシークレットを含め、必要に応じていくつでもシークレットを定義できます。

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

Key Vault にアクセスするためのアクセス許可は、監視仮想マシンのマネージド サービス ID に提供されます。 Azure Monitor では、少なくとも仮想マシンへのアクセス許可を取得するためのシークレットが Key Vault で提供されることを想定しています。 これは、Azure portal、PowerShell、CLI、または Resource Manager テンプレートから有効にできます。

#### <a name="parameters"></a>パラメーター
パラメーターは、JSON テンプレートを使用してプロファイル構成で参照できるトークンです。 パラメーターには、名前と値があります。 値は、オブジェクトや配列を含む任意の JSON 型にすることができます。 パラメーターは、`.Parameters.<name>` 規則で名前を使用して、プロファイル構成で参照されます。

パラメーターでは、同じ規則を使用して Key Vault 内のシークレットを参照できます。 たとえば、`sqlAzureConnections` では、`$telegrafPassword` 規則を使用してシークレット `telegrafPassword` を参照します。

実行時には、すべてのパラメーターとシークレットが解決され、プロファイル構成とマージされて、マシンで使用される実際の構成が作成されます。

> [!NOTE]
> パラメーター名 `sqlAzureConnections`、`sqlVmConnections`、および `sqlManagedInstanceConnections` は、このうちの一部に対して指定する接続文字がない場合でも、すべて構成に必要です。


## <a name="collecting-with-errors-state"></a>Collecting with errors (収集中だが、エラーあり) 状態
少なくとも 1 つの *InsightsMetrics* ログがあっても、*操作* テーブルにエラーもある場合、監視マシンの状態は *[Collecting with errors]\(収集中だが、エラーあり\)* になります。

SQL insights では、次のクエリを使用してこの情報を取得します。

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> 'WorkloadDiagnosticLogs' データ型にデータが表示されない場合、このデータを格納するために監視プロファイルの更新が必要になる場合があります。  SQL Insights UX 内から、[プロファイルの管理]、[プロファイルの編集]、[監視プロファイルの更新]の順に選択します。


一般的なケースの場合、トラブルシューティングの知識はログ ビューで提供されます。 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="トラブルシューティング ログ ビュー。":::



## <a name="next-steps"></a>次のステップ

- [SQL insights の有効化](sql-insights-enable.md)に関するページで詳細情報を取得します。

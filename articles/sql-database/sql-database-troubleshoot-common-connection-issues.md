<properties
	pageTitle="Azure SQL Database との接続に関する一般的な問題のトラブルシューティング"
	description="Azure SQL Database の一般的な接続エラーを特定して解決する手順。"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="daleche"/>

# Azure SQL Database との接続に関する一般的な問題のトラブルシューティング
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Azure SQL Database との接続の問題は、大きく次のカテゴリに分類されます。

- 一時的なエラー (短期間または断続的)
- 永続的または一時的でないエラー (定期的に発生するエラー)

アプリケーションで一時的なエラーが発生する場合は、トラブルシューティングや、エラーの頻度を減らす方法について、次のヒントのトピックを確認してください。

- [Troubleshooting Database &lt;x&gt; on Server &lt;y&gt; is unavailable (Error: 40613) (サーバー &lt;y&gt; 上のデータベース &lt;x&gt; が使用できない問題のトラブルシューティング (エラー: 40613))](sql-database-troubleshoot-connection.md)
- [SQL Database の SQL 接続エラーと一時エラーのトラブルシューティング、診断、防止](sql-database-connectivity-issues.md)

アプリケーションが SQL Azure Database の接続に引き続き失敗する場合は、一般的に、次のいずれかの問題が考えられます。

- ファイアウォールの構成: Azure SQL データベースまたはクライアント側のファイアウォールにより、Azure SQL Database への接続がブロックされています。
- クライアント側のネットワークの再構成: 新しい IP アドレス、プロキシ サーバーなど。
- ユーザー エラー: 接続パラメーター (接続文字列のサーバー名など) の入力間違いなど。

## 永続的な接続の問題を解決する手順
1.	クライアントの IP アドレスを許可するには、[ファイアウォール規則](sql-database-configure-firewall-settings.md)を設定します。
2.	クライアントとインターネットの間のすべてのファイアウォールで、送信接続用のポート 1433 が開いていることを確認します。その他のポインターについては、「[SQL Server のアクセスを許可するための Windows ファイアウォールの構成)](https://msdn.microsoft.com/library/cc646023.aspx)」を参照してください。
3.	接続文字列およびその他の接続設定を確認します。[接続の問題のトピック](sql-database-connectivity-issues.md#connections-to-azure-sql-database)で、接続文字列のセクションを確認します。
4.	ダッシュ ボードでサービスの正常性を確認します。リージョンの障害があると思われる場合は、新しいリージョンへの回復手順を[障害からの回復](sql-database-disaster-recovery.md)のトピックで参照してください。
5.	接続の問題が解消しない場合は、[Azure サポート](https://azure.microsoft.com/support/options) サイトの **[サポートの要求]** を選択して、Azure サポート要求を提出してください。

<!---HONumber=AcomDC_0330_2016-->
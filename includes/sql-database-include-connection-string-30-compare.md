
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### 接続文字列の比較
次の表では、C# プログラムでオンプレミスの SQL Server に接続するときに必要な接続文字列と、クラウドの Azure SQL Database に接続するときに必要な接続文字列を比較しています。相違点を太字で示しています。

| Azure SQL Database<br/>の接続文字列 | Microsoft SQL Server<br/>の接続文字列 |
|:--- |:--- |
| Server=**tcp:**{サーバー名}**.database.windows.net,1433**;<br/>User ID={ログイン名}**@{サーバー名}**;<br/>Password={パスワード};<br/>**Database={データベース名};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={サーバー名};<br/>User ID={ログイン名};<br/>Password={パスワード}; |

**Database=** は、SQL Server の場合は省略可能ですが、SQL Database では必須です。

すべてのパラメーターの詳細については、[.NET ADO SqlConnectionStringBuilder のプロパティに関するページ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx)をご覧ください。

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->
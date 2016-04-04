<properties
   pageTitle="SQL Data Warehouse でデータベースを障害から復旧する | Microsoft Azure"
   description="SQL Data Warehouse でデータベースを障害から復旧するための手順です。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/09/2016"
   ms.author="sahajs;barbkess"/>

# SQL Data Warehouse でのデータベースの障害からの復旧

geo 復元機能を利用すると、geo 冗長バックアップからデータベースを復元して新しいデータベースを作成できます。データベースは任意の Azure リージョンの任意のサーバーに作成できます。ソースとして geo 冗長バックアップが使用されるため、障害によってデータベースにアクセスできない場合でも、それを使用してデータベースを復旧できます。geo 復元は、障害からの復旧以外に、データベースを別のサーバーやリージョンに移行またはコピーするなど、他のシナリオにも使用できます。


## 復旧を開始するタイミング
復旧操作を行う場合、復旧時に SQL 接続文字列を変更する必要があり、結果としてデータが完全に失われる可能性があります。そのため、障害がアプリケーションの RTO よりも長くかかる可能性が高い場合にのみ行ってください。次のデータ ポイントを使用して、復旧が保証されていることを確認してください。

- データベースへの永続的な接続エラー。
- Azure ポータルは、幅広い影響のあるリージョンでのインシデントに関するアラートを示します。


## geo リストアを使用した復旧
データベースを復旧すると、最新の geo 冗長バックアップから新しいデータベースが作成されます。復旧先サーバーに新しいデータベース用の十分な DTU 容量があることが重要です。このクォータの増加を要求する場合は、[サポートに連絡][]してください。


### Azure ポータル
1. [Azure ポータル][]にログインします。
2. 画面の左側にある、**[新規]** を選択し、次に **[データ + ストレージ]** を選択し、次に **[SQL Data Warehouse]** を選択します。
3. ソースとして **[バックアップ]** を選択し、復旧する geo 冗長バックアップを選択します。
4. データベースのプロパティの残りの部分を指定して **[作成]** をクリックします。
5. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。


### PowerShell
Azure PowerShell を使用して、プログラムでデータベースの復旧を実行します。Azure PowerShell モジュールをダウンロードするには、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行します。Get-Module -ListAvailable -Name Azure を実行することで、バージョンを確認できます。この記事は、Microsoft Azure PowerShell バージョン 1.0.4 に基づいています。

データベースを復旧するには、[Start-AzureSqlDatabaseRecovery][] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. 復旧するデータベースを取得します。
5. データベースの復旧要求を作成します。
6. 復旧の進行状況を監視します。

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$Database = Get-AzureRmSqlRecoverableDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Recover database
$RecoveryRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourSourceServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -TargetServerName "<YourTargetServerName>"

# Monitor progress of recovery operation
Get-AzureSqlDatabaseOperation -ServerName "<YourTargetServerName>" –OperationGuid $RecoveryRequest.RequestID

```

サーバーが foo.database.windows.net の場合は、上の PowerShell コマンドレットで -ServerName として "foo" を使用してください。

### REST API
REST を使用して、プログラムでデータベースの復旧を実行します。

1. [List Recoverable Databases][] 操作で回復可能なデータベースの一覧を取得します。
2. [Get Recoverable Database][] 操作で回復するデータベースを取得します。
3. [Create Database Recovery Request][] 操作を使用して回復要求を作成します。
4. [Database Operation Status][] 操作で回復の状態を追跡します。



## 復旧後のデータベースの構成
復旧後のデータベースをすぐ運用できるようにするためのチェックリストです。

1. **接続文字列を更新する**: クライアント ツールの接続文字列が新しく復旧したデータベースを指していることを確認します。
2. **ファイアウォール ルールを変更する**: 対象サーバーのファイアウォール ルールを確認し、クライアント コンピューターまたは Azure からサーバーと新しく復旧されたデータベースへの接続が有効になっていることを確認します。
3. **サーバー ログインとデータベース ユーザーを確認する**: アプリケーションで使用するすべてのログインが、復旧されたデータベースをホストしているサーバー上に存在することを確認します。不足しているログインを再作成し、復旧されたデータベースに対する適切なアクセス許可を与えます。 
4. **監査を有効にする**: データベースにアクセスするために監査が必要な場合は、データベースの復旧後に監査を有効にする必要があります。

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。


## 次のステップ
Azure SQL Database のその他のエディションのビジネス継続性については、[Azure SQL Database のビジネス継続性の概要][]を参照してください。


<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Start-AzureSqlDatabaseRecovery]: https://msdn.microsoft.com/library/azure/dn720224.aspx
[List Recoverable Databases]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[Get Recoverable Database]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[Create Database Recovery Request]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[Database Operation Status]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[Azure ポータル]: https://portal.azure.com/
[サポートに連絡]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0316_2016-->
<properties
   pageTitle="Azure SQL Data Warehouse でのデータベースの復元 (REST API) | Microsoft Azure"
   description="Azure SQL Data Warehouse でライブ データベース、削除済みデータベース、またはアクセスできないデータベースを復元するための REST API のタスク。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Azure SQL Data Warehouse でのデータベースの復元 (REST API)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-restore-database-overview.md)
- [ポータル](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST ()](sql-data-warehouse-manage-restore-database-rest-api.md)

Azure SQL Data Warehouse でライブ データベース、削除済みデータベース、またはアクセスできないデータベースを復元するための REST API のタスク。

このトピックのタスク:

- ライブ データベースの復元
- 削除されたデータベースの復元
- アクセスできないデータベースを地理的に異なる Azure リージョンから復元

## 開始する前に

**SQL Database の DTU 容量を確認する** SQL Data Warehouse では、論理 SQL サーバーの新しいデータベースに復元されるため、復元先 SQL サーバーに、新しいデータベース用の DTU 容量が十分にあることを確認することが重要です。詳細については、[DTU クォータを表示および増量する方法][]に関するブログ投稿記事をご覧ください。

## ライブ データベースの復元

データベースを復元するには:

1. 「データベース復元ポイントの取得」操作を使用して、データベースの復元ポイントの一覧を取得します。
2. 「[データベースの復元要求の作成][]」の操作を使用して、復元を開始します。
3. 「[データベース操作の状態][]」の操作を使用して、復元の状態を追跡します。

>[AZURE.NOTE] 復元が完了したら、[復旧されたデータベースの最終処理][]に関するガイドに従って、復旧されたデータベースを構成できます。

## 削除されたデータベースの復元

削除されたデータベースを復元するには

1.	「[削除された復元可能なデータベースの一覧表示][]」の操作を使用して、復元可能なすべての削除済みデータベースのリストを表示します。
2.	「[削除された復元可能なデータベースの取得][]」の操作を使用して、復元する削除済みデータベースの詳細を取得します。
3.	「[データベースの復元要求の作成][]」の操作を使用して、復元を開始します。
4.	「[データベース操作の状態][]」の操作を使用して、復元の状態を追跡します。

>[AZURE.NOTE] 復元が完了したら、[復旧されたデータベースの最終処理][]に関するガイドに従って、復旧されたデータベースを構成できます。

## Azure 地理的リージョンからの復元

geo リストアを実行するには:

1. [List Recoverable Databases][] 操作で回復可能なデータベースの一覧を取得します。
2. [Get Recoverable Database][] 操作で回復するデータベースを取得します。
3. [Create Database Recovery Request][] 操作で回復要求を作成します。
4. [Database Operation Status][] 操作で回復の状態を追跡します。

### geo リストアの実行後にデータベースを構成する
復旧後のデータベースをすぐ運用できるようにするためのチェックリストです。

1. **接続文字列を更新する**: クライアント ツールの接続文字列が新しく復旧したデータベースを指していることを確認します。
2. **ファイアウォール規則を変更する**: ターゲット サーバーのファイアウォール規則を確認し、クライアント コンピューターまたは Azure からサーバーおよび新しく復旧されたデータベースへの接続が有効になっていることを確認します。
3. **サーバー ログインとデータベース ユーザーを確認する**: アプリケーションで使用するすべてのログインが、復旧されたデータベースをホストしているサーバー上に存在することを確認します。不足しているログインを再作成し、復旧されたデータベースに対する適切なアクセス許可を与えます。 
4. **監査を有効にする**: データベースにアクセスするために監査が必要な場合は、データベースの復旧後に監査を有効にする必要があります。

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。


## 次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: sql-database-business-continuity.md
[復旧されたデータベースの最終処理]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[データベースの復元要求の作成]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[データベース操作の状態]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[削除された復元可能なデータベースの取得]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[削除された復元可能なデータベースの一覧表示]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[DTU クォータを表示および増量する方法]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->
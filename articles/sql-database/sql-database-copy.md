---
title: Azure SQL Database のコピーの作成 | Microsoft Docs
description: Azure SQL Database のコピーの作成
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 06/16/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# <a name="copy-an-azure-sql-database"></a>Azure SQL Database のコピー
> [!div class="op_single_selector"]
> * [概要](sql-database-copy.md)
> * [Azure ポータル](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Azure [SQL Database 自動バックアップ](sql-database-automated-backups.md) を使用して、 SQL データベースのコピーを作成できます。 コピー操作では、トランザクション ログの末尾がコピーされて、自動バックアップの一部の完全、差分、およびトランザクション ログ バックアップにより、トランザクションが最後のトランザクション ログ バックアップの時点のコピー元データベースと同じデータベースがコピーされます。 

同じサーバーまたは別のサーバーのいずれかで、データベースのコピーを作成できます。 データベース コピーのサービス レベルとパフォーマンス レベル (価格レベル) はコピー元のデータベースと同じになります。 コピーの完了後、コピーは完全に機能する独立したデータベースになります。 ログイン、ユーザー、アクセス許可は非依存で管理できます。 

データベースを同じ論理サーバーにコピーすると、両方のデータベースで同じログインを利用できます。 データベースをコピーするために使用するセキュリティ プリンシパルが、新しいデータベースのデータベース所有者 (DBO) になります。 すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。 

別の論理サーバーにデータベースをコピーする場合は、新しいサーバーのセキュリティ プリンシパルが新しいデータベースのデータベース所有者になります。 包含ユーザーであるデータベース ユーザーは、コピー先のデータベースで使用できます。 ただし、新しいサーバーにデータベースをコピーする場合、ログインに基づくユーザーは通常は動作しません。これは、そのログインが新しいサーバーに存在しないためで、存在していたとしても、SID が一致しない場合があるためです。 新しいデータベースがコピー先サーバーでオンラインになった後、 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) ステートメントを使用して、ユーザーを新しいデータベースからコピー先サーバーのログインに再マップします。 孤立したユーザーを解決するには、「 [孤立ユーザーのトラブルシューティング](https://msdn.microsoft.com/library/ms175475.aspx)」をご覧ください。 

SQL Database をコピーするには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合、このページの上部の **無料評価版** をクリックしてからこの記事に戻り、最後まで完了してください。
* コピーする SQL データベース。 SQL Database がない場合は、「 [最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。

## <a name="next-steps"></a>次のステップ
* Azure ポータルを使用してデータベースをコピーするには、「 [Copy an Azure SQL database using the Azure Portal (Azure ポータルを使用した Azure SQL Database のコピー)](sql-database-copy-portal.md) 」をご覧ください。
* PowerShell を使用してデータベースをコピーするには、「 [PowerShell を使用した Azure SQL Database のコピー](sql-database-copy-powershell.md) 」をご覧ください。
* Transact-SQL を使用してデータベースをコピーするには、「 [T-SQL を使用した Azure SQL Database のコピー](sql-database-copy-transact-sql.md) 」をご覧ください。
* 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「 [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md) 」をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [ログインの管理](sql-database-manage-logins.md)
* [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)
* [データベースを BACPAC にエクスポートする](sql-database-export.md)
* [ビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!--HONumber=Oct16_HO2-->



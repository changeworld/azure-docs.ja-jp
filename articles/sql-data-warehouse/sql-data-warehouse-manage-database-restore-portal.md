<properties
   pageTitle="Azure SQL Data Warehouse でのデータベースの復元 (Azure ポータル) | Microsoft Azure"
   description="Azure SQL Data Warehouse でライブ データベース、削除済みデータベース、またはアクセスできないデータベースを復元するための Azure ポータルのタスク。"
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
   ms.date="06/02/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Azure SQL Data Warehouse でのデータベースのバックアップと復元 (Azure ポータル)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-overview-manage-database-restore.md)
- [ポータル](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST ()](sql-data-warehouse-manage-database-restore-rest-api.md)

Azure SQL Data Warehouse でデータベースを復元するための Azure ポータル タスク。

このトピックのタスク:

- ライブ データベースの復元
- 削除されたデータベースの復元

## 開始する前に

SQL Database の DTU 容量を確認するSQL Data Warehouse では、論理 SQL サーバーの新しいデータベースに復元されるため、復元先 SQL サーバーに、新しいデータベース用の DTU 容量が十分にあることを確認することが重要です。詳細については、[DTU クォータを表示および増量する方法][]に関するブログ投稿記事をご覧ください。


## ライブ データベースの復元

データベースを復元するには:

1. [Azure ポータル][]にログインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL データベース]** を選択します。
3. 目的のデータベースに移動し、それを選択します。
4. データベース ブレードの上部にある **[復元]** をクリックします。
5. 新しい**データベース名**を指定し、**[復元ポイント]** を選択して、**[作成]** をクリックします。
6. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。


## 削除されたデータベースの復元

削除されたデータベースを復元するには:

1. [Azure ポータル][]にログインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL Server]** を選択します。
3. 目的のサーバーに移動して選択します。
4. サーバーのブレードで下にスクロールして [操作] を表示し、**[削除済みデータベース]** タイルをクリックします。
5. 復元する削除済みデータベースを選択します。
5. 新しい**データベース名**を指定し、**[作成]** をクリックします。
6. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。


## 次のステップ
詳細については、[Azure SQL Database のビジネス継続性の概要][]および[管理の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: sql-database-business-continuity.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md
[管理の概要]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->

<!--Blog references-->
[DTU クォータを表示および増量する方法]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->
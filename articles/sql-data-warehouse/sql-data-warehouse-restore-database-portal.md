<properties
   pageTitle="Azure SQL Data Warehouse の復元 (ポータル) | Microsoft Azure"
   description="Azure SQL Data Warehouse を復元するための Azure ポータル タスク。"
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
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Azure SQL Data Warehouse の復元 (ポータル)

> [AZURE.SELECTOR]
- [概要][]
- [ポータル][]
- [PowerShell][]
- [REST ()][]

この記事では、Azure ポータルを使用して Azure SQL Data Warehouse を復元する方法について説明します。

## 開始する前に

**DTU 容量を確認します。** 各 SQL Data Warehouse は SQL サーバーの論理サーバーでホストされます。この論理サーバーには容量制限があり、DTU 単位で測定されます。SQL Data Warehouse を復元する前に、データベースをホストしている SQL サーバーの論理サーバーに、データベースを復元するために十分な DTU 容量があるか確認する必要があります。詳細については、[DTU クォータを表示および増量する方法][]に関するブログ投稿記事をご覧ください。


## アクティブまたは一時停止中のデータベースを復元する

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
4. サーバーのブレードで下へスクロールして [操作] を表示し、**[削除済みデータベース]** タイルをクリックします。
5. 復元する削除済みデータベースを選択します。
5. 新しい**データベース名**を指定し、**[作成]** をクリックします。
6. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。


## 次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: ./sql-database-business-continuity.md
[概要]: ./sql-data-warehouse-restore-database-overview.md
[ポータル]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST ()]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->

<!--Blog references-->
[DTU クォータを表示および増量する方法]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->
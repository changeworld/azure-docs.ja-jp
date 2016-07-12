<properties
   pageTitle="Azure SQL Data Warehouse の復元 (ポータル) | Microsoft Azure"
   description="Azure SQL Data Warehouse を復元するための Azure ポータル タスク。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess;nicw"/>

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

1. [Azure ポータル][]にログインします
2. 画面の左側にある **[参照]** をクリックし、**[SQL Server]** を選択します
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. 目的のサーバーに移動して選択します
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. 復元する SQL Data Warehouse を見つけて選択します
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Data Warehouse ブレードの上部にある **[復元]** をクリックします
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. 新しい**データベース名**を指定します
7. 最新の**復元ポイント**を選択します
    1. 最新の復元ポイントを選択していることを確認します。復元ポイントは UTC で表示されるため、表示される既定のオプションが最新の復元ポイントではない場合があります。
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. **[OK]** をクリックします。
9. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます

>[AZURE.NOTE] 復元が完了したら、[復旧されたデータベースの最終処理][]に関するガイドに従って、復旧されたデータベースを構成できます。


## 削除されたデータベースの復元

削除されたデータベースを復元するには:

1. [Azure ポータル][]にログインします
2. 画面の左側にある **[参照]** をクリックし、**[SQL Server]** を選択します
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. 目的のサーバーに移動して選択します
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. サーバーのブレードで下にスクロールして [操作] セクションに移動します
5. **[削除済みデータベース]** タイルをクリックします
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. 復元する削除済みデータベースを選択します
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. 新しい**データベース名**を指定します
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. **[OK]** をクリックします。
9. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます

>[AZURE.NOTE] 復元が完了したら、[復旧されたデータベースの最終処理][]に関するガイドに従って、復旧されたデータベースを構成できます。


## 次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database のビジネス継続性の概要]: ./sql-database-business-continuity.md
[概要]: ./sql-data-warehouse-restore-database-overview.md
[ポータル]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST ()]: ./sql-data-warehouse-restore-database-rest-api.md
[復旧されたデータベースの最終処理]: ./sql-database-recovered-finalize.md

<!--MSDN references-->

<!--Blog references-->
[DTU クォータを表示および増量する方法]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_0629_2016-->
<properties
   pageTitle="[データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用して SQL Server データベースを SQL Database に移行する"
   description="Microsoft Azure SQL Database、データベースの移行、Microsoft Azure Database ウィザード"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# [データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用して SQL Server データベースを SQL Database に移行する

SQL Server Management Studio のデータベースの Microsoft Azure Database へのデプロイ ウィザードは、[互換性のある SQL Server データベース](sql-database-cloud-migrate.md)を Azure SQL Database サーバーに直接移行します。

## [データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用する

> [AZURE.NOTE] 以下の手順は、[SQL Database サーバーのプロビジョニング](sql-database-get-started.md)が完了していることを想定しています。

1. 最新バージョンの SQL Server Management Studio があることを確認します。Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。

    > [AZURE.IMPORTANT] 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。

2. Management Studio を開き、オブジェクト エクスプローラーで移行する SQL Server データベースに接続します。
3. オブジェクト エクスプローラーでデータベースを右クリックし、**[タスク]** をポイントして **[データベースの Microsoft Azure SQL データベースへのデプロイ...]** をクリックします。

	![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	デプロイメント ウィザードで、**[次へ]** をクリックして **[接続]** をクリックし、SQL Database サーバーへの接続を構成します。

	![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. [サーバーに接続] ダイアログ ボックスで、接続情報を入力し、SQL Database サーバーに接続します。

	![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	新しいデータベース名の **[新しいデータベース名]** を指定し、**[Microsoft Azure SQL Database のエディション]** ([サービス階層](sql-database-service-tiers.md))、**[データベースの最大サイズ]**、**[サービスの目的]** (パフォーマンス レベル)、および移行プロセスの間にこのウィザードが作成する [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルの **[一時ファイル名]** を設定します。

	![設定のエクスポート](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	データベースを移行するウィザードを完了します。データベースのサイズと複雑さに応じて、デプロイメントには数分から数時間かかる場合があります。このウィザードで互換性の問題が検出された場合、画面にエラーが表示され、移行は続行されません。データベース互換性問題の修正方法については、「[データベースの互換性の問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues.md)」を参照してください。

7.	オブジェクト エクスプローラーを使用して、Azure SQL Database サーバーの移行されたデータベースに接続します。
8.	Azure ポータルを使用して、データベースとそのプロパティを表示します。

## 次のステップ: 互換性の問題の修正

必要に応じて、[データベースの互換性の問題](sql-database-cloud-migrate-fix-compatibility-issues.md)を修正します。

<!---HONumber=AcomDC_0309_2016-->
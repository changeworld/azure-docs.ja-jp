---
title: "SSMS: SQL Server データベースを Azure SQL Database に移行する | Microsoft Docs"
description: "この記事では、SQL Server Management Studio の [データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用して、互換性のある SQL Server データベースを Azure SQL Database サーバーに直接移行する方法について説明します。"
keywords: "Microsoft Azure SQL Database、データベースの移行、Microsoft Azure Database ウィザード"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 91d3fd5e-d035-4c55-b1ea-a7ccc8e0f543
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f4cbfc1a277cbe80edbfbb4dc651adb1972f41b4


---
# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>[データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用して SQL Server データベースを SQL Database に移行する
> [!div class="op_single_selector"]
> * [SSMS の移行ウィザード](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [BACPAC ファイルへのエクスポート](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [BACPAC ファイルからのインポート](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [トランザクション レプリケーション](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

この記事では、SQL Server Management Studio の [データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用して、[互換性のある SQL Server データベース](sql-database-cloud-migrate.md)を Azure SQL Database サーバーに直接移行する方法について説明します。

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>[データベースの Microsoft Azure Database へのデプロイ] ウィザードを使用する
> [!NOTE]
> 以下の手順では、 [SQL Database サーバーのプロビジョニング](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)が完了していると想定しています。
> 
> 

1. 最新バージョンの SQL Server Management Studio があることを確認します。 Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。
   
   > [!IMPORTANT]
   > 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
   > 
   > 
2. Management Studio を開き、オブジェクト エクスプローラーで移行する SQL Server データベースに接続します。
3. オブジェクト エクスプローラーでデータベースを右クリックし、**[タスク]** をポイントして **[データベースの Microsoft Azure SQL データベースへのデプロイ...]** をクリックします。
   
    ![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)
4. デプロイメント ウィザードで、**[次へ]** をクリックして **[接続]** をクリックし、SQL Database サーバーへの接続を構成します。
   
   ![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)
5. [サーバーに接続] ダイアログ ボックスで、接続情報を入力し、SQL Database サーバーに接続します。 既存のサーバーのサーバー名を取得するには、「[View and update an SQL SErver Database server and its settings](sql-database-view-update-server-settings.md)」(SQL Database サーバーとその設定を表示し、更新する) をご覧ください。
   
    ![[タスク] メニューの "Azure へのデプロイ"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)
6. 移行プロセス時にこのウィザードが作成する [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルについて、次の項目を入力します。
   
   * **新しいデータベース名** 
   * **Microsoft Azure SQL Database のエディション** ([サービス レベル](sql-database-service-tiers.md))
   * **データベースの最大サイズ**
   * **サービスの目標** (パフォーマンス レベル)
   * **一時ファイル名**  
   
   ![設定のエクスポート](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)
7. ウィザードを終了します。 データベースのサイズと複雑さに応じて、デプロイメントには数分から数時間かかる場合があります。 このウィザードで互換性の問題が検出された場合、画面にエラーが表示され、移行は続行されません。 データベース互換性問題の修正方法については、「 [データベースの互換性の問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues.md)」を参照してください。
8. オブジェクト エクスプローラーを使用して、Azure SQL Database サーバーの移行されたデータベースに接続します。
9. Azure ポータルを使用して、データベースとそのプロパティを表示します。

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->



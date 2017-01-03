---
title: "SQL Database へ移行する前に SQL Server データベースの互換性に関する問題を修正する | Microsoft Docs"
description: "Microsoft Azure SQL Database、データベースの移行、互換性、SQL Azure の移行ウィザード、SSDT"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7ff52877-5b63-4adc-aa1a-689669a1146e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: e09c60111286681928ee1dd0b08fade7a102d6f2


---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Visual Studio 用の SQL Server Data Tools を使用して Azure SQL Database に SQL Server データベースを移行する
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

この記事では、Azure SQL Database に移行する前に Visual Studio 用の SQL Server Data Tools を使用して SQL Server データベースの互換性問題を検出し修正する方法について説明します。

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Visual Studio 用の SQL Server Data Tools を使用する
Visual Studio 用の SQL Server Data Tools ("SSDT") を使用すると、データベース スキーマを Visual Studio データベース プロジェクトにインポートして分析できます。 分析するには、SQL Database V12 にプロジェクトのターゲット プラットフォームを指定し、プロジェクトを構築します。 構築に成功した場合は、データベースには互換性があります。 構築に失敗した場合、SSDT (またはこのトピックで説明したその他のツールのいずれか) を使用してエラーを解決できます。 プロジェクトが正常に構築されると、それをソース データベースのコピーとして発行できます。 その後、SSDT のデータ比較機能を使用して、ソース データベースから Azure SQL V12 互換のデータベースにデータをコピーできます。 その後、更新されたこのデータベースを移行できます。 このオプションを使用するには、 [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)をダウンロードします。

  ![VSSSDT の移行ダイアグラム](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> スキーマのみの移行が必要な場合は、Visual Studio から Azure SQL Database に直接、発行することができます。 この方法は移行ウィザードのみで処理できる変更よりも多数の変更を、データベース スキーマで行う必要がある場合に使用します。
> 
> 

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Visual Studio 用の SQL Server Data Tools を使用して互換性に関する問題を検出する
1. Visual Studio で **SQL Server オブジェクト エクスプローラー** を開きます。 **[SQL Server の追加]** を使用して、移行されるデータベースを含む SQL Server インスタンスに接続します。 オブジェクト エクスプローラーでデータベースを選択して右クリックし、 **[新しいプロジェクトの作成]**     
   
   ![新しいプロジェクト](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
2. インポート設定を構成し、 **[アプリケーション スコープのオブジェクトのみをインポートする]**チェック ボックスをオンにします。 参照先のログイン、アクセス許可、データベースの設定をインポートするためのオプションの選択を解除します。    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    
3. **[開始]** をクリックしてデータベースをインポートし、データベース内の各オブジェクトの T-SQL スクリプト ファイルが含まれるプロジェクトを作成します。 スクリプト ファイルは、プロジェクト内のフォルダーに入れ子になっています。    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    
4. Visual Studio のソリューション エクスプローラーで、データベース プロジェクトを右クリックして [プロパティ] を選択します。 **[プロジェクトの設定]** ページで、[ターゲット プラットフォーム] を [Microsoft Azure SQL Database V12] に設定します。    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
5. プロジェクトを右クリックして **[ビルド]** をクリックし、プロジェクトを構築します。    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
6. **[エラー一覧]** には、各非互換性が表示されます。 この場合、ユーザー名 NT AUTHORITY\NETWORK SERVICE は互換性がありません。 互換性がないため、これをコメント アウトまたは削除できます (このログインとロールをデータベース ソリューションから削除することによる影響に対応できます)。     
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    

## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Visual Studio 用の SQL Server Data Tools を使用して互換性に関する問題を修正する
1. 最初のスクリプトをダブルクリックしてクエリ ウィンドウでスクリプトを開き、スクリプトをコメント アウトし、スクリプトを実行します。     
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)
2. このプロセスを互換性のない各スクリプトに対して、エラーがなくなるまで繰り返します。    
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
3. データベースのエラーがなくなったら、プロジェクトを右クリックし、 **[発行]**をクリックします。 ソース データベースのコピーが作成され、発行されます (少なくとも最初のうちは、コピーを使用することを強くお勧めします)。     
   
   * 発行前に、ソースの SQL Server のバージョン (SQL Server 2014 より前) に応じて、プロジェクトのターゲット プラットフォームをリセットしてデプロイメントを有効にすることが必要になる場合があります。     
   * 古い SQL Server データベースを移行する場合は、データベースを新しいバージョンの SQL Server に移行するまで、ソースの SQL Server でサポートされていない機能をプロジェクトに導入しないでください。     
     
     ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
     
     ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
4. SQL Server オブジェクト エクスプローラーでソース データベースを右クリックし、 **[データ比較]**をクリックします。 プロジェクトを元のデータベースと比較すると、ウィザードによってどのような変更が加えられたかを把握できます。 Azure SQL V12 バージョンのデータベースを選択し、 **[完了]**をクリックします。    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    
5. 検出された相違点を確認し、 **[Update Target]** をクリックしてデータをソース データベースから Azure SQL V12 データベースに移行します。     
   
   ![alt text](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
6. デプロイメント方法を選択します。  [互換性のある SQL Server データベースの SQL Database への移行](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->



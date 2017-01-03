---
title: "トランザクション レプリケーションを使用して SQL Database に移行する | Microsoft Docs"
description: "Microsoft Azure SQL Database、データベースの移行、データベースのインポート、トランザクション レプリケーション"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
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
ms.openlocfilehash: 891c10b2f8e560a3c97f93198c742f657a92e927


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>トランザクション レプリケーションを使用して SQL Server データベースを Azure SQL Database に移行する
> [!div class="op_single_selector"]
> * [SSMS の移行ウィザード](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [BACPAC ファイルへのエクスポート](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [BACPAC ファイルからのインポート](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [トランザクション レプリケーション](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

この記事では、SQL Server のトランザクション レプリケーションを使用して最小限のダウンタイムで Azure SQL Database へ互換性のある SQL Server データベースを移行する方法について説明します。

## <a name="understanding-the-transactional-replication-architecture"></a>トランザクション レプリケーションのアーキテクチャについて
移行中、SQL Server データベースを外す余裕がない場合、移行ソリューションとして SQL Server トランザクション レプリケーションを使用できます。 このソリューションを使用するには、Azure SQL Database を移行対象のオンプレミス SQL Server インスタンスへのサブスクライバーとして構成します。 オンプレミスのトランザクション レプリケーション ディストリビューターは、新しいトランザクションが発生し続ける中で、同期対象のオンプレミスのデータベース (パブリッシャー) からデータを同期します。 

また、トランザクション レプリケーションを使用し、オンプレミス データベースの一部を移行できます。 Azure SQL Database に複製するパブリケーションは、複製されるデータベースのテーブルの一部に制限できます。 複製されるテーブルごとに、行の一部または列の一部にデータを制限できます。

トランザクション レプリケーションでは、データやスキーマのすべての変更が Azure SQL Database に表示されます。 同期が完了し、移行の準備ができたら、アプリケーションの接続文字列を変更し、Azure SQL Database をポイントするようにします。 トランザクション レプリケーションがオンプレミス データベースに残っているすべての変更を抜き取り、すべてのアプリケーションが Azure DB をポイントしたら、トランザクション レプリケーションをアンインストールできます。 これで、Azure SQL Database が実稼働システムになります。

 ![SeedCloudTR ダイアグラム](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>トランザクション レプリケーションの要件
トランザクション レプリケーションは、SQL Server 6.5 以降、SQL Server に内蔵された技術です。 成熟した証明済みのテクノロジであり、ほとんどの DBA に運用経験があります。 [SQL Server 2016](https://www.microsoft.com/sql-server/sql-server-2016) では、Azure SQL Database をオンプレミス パブリケーションの[トランザクション レプリケーション サブスクライバー](https://msdn.microsoft.com/library/mt589530.aspx)として構成できるようになりました。 Management Studio の設定は、オンプレミス サーバーでトランザクション レプリケーション サブスクライバーを設定する場合と同じです。 このシナリオは、パブリッシャーとディストリビューターの SQL Server のバージョンが次のいずれか以上である場合にサポートされます。

* SQL Server 2016 以降 
* SQL Server 2014 SP1 CU3 以降
* SQL Server 2014 RTM CU10 以降
* SQL Server 2012 SP2 CU8 以降
* SQL Server 2012 SP3 以降

> [!IMPORTANT]
> 最新バージョンの SQL Server Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持します。 以前のバージョンの SQL Server Management Studio では、サブスクライバーとして SQL Database を設定できません。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>その他のリソース
* [トランザクション レプリケーション](https://msdn.microsoft.com/library/mt589530.aspx)
* [SQL Database の機能](sql-database-features.md)
* [Transact-SQL の部分的にサポートされる機能またはまったくサポートされていない機能](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant を使用した SQL Server 以外のデータベースの移行](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->



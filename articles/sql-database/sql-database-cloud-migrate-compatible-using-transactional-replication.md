---
title: "トランザクション レプリケーションを使用して SQL Database に移行する | Microsoft Docs"
description: "Microsoft Azure SQL Database、データベースの移行、データベースのインポート、トランザクション レプリケーション"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


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

## <a name="how-transactional-replication-works"></a>トランザクション レプリケーションのしくみ

トランザクション レプリケーションは、主に 3 つのコンポーネントで構成されます。 それは、パブリッシャー、ディストリビューター、サブスクライバーです。 これらのコンポーネントが連携してレプリケーションが実行されます。 ディストリビューターは、サーバー間でデータを移動するプロセスを制御する役割を担います。 ディストリビューションを設定すると、SQL によってディストリビューション データベースが作成されます。 各パブリッシャーは、ディストリビューション データベースに関連付けられる必要があります。 ディストリビューション データベースは、関連付けられた各パブリケーションのメタデータと、各レプリケーションの進捗状況に関するデータを保持します。 トランザクション レプリケーションの場合、これは、サブスクライバーで実行する必要があるすべてのトランザクションを保持します。

パブリッシャーとは、移行対象のすべてのデータの発行元であるデータベースです。 パブリッシャーの中には、多くのパブリケーションが存在する場合があります。 このようなパブリケーションには、すべてのテーブルにマップされるアーティクルと、レプリケートする必要のあるデータが含まれています。 パブリケーションとアーティクルを定義する方法に応じて、データベースのすべてまたは一部をレプリケートすることができます。 

レプリケーションにおいて、サブスクライバーとは、パブリケーションからデータとトランザクションすべてを受け取るサーバーのことです。 各パブリケーションには、多数のレプリケーションを含めることができます。

## <a name="transactional-replication-requirements"></a>トランザクション レプリケーションの要件
[最新の要件の一覧については、こちらのリンクを参照してください。](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> 最新バージョンの SQL Server Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持します。 以前のバージョンの SQL Server Management Studio では、サブスクライバーとして SQL Database を設定できません。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>トランザクション レプリケーション ワークフローを使用した SQL Database への移行

1. ディストリビューションの設定
   -  [SQL Server Management Studio (SSMS) を使用する](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Transact-SQL を使用する](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. パブリケーションの作成
   -  [SQL Server Management Studio (SSMS) を使用する](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Transact-SQL を使用する](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. サブスクリプションの作成
   -  [SQL Server Management Studio (SSMS) を使用する](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Transact-SQL を使用する](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>SQL Database への移行に関するヒントと相違点

1. ローカル ディストリビューターを使用します。 
   - これにより、サーバーのパフォーマンスに影響が生じます。 
   - パフォーマンスの影響を許容できない場合は、別のサーバーを使用できますが、管理がさらに複雑になります。
2. スナップショット フォルダーを選択する際は、選択したフォルダーが、レプリケートするすべてのテーブルの BCP を保持するのに十分な大きさであることを確認してください。 
3. スナップショットをスケジュールする際は、スナップショットの作成によって、その処理が完了するまで関連付けられたテーブルがロックされることに注意してください。 
4. Azure SQL Database でサポートされているのは、プッシュ サブスクリプションのみです。
   - サブスクライバーを追加できるのは、オンプレミス データベース側からのみです。

## <a name="next-steps"></a>次のステップ
* [最新バージョンの SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [最新バージョンの SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>その他のリソース
* トランザクション レプリケーションの詳細については、[トランザクション レプリケーション](https://msdn.microsoft.com/library/mt589530.aspx)に関するページを参照してください。
* 移行プロセス全体とオプションについては、「[SQL Server データベースのクラウド内の SQL Database への移行](sql-database-cloud-migrate.md)」を参照してください。



<!--HONumber=Dec16_HO2-->



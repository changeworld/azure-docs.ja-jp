---
title: "Azure SQL Database のサービス レベルとパフォーマンス レベル | Microsoft Docs"
description: "SQL Database の単一データベースのサービス レベルとパフォーマンス レベルを比較し、SQL エラスティック プールを紹介します"
keywords: "データベース オプション, データベース パフォーマンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: a3c287c5317bd7db2b560e37ddacc9e43d7292d1
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="what-performance-options-are-available-for-an-azure-sql-database"></a>Azure SQL Database で利用可能なパフォーマンス オプション

[Azure SQL Database](sql-database-technical-overview.md) には、単一データベースと[プールされた](sql-database-elastic-pool.md)データベースのそれぞれに 4 つのサービス レベルが用意されています。 サービス レベルは、**Basic**、**Standard**、**Premium**、**Premium RS** です。 各サービス レベルには、異なるワークロードとデータ サイズを処理するために、複数のパフォーマンス レベル ([DTU](sql-database-what-is-a-dtu.md)) とストレージ オプションがあります。 パフォーマンス レベルが高くなるほど提供されるコンピューティング リソースとストレージ リソースが追加され、スループットとキャパシティが段階的に高くなるように設計されています。 ダウンタイムを発生させることなく、サービス レベル、パフォーマンス レベル、ストレージを動的に変更できます。 
- **Basic**、**Standard**、**Premium** のサービス レベルは、いずれもアップタイムの SLA が 99.99% で、柔軟なビジネス継続性のオプション、セキュリティ機能、時間単位の課金体系が用意されています。 
- **Premium RS** レベルは、Premium レベルと同じパフォーマンス レベルですが、他のサービス レベルのデータベースよりも少ない冗長コピーで実行されるため、SLA が低下します。 そのため、サービスで障害が発生した場合は、最大 5 分間の遅延があるバックアップからデータベースを復旧する必要があります。

> [!IMPORTANT]
> Azure SQL データベースは所定のリソースを取得します。データベースの期待されるパフォーマンス特性は Azure 内の他のデータベースの影響を受けません。 

## <a name="choosing-a-service-tier"></a>サービス階層の選択
次の表では、各種のアプリケーション ワークロードに最適なサービス階層の例を示します。

| サービス階層 | 対象のワークロード |
| :--- | --- |
| **Basic** | 小規模なデータベースに最適です。通常は、一度に 1 つのアクティブな操作をサポートします。 たとえば、開発やテスト、使用頻度の低い小規模なアプリケーションなどに使用するデータベースがこれに該当します。 |
| **Standard** |IO パフォーマンス要件が低～中程度のクラウド アプリケーションに適しています。複数の同時クエリをサポートします。 たとえば、ワークグループや Web アプリケーションなどです。 |
| **Premium** | IO パフォーマンス要件が高く、トランザクション量が膨大な場合に合わせて設計されています。多数の同時ユーザーをサポートしています。 たとえば、ミッション クリティカルなアプリケーションをサポートするデータベースなどです。 |
| **Premium RS** | 最高レベルの可用性の保証を必要としない I/O 集中型のワークロード向けに設計されています。 例としては、高パフォーマンス ワークロードまたはデータベースがレコードのシステムではない分析ワークロードのテストが含まれます。 |
|||

あるサービス レベルの特定の[パフォーマンス レベル](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)で、専用リソースを含む単一データベースを作成できます。また、[SQL エラスティック プール](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)内にデータベースを作成することもできます。 SQL エラスティック プールでは、コンピューティング リソースとストレージ リソースが単一の論理サーバー内の複数のデータベース間で共有されます。 

単一データベースで利用できるリソースは、データベース トランザクション ユニット (DTU) を単位として表現されます。これに対して、SQL エラスティック プールで利用できるリソースは、エラスティック データベース トランザクション ユニット (eDTU) を単位として表現されます。 DTU と eDTU の詳細については、[DTU と eDTU の概要](sql-database-what-is-a-dtu.md)に関する記事を参照してください。

サービス レベルを決定するには、まず、最低限必要なデータベース機能を確認します。

| **サービス レベルの機能** | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 単一データベースの最大サイズ | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| エラスティック プールの最大サイズ | 156 GB | 2.9 TB | 4 TB* | 750 GB |
| エラスティック プール内のデータベースの最大サイズ | 2 GB | 250 GB | 500 GB | 500 GB |
| プールあたりのデータベースの最大数 | 500  | 500 | 100 | 100 |
| 単一データベースの最大 DTU | 5 | 100 | 4000 | 1,000 |
| エラスティック プール内のデータベースあたりの最大 DTU | 5 | 3000 | 4000 | 1,000 |
| データベース バックアップのリテンション期間 | 7 日 | 35 日 | 35 日 | 35 日 |
||||||

> [!IMPORTANT]
> 最大 4 TB のストレージは現在、米国東部 2、米国西部、米国政府バージニア州、西ヨーロッパ、ドイツ中部、東南アジア、東日本、オーストラリア東部、カナダ中部、カナダ東部の各リージョンで利用できます。 詳細については、[4 TB のデータベースの現時点での制限事項](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)に関するセクションを参照してください。
>

適切なサービス レベルを決定したら、データベースのパフォーマンス レベル (DTU 数) とストレージ容量を決定できるようになります。 

- 多くの場合、出発点として **Standard** レベルの S2 および S3 というパフォーマンス レベルが適しています。 
- CPU または IO の要件が高いデータベースの場合は、**Premium** レベルのパフォーマンス レベルが出発点として適しています。 
- **Premium** レベルでは、より多くの CPU が提供されるため、**Standard** レベルの最も高いパフォーマンス レベルと比較して 10 倍超える IO から始まります。
- **Premium RS** レベルでは、低コストで **Premium** レベルのパフォーマンスが提供されますが、SLA が低下します。

> [!IMPORTANT]
> データベースを SQL エラスティック プールにグループ化してコンピューティング リソースとストレージ リソースを共有する方法の詳細については、[SQL エラスティック プール](sql-database-elastic-pool.md)に関するトピックを参照してください。 このトピックの残りの部分では、単一データベースのサービス レベルとパフォーマンス レベルを中心に説明します。
>

## <a name="single-database-service-tiers-and-performance-levels"></a>単一データベース サービス階層とパフォーマンス レベル
単一データベースでは、各サービス レベル内に複数のパフォーマンス レベルとストレージ容量があります。 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="scaling-up-or-scaling-down-a-single-database"></a>単一データベースのスケールアップとスケールダウン

最初にサービス レベルとパフォーマンス レベルを選んだ後、実際の使用感に基づいて、単一データベースを動的にスケールアップまたはスケールダウンすることができます。  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。 このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。 切り替え時間はさまざまですが、通常 4 秒以内であり、99% 以上が 30 秒未満です。 接続が無効になった時点で多数のトランザクションが実行中の場合、切り替え時間が長引くことがあります。  

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。 たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。 Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合は、3 時間以内で完了します。

> [!TIP]
> 実行中の SQL Database のスケーリング操作について、その状態をチェックするには、```select * from sys.dm_operation_status``` というクエリを使用します。
>

* より上位のサービス レベルまたはパフォーマンス レベルにアップグレードしても、より大きな最大サイズを明示的に指定しない限り、最大データベース サイズは増加しません。
* データベースをダウングレードするには、データベースがダウングレード後のサービス レベルで許可されている最大サイズより小さい必要があります。 
* [geo レプリケーション](sql-database-geo-replication-portal.md)が有効な状態でデータベースをアップグレードする場合、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードします (一般的なガイダンス)。
* **Premium** サービス レベルから下位のサービス レベルにダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。 [障害からの回復](sql-database-disaster-recovery.md)に関する記事に記載されている手順に従って、プライマリ データベースとセカンダリ データベース間のレプリケーション プロセスを停止できます。
* サービス階層によって、提供されている復元サービスは異なります。 **Basic** レベルにダウングレードする場合は、バックアップのリテンション期間が短くなります。[Azure SQL Database のバックアップ](sql-database-automated-backups.md)に関する記事を参照してください。
* データベースに対する新しいプロパティは、変更が完了するまで適用されません。


## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>最大サイズ 4 TB の P11 および P15 データベースの現時点での制限事項

(前に説明したように) P11 および P15 データベースの 4 TB の最大サイズは、一部のリージョンでサポートされています。 最大サイズ 4 TB の P11 および P15 データベースには、次の考慮事項と制限事項が適用されます。

- データベースを作成するときに (4 TB または 4096 GB の値を使用して) 4 TB の最大サイズのオプションを選択した場合、サポートされていないリージョンでデータベースをプロビジョニングすると、エラーのため、create コマンドが失敗します。
- サポート対象のリージョンにある既存の P11 と P15 のデータベースでは、ストレージの最大サイズを 4 TB に増やすことができます。 これを確認するには、[SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) を使用するか、Azure Portal でデータベース サイズを調べます。 既存の P11 または P15 のデータベースのアップグレードは、サーバーレベル プリンシパルのログインまたは dbmanager データベース ロールのメンバーのみが実行できます。 
- サポートされているリージョンでアップグレード操作が実行されると、すぐに構成が更新されます。 データベースは、アップグレード処理中もオンラインのままになります。 ただし、実際のデータベース ファイルが新しい最大サイズにアップグレードされるまで、4 TB のストレージを十分に利用することはできません。 必要な時間は、アップグレードされるデータベースのサイズに応じて異なります。  
- P11 または P15 データベースを作成または更新する際に、最大サイズとして 1 TB と 4 TB のいずれかのみを選択できます。 現在、中間のストレージ サイズはサポートされていません。 P11/P15 を作成するとき、既定で 1 TB のストレージ オプションがあらかじめ選択されています。 サポート対象のリージョンにあるデータベースでは、新規または既存の単一データベースのストレージの最大サイズを 4 TB に増やすことができます。 他のすべてのリージョンでは、最大サイズが 1 TB を超えることはできません。 4 TB の付属のストレージを選択しても、価格は変わりません。
- 実際に使用されるストレージが 1 TB 未満の場合でも、データベースの最大サイズ 4 TB を 1 TB に変更することはできません。 したがって、他のパフォーマンス レベルに追加のストレージ オプションが提供されない限り、P11-4TB/P15-4TB を P11-1TB/P15-1TB またはさらに下位のパフォーマンス レベル (たとえば、P1-P6) にダウングレードすることはできません。 この制限は、ポイントインタイム リストア、geo リストア、長期的なバックアップ保有期間、データベース コピーなど、復元とコピーのシナリオにも適用されます。 データベースを 4 TB のオプションを使用して構成すると、このデータベースのすべての復元操作により、最大サイズ 4 TB の P11/P15 になります。
- サポートされていないリージョンで P11/P15 データベースを作成またはアップグレードすると、アップグレード操作が失敗し、"**P11 and P15 database with up to 4TB of storage are available in US East2, West US, US Gov Virginia, West Europe, Germany Central, South East Asia, Japan East, Australia East, Canada Central, and Canada East. (最大 4 TB のストレージを使用する P11 および P15 データベースは、米国東部 2、米国西部、米国政府バージニア、西ヨーロッパ、ドイツ中部、東南アジア、東日本、オーストラリア東部、カナダ中部、カナダ東部で利用可能です)**" というエラー メッセージが表示されます。
- アクティブ geo レプリケーションのシナリオの場合:
   - geo レプリケーションのリレーションシップの設定: プライマリ データベースが P11 または P15 の場合は、セカンダリも P11 または P15 である必要があります。つまり、下位のパフォーマンス レベルは、4 TB をサポートできないため、セカンダリとして拒否されます。
   - geo レプリケーションのリレーションシップでのプライマリ データベースのアップグレード: プライマリ データベースで最大サイズを 4 TB に変更すると、セカンダリ データベースでも同じ変更がトリガーされます。 プライマリに対する変更を有効にするには、両方のアップグレードが正常に完了する必要があります。 4 TB のオプションに関するリージョンの制限が適用されます (上記参照)。 4 TB をサポートしていないリージョンにセカンダリが存在する場合、プライマリはアップグレードされません。
- P11-4TB/P15-4TB データベースの読み込みに Import/Export サービスを使用することはサポートされていません。 SqlPackage.exe を使用して、データを[インポート](sql-database-import.md)および[エクスポート](sql-database-export.md)してください。

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal"></a>Azure Portal を使用した単一データベースのサービス レベルとパフォーマンス レベルの管理

Azure Portal を使用して、新規または既存の Azure SQL データベースのサービス レベル、パフォーマンス レベル、またはストレージ容量を設定または変更するには、次のスクリーンショットに示すように、**[価格レベル (DTU のスケール)]** をクリックし、データベースの **[パフォーマンスの構成]** ウィンドウを開きます。 

- ワークロードのサービス レベルを選択して、サービス レベルを設定または変更します。 
- **[DTU]** スライダーを使用して、サービス レベル内のパフォーマンス レベル (**DTU**) を設定または変更します。
- **[ストレージ]** スライダーを使用して、パフォーマンス レベルのストレージ容量を設定または変更します。 

  ![サービス レベルとパフォーマンス レベルの構成](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> P11 または P15 サービス レベルを選択する場合は、「[最大サイズ 4 TB の P11 および P15 データベースの現時点での制限事項](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)」を参照してください。
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-powershell"></a>PowerShell を使用した単一データベースのサービス レベルとパフォーマンス レベルの管理

PowerShell を使用して、Azure SQL データベースのサービス レベル、パフォーマンス レベル、およびストレージ容量を設定または変更するには、次の PowerShell コマンドレットを使用します。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 

| コマンドレット | Description |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|データベースを作成します。 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|1 つまたは複数のデータベースを取得します。|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|データベースのプロパティを設定するか、既存のデータベースをエラスティック プールに移動します。|


> [!TIP]
> データベースのパフォーマンス メトリックを監視し、そのデータベースを上位のパフォーマンス レベルにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成する PowerShell のサンプル スクリプトについては、「[PowerShell を使用して単一の SQL データベースを監視およびスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md)」を参照してください。

## <a name="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli"></a>Azure CLI を使用した単一データベースのサービス レベルとパフォーマンス レベルの管理

Azure CLI を使用して、Azure SQL データベースのサービス レベル、パフォーマンス レベル、およびストレージ容量を設定または変更するには、次の [Azure CLI SQL Database](/cli/azure/sql/db) コマンドを使用します。 [Cloud Shell](/azure/cloud-shell/overview) を使用して CLI をブラウザーで実行することも、macOS、Linux、または Windows に[インストール](/cli/azure/install-azure-cli)することもできます。 SQL エラスティック プールの作成と管理については、[エラスティック プール](sql-database-elastic-pool.md)に関する記事を参照してください。

| コマンドレット | Description |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |データベースを作成します。|
|[az sql db list](/cli/azure/sql/db#list)|サーバー内のすべてのデータベースとデータ ウェアハウス、またはエラスティック プール内のすべてのデータベースを一覧表示します。|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|利用可能なサービス目標と容量の上限を一覧表示します。|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|データベースの使用状況を返します。|
|[az sql db show](/cli/azure/sql/db#show)|データベースまたはデータ ウェアハウスを取得します。|
|[az sql db update](/cli/azure/sql/db#update)|データベースを更新します。|

> [!TIP]
> 単一の Azure SQL データベースのサイズ情報を照会した後に、そのデータベースを別のパフォーマンス レベルにスケーリングする Azure CLI のサンプル スクリプトについては、「[CLI を使用して 1 つの SQL データベースを監視およびスケーリングする](scripts/sql-database-monitor-and-scale-database-cli.md)」を参照してください。
>

## <a name="manage-single-database-service-tiers-and-performance-levels-using-transact-sql"></a>Transact-SQL を使用した単一データベースのサービス レベルとパフォーマンス レベルの管理

Transact-SQL を使用して、Azure SQL データベースのサービス レベル、パフォーマンス レベル、およびストレージ容量を設定または変更するには、次の T-SQL コマンドを使用します。 これらのコマンドは、Azure Portal、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)、または Azure SQL Database サーバーに接続して Transact-SQL コマンドを渡すことができるその他のプログラムを使用して実行できます。 

| コマンド | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|新しいデータベースを作成します。 新しいデータベースを作成するには、マスター データベースに接続する必要があります。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL データベースを変更します。 |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database または Azure SQL Data Warehouse のエディション (サービス レベル)、サービス目標 (価格レベル)、およびエラスティック プール名 (存在する場合) を返します。 Azure SQL Database サーバーの master データベースにログオンしている場合は、すべてのデータベースの情報が返されます。 Azure SQL Data Warehouse の場合は、master データベースに接続する必要があります。|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Azure SQL Database サーバー上のデータベースの数、種類、および期間を一覧表示します。|

次のコードは、ALTER DATABASE コマンドを使用した最大サイズの変更の例を示しています。

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-databases-using-the-rest-api"></a>REST API を使用した単一データベースの管理

REST API を使用して、Azure SQL データベースのサービス レベル、パフォーマンス レベル、およびストレージ容量を設定または変更するには、「[Azure SQL Database REST API](/rest/api/sql/)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [DTU](sql-database-what-is-a-dtu.md) の詳細を参照してください。
* DTU の使用状況の監視については、「[監視とパフォーマンスのチューニング](sql-database-troubleshoot-performance.md)」を参照してください。



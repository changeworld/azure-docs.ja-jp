---
title: 'Azure SQL Database の DTU ベースのリソース制限: 単一データベース | Microsoft Docs'
description: このページでは、Azure SQL Database の単一データベースに対するいくつかの一般的な DTU ベースのリソース制限について説明します。
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: carlrab
ms.openlocfilehash: b8c1a0a0c4f2b3b33338f1f76245fd4bcb66ad61
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139936"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>DTU ベースの購入モデルを使用した単一データベースに対するリソース制限 

この記事では、DTU ベースの購入モデルを使用した、Azure SQL Database の単一データベースに対する詳細なリソース制限について説明します。

エラスティック プールに対する DTU ベースの購入モデルのリソース制限については、[エラスティック プールに対する DTU ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md)に関するページを参照してください。 [単一データベースに対する仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md)および[エラスティック プールに対する仮想コアベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md)に関するページを参照してください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="single-database-storage-sizes-and-performance-levels"></a>単一データベース: ストレージ サイズとパフォーマンス レベル

次の表では、各サービス レベルおよびパフォーマンス レベルにおいて単一データベースで使用可能なリソースを示します。 [Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases)、または [REST API](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases) を使用して、単一データベースのサービス レベル、パフォーマンス レベル、ストレージ量を設定できます。

### <a name="basic-service-tier"></a>Basic サービス レベル
| **パフォーマンス レベル** | **Basic** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 付属ストレージ (GB) | 2 |
| 選択可能な最大ストレージ容量 (GB) | 2 |
| 最大インメモリ OLTP ストレージ容量 (GB) |該当なし |
| 最大同時実行ワーカー (要求) 数 | 30 |
| 最大同時セッション数 | 300 |
|||

### <a name="standard-service-tier"></a>Standard サービス レベル
| **パフォーマンス レベル** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU 数 | 10 | 20 | 50 | 100 |
| 付属ストレージ (GB) | 250 | 250 | 250 | 250 |
| 選択可能な最大ストレージ容量 (GB) | 250 | 250 | 250 | 250、500、750、1024 |
| 最大インメモリ OLTP ストレージ容量 (GB) | 該当なし | 該当なし | 該当なし | 該当なし |
| 最大同時実行ワーカー (要求) 数| 60 | 90 | 120 | 200 |
| 最大同時セッション数 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard サービス レベル (続き)
| **パフォーマンス レベル** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 付属ストレージ (GB) | 250 | 250 | 250 | 250 | 250 |
| 選択可能な最大ストレージ容量 (GB) | 250、500、750、1024 | 250、500、750、1024 | 250、500、750、1024 | 250、500、750、1024 | 250、500、750、1024 |
| 最大インメモリ OLTP ストレージ容量 (GB) | 該当なし | 該当なし | 該当なし | 該当なし |該当なし |
| 最大同時実行ワーカー (要求) 数| 400 | 800 | 1600 | 3200 |6000 |
| 最大同時セッション数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium サービス レベル 
| **パフォーマンス レベル** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1,000 | 1750 | 4000 |
| 付属ストレージ (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| 選択可能な最大ストレージ容量 (GB) | 500、750、1024 | 500、750、1024 | 500、750、1024 | 500、750、1024 | 4096 | 4096 |
| 最大インメモリ OLTP ストレージ容量 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 最大同時実行ワーカー (要求) 数| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 最大同時セッション数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> 現在、米国中西部、中国東部、US DoD 中部、ドイツ中部、US DoD 東部、US Gov 南西部、ドイツ北東部、USGov アイオワ、中国北部を除くすべてのリージョンでは、Premium レベルで 1 TB を超えるストレージを使用できます。 それ以外のリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 [P11 ～ P15 の現時点での制限](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。  

## <a name="single-database-change-storage-size"></a>単一データベース: ストレージ サイズを変更する

- 単一データベースの DTU 価格には、追加コストなしで一定量のストレージが含まれます。 付属の容量を超える分のストレージについては、追加費用を払うことで、1 TB までは 250 GB 単位で、1 TB 以降は 256 GB 単位で、最大サイズ制限までプロビジョニングできます。 付属するストレージの量と最大サイズ制限については、「[単一データベース: ストレージ サイズとパフォーマンス レベル](#single-database-storage-sizes-and-performance-levels)」をご覧ください。
- 単一データベースの追加ストレージは、[Azure Portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update)、または [REST API](/rest/api/sql/databases/update) を使ってサイズを最大に増やすことでプロビジョニングできます。
- 単一データベースの追加ストレージの料金は、追加ストレージ量にサービス レベルの追加ストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

## <a name="single-database-change-dtus"></a>単一データベース: DTU を変更する

サービス レベル、パフォーマンス レベル、およびストレージ量を最初に選択した後は、[Azure Portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update)、または [REST API](/rest/api/sql/databases/update) を使い、実際の状況に基づいて、単一データベースを動的にスケールアップまたはスケールダウンできます。 

次のビデオでは、パフォーマンス レベルを動的に変更して単一データベースで使用可能な DTU を増やす方法を示します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。 このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。 切り替え時間はさまざまですが、99% 以上が 30 秒未満です。 接続が無効になった時点で多数のトランザクションが実行中の場合、切り替え時間が長引くことがあります。 

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。 たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。 Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合、スケールアップは 3 時間以内で完了します。

> [!TIP]
> 実行中の操作の監視については、[SQL REST API を使った操作の管理](/rest/api/sql/Operations/List)、[CLI を使った操作の管理](/cli/azure/sql/db/op)、[T-SQL を使った操作の管理](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)に関する各ページと、2 つの PowerShell コマンド [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) と [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity) をご覧ください。

* より上位のサービス レベルまたはパフォーマンス レベルにアップグレードしても、より大きなサイズ (最大サイズ) を明示的に指定しない限り、データベースの最大サイズは増加しません。
* データベースをダウングレードするには、データベースで使われている領域がダウングレード後のサービス レベルとパフォーマンス レベルで許可されている最大サイズより小さい必要があります。 
* **Premium** から **Standard** レベルにダウングレードするときは、(1) データベースの最大サイズがターゲットのパフォーマンス レベルでサポートされていて、かつ、(2) 最大サイズがターゲットのパフォーマンス レベルで付属のストレージ量を超えている場合、追加ストレージ コストが適用されます。 たとえば、最大サイズ 500 GB の P1 データベースを S3 にダウンサイズする場合、S3 がサポートする最大サイズは 500 GB であり、S3 で付属のストレージ量は 250 GB だけなので、追加ストレージ コストが適用されます。 したがって、追加ストレージ量は 500 GB – 250 GB = 250 GB になります。 追加ストレージの価格については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使われる領域の量が付属のストレージの量より少ない場合、データベースの最大サイズを付属の量に減らすことで、この追加コストを回避できます。 
* [geo レプリケーション](sql-database-geo-replication-portal.md)が有効な状態でデータベースをアップグレードする場合、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにアップグレードする場合は、最初にセカンダリ データベースのアップグレードが必要です。
* [geo レプリケーション](sql-database-geo-replication-portal.md)が有効な状態でデータベースをダウングレードする場合、プライマリ データベースを目的のパフォーマンス レベルにダウングレードしてから、セカンダリ データベースをダウングレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにダウングレードする場合は、最初にプライマリ データベースのダウングレードが必要です。
* サービス階層によって、提供されている復元サービスは異なります。 **Basic** レベルにダウングレードする場合は、バックアップのリテンション期間が短くなります。「[SQL Database 自動バックアップについての詳細情報](sql-database-automated-backups.md)」をご覧ください。
* データベースに対する新しいプロパティは、変更が完了するまで適用されません。

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>単一データベース: 最大サイズが 1 TB を超える場合の P11 および P15 の制限事項

次のリージョンでは、最大サイズが 1 TB を超える P11 および P15 データベースがサポートされます。オーストラリア東部、オーストラリア南東部、ブラジル南部、カナダ中部、カナダ東部、米国中部、フランス中部、ドイツ中部、東日本、西日本、韓国中部、米国中北部、北ヨーロッパ、米国中南部、東南アジア、英国南部、英国西部、米国東部 2、米国西部、米国政府バージニア、および西ヨーロッパ。 最大サイズが 1 TB を超える P11 および P15 データベースには、次の考慮事項と制限事項が適用されます。

- データベースを作成するときに (4 TB または 4096 GB の値を使用して) 1 TB を超える最大サイズを選択した場合、サポートされていないリージョンでデータベースをプロビジョニングすると、エラーのため、create コマンドが失敗します。
- サポート対象のリージョンにある既存の P11 と P15 のデータベースでは、最大ストレージを 1 TB を超えて最大 4 TB まで 256 GB 刻みで増やすことができます。 対象リージョンでより大きなサイズがサポートされているかどうかを確認するには、[DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) 関数を使うかか、Azure Portal でデータベースのサイズを調べてください。 既存の P11 または P15 のデータベースのアップグレードは、サーバーレベル プリンシパルのログインまたは dbmanager データベース ロールのメンバーのみが実行できます。 
- サポートされているリージョンでアップグレード操作が実行されると、すぐに構成が更新されます。 データベースは、アップグレード処理中もオンラインのままになります。 ただし、実際のデータベース ファイルが新しい最大サイズにアップグレードされるまで、1 TB を超えるストレージの容量を完全に利用することはできません。 必要な時間は、アップグレードされるデータベースのサイズに応じて異なります。 
- P11 または P15 データベースを作成または更新するときは、1 TB から 4 TB の範囲で、256 GB 刻みでのみ最大サイズを選択できます。 P11/P15 を作成するとき、既定で 1 TB のストレージ オプションがあらかじめ選択されています。 サポート対象のリージョンにあるデータベースでは、新規または既存の単一データベースのストレージの最大サイズを最大 4 TB まで増やすことができます。 他のすべてのリージョンでは、最大サイズが 1 TB を超えることはできません。 4 TB の付属のストレージを選択しても、価格は変わりません。
- データベースの最大サイズを 1 TB より大きい値に設定した場合は、ストレージの実際の使用量が 1 TB を下回る場合でも、1 TB に変更することはできません。 したがって、最大サイズが 1 TB より大きい P11 または P15 を、1 TB の P11、1 TB の P15、またはそれより低いパフォーマンス レベル (P1-P6 など) にダウングレードすることはできません。 この制限は、ポイントインタイム リストア、geo リストア、長期的なバックアップ保有期間、データベース コピーなど、復元とコピーのシナリオにも適用されます。 データベースを 1 TB より大きい最大サイズで構成した後、そのデータベースのすべての復元操作は、最大サイズが 1 TB より大きい P11/P15 に対して実行する必要があります。
- アクティブ geo レプリケーションのシナリオの場合:
   - geo レプリケーションのリレーションシップの設定: プライマリ データベースが P11 または P15 の場合は、セカンダリも P11 または P15 である必要があります。つまり、下位のパフォーマンス レベルは、1 TB を超えるサイズをサポートできないため、セカンダリとして拒否されます。
   - geo レプリケーションのリレーションシップでのプライマリ データベースのアップグレード: プライマリ データベースで最大サイズを 1 TB 超に変更すると、セカンダリ データベースでも同じ変更がトリガーされます。 プライマリに対する変更を有効にするには、両方のアップグレードが正常に完了する必要があります。 1 TB を超えるオプションに関するリージョンの制限が適用されます。 1 TB 超をサポートしていないリージョンにセカンダリが存在する場合、プライマリはアップグレードされません。
- 1 TB を超える P11/P15 データベースの読み込みに Import/Export サービスを使うことはサポートされていません。 SqlPackage.exe を使用して、データを[インポート](sql-database-import.md)および[エクスポート](sql-database-export.md)してください。

## <a name="next-steps"></a>次の手順

- よく寄せられる質問の回答については、「[SQL Database に関する FAQ](sql-database-faq.md)」を参照してください。
- サーバーおよびサブスクリプション レベルの制限については詳しくは、「[Azure SQL Database のリソース制限の概要](sql-database-resource-limits.md)」をご覧ください。
- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。
- DTU と eDTU については、「[データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) の説明](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)」をご覧ください。
- tempdb のサイズ制限については、https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database を参照してください。

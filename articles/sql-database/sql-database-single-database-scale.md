---
title: 単一データベースのリソースをスケーリングする
description: この記事では、Azure SQL Database で単一データベースに使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/30/2020
ms.openlocfilehash: a13b860e01e7ef295df629d79dfa44700b5f0d02
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691588"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Database で単一データベースのリソースをスケーリングする

この記事では、プロビジョニングされたコンピューター レベルで Azure SQL データベース用に使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。 また、[サーバーレス コンピューティング レベル](sql-database-serverless.md)は、コンピューティングの自動スケーリングと、使用されたコンピューティングの 1 秒あたりの請求額を提供します。

最初に仮想コア数または DTU 数を選択すると、[Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) を使い、実際のエクスペリエンスに基づいて、単一データベースを動的にスケールアップまたはスケールダウンできます。

次のビデオでは、サービス レベルとコンピューティング サイズを動的に変更して単一データベースで使用可能な DTU を増やす方法を示します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="impact"></a>影響

サービス レベルまたはコンピューティング サイズの変更には、主に次の手順を実行するサービスが含まれます。

1. データベース用に新しいコンピューティング インスタンスを作成する  

    要求されたサービス レベルとコンピューティング サイズを持つ新しいコンピューティング インスタンスが作成されます。 サービス レベルとコンピューティング サイズの変更の組み合わせの中には、新しいコンピューティング インスタンスにデータベースのレプリカを作成する必要があるものがあります。これにはデータのコピーも含まれ、全体の待機時間に大きく影響する場合があります。 いずれにしても、この手順の間、データベースはオンラインのままで、接続は元のコンピューティング インスタンスのデータベースに引き続き向けられます。

2. 接続のルーティングを新しいコンピューティング インスタンスに切り替える

    元のコンピューティング インスタンス内のデータベースへの既存の接続が削除されます。 新しいコンピューティング インスタンス内のデータベースへの新しい接続が確立されます。 サービス レベルとコンピューティング サイズの一部の組み合わせの変更では、データベース ファイルがデタッチされ、切り替え時に再アタッチされます。  いずれにしても、切り替えにより、データベースが使用できなくなる短時間の中断が発生する場合があります。中断は通常は 30 秒未満で、多くの場合はほんの数秒です。 接続が削除されたときに長時間実行されているトランザクションがあると、切断されたトランザクションを復旧するために、この手順の所要時間が長くなる場合があります。 [高速データベースの復旧](sql-database-accelerated-database-recovery.md)により、長時間実行されているトランザクションの中断による影響を軽減できます。

> [!IMPORTANT]
> ワークフローのいずれの手順でもデータが失われることはありません。 サービス レベルの変更中に Azure SQL Database を使用するアプリケーションとコンポーネントに、何らかの[再試行ロジック](sql-database-connectivity-issues.md)を実装したことを確認してください。

## <a name="latency"></a>Latency 

サービス レベルの変更、単一データベースまたはエラスティック プールのコンピューティング サイズのスケーリング、エラスティック プールとの間のデータベースの移動、またはエラスティック プール間でのデータベースの移動に伴う推定待ち時間は、次のようにパラメーター化されます。

|サービス階層|Basic 単一データベース、</br>Standard (S0-S1)|Basic エラスティック プール、</br>Standard (S2-S12)、 </br>ハイパースケール、 </br>汎用の単一データベースまたはエラスティック プール|Premium または Business Critical の単一データベースまたはエラスティック プール|
|:---|:---|:---|:---|
|**Basic 単一データベース、</br>Standard (S0-S1)**|&bull; &nbsp;使用される領域とは関係ない一定時間の待機時間</br>&bull; &nbsp;通常は 5 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|
|**Basic エラスティック プール、</br>Standard (S2-S12)、</br>ハイパースケール、</br>汎用の単一データベースまたはエラスティック プール**|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;使用される領域とは関係ない一定時間の待機時間</br>&bull; &nbsp;通常は 5 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|
|**Premium または Business Critical の単一データベースまたはエラスティック プール**|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|&bull; &nbsp;データのコピーのために使用されるデータベース領域に比例した待機時間</br>&bull; &nbsp;通常、使用される領域の GB あたり 1 分未満|

> [!NOTE]
> さらに、Standard (S2-S12) データベースと General Purpose データベースでは、データベースで Premium ファイル共有 ([PFS](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)) ストレージが使用されている場合、エラスティック プールとの間、またはエラスティック プール間でデータベースを移動するための待ち時間は、データベース サイズに比例します。
>
> データベースで PFS ストレージが使用されているかどうかを確認するには、データベースのコンテキストで次のクエリを実行します。 AccountType 列の値が `PremiumFileStorage` の場合、データベースで PFS ストレージが使用されています。
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> 実行中の操作の監視については、[SQL REST API を使った操作の管理](https://docs.microsoft.com/rest/api/sql/operations/list)、[CLI を使った操作の管理](/cli/azure/sql/db/op)、[T-SQL を使った操作の管理](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)に関する各ページと、2 つの PowerShell コマンド [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) と [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

## <a name="cancelling-changes"></a>変更の取り消し

サービス レベルの変更またはコンピューティングの再スケーリング操作を取り消すことができます。

#### <a name="azure-portal"></a>Azure portal

[データベースの概要] ブレードで、 **[通知]** に移動し、進行中の操作があることを示すタイルをクリックします。

![進行中の操作](media/sql-database-single-database-scale/ongoing-operations.png)

次に、 **[この操作を取り消す]** というラベルのボタンをクリックします。

![進行中の操作の取り消し](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

PowerShell コマンド プロンプトで `$resourceGroupName`、`$serverName`、および `$databaseName` を設定した後、次のコマンドを実行します。

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>その他の注意点

- より上位のサービス レベルまたはコンピューティング サイズにアップグレードしても、より大きなサイズ (最大サイズ) を明示的に指定しない限り、データベースの最大サイズは増加しません。
- データベースをダウングレードするには、データベースで使われている領域がダウングレード後のサービス レベルとコンピューティング サイズで許可されている最大サイズより小さい必要があります。
- **Premium** から **Standard** レベルにダウングレードするときは、(1) データベースの最大サイズがターゲットのコンピューティング サイズでサポートされていて、かつ、(2) 最大サイズがターゲットのコンピューティング サイズで付属のストレージ容量を超えている場合、追加ストレージ コストが適用されます。 たとえば、最大サイズ 500 GB の P1 データベースを S3 にダウンサイズする場合、S3 がサポートする最大サイズは 1 TB であり、S3 で付属のストレージ容量は 250 GB だけなので、追加ストレージ コストが適用されます。 したがって、追加ストレージ容量は 500 GB – 250 GB = 250 GB になります。 追加ストレージの価格については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使われる領域の量が付属のストレージの量より少ない場合、データベースの最大サイズを付属の量に減らすことで、この追加コストを回避できます。
- [geo レプリケーション](sql-database-geo-replication-portal.md)が有効な状態でデータベースをアップグレードする場合、そのセカンダリ データベースを目的のサービス レベルとコンピューティング サイズにアップグレードしてから、プライマリ データベースをアップグレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにアップグレードするには、セカンダリ データベースを先にアップグレードする必要があります。
- [geo レプリケーション](sql-database-geo-replication-portal.md)が有効な状態でデータベースをダウングレードする場合、そのプライマリ データベースを目的のサービス レベルとコンピューティング サイズにダウングレードしてから、セカンダリ データベースをダウングレードします (パフォーマンスを最大にするための一般的なガイダンス)。 別のエディションにダウングレードするには、プライマリ データベースを先にダウングレードする必要があります。
- サービス階層によって、提供されている復元サービスは異なります。 **Basic** レベルにダウングレードする場合は、バックアップのリテンション期間が短くなります。 [Azure SQL Database のバックアップ](sql-database-automated-backups.md)に関する記事をご覧ください。
- データベースに対する新しいプロパティは、変更が完了するまで適用されません。

## <a name="billing"></a>課金 

使用状況やデータベースがアクティブであったのが 1 時間未満であったことに関係なく、データベースが存在していた 1 時間単位で、その時間に使用された最上位のサービス階層とコンピューティング サイズで課金は行われます。 たとえば、Single Database を作成し、それを 5 分後に削除した場合、請求書にはデータベース時間として 1 時間の請求が表示されます。

## <a name="change-storage-size"></a>ストレージ サイズの変更

### <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

- ストレージは、データ ストレージの最大サイズの上限に達するまで、1 GB ずつ増分しプロビジョニングできます。 構成可能な最小データ ストレージは 1 GB です。 リソース制限のドキュメント ページにて、[単一データベース](sql-database-vcore-resource-limits-single-databases.md)および[エラスティック プール](sql-database-vcore-resource-limits-elastic-pools.md)の各サービス目標におけるデータ ストレージの最大サイズの制限をご確認ください。
- 単一データベースのストレージは、[Azure portal](https://portal.azure.com)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) を使って最大サイズを増減してプロビジョニングできます。 バイトで指定する最大サイズの値は、1 GB (1073741824 バイト) の倍数である必要があります。
- データベースのデータ ファイルに格納できるデータ量は、データ ストレージに構成されている最大サイズによって制限されます。 SQL Database では、この記憶域のほかに 30% のストレージを、トランザクション ログで使用するために自動的に割り当てます。
- SQL Database では、`tempdb` データベースの仮想コアごとに 32 GB が自動的に割り当てられます。 `tempdb` は、すべてのサービス レベルのローカル SSD ストレージにあります。
- 単一データベースまたはエラスティック プールのストレージ料金は、データ ストレージとトランザクション ログ ストレージの容量の合計にサービス レベルのストレージ ユニットを掛けて計算します。 `tempdb` のコストは価格に含まれています。 ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

- 単一データベースの DTU 価格には、追加コストなしで一定量のストレージが含まれます。 付属の容量を超える分のストレージについては、追加費用を払うことで、1 TB までは 250 GB 単位で、1 TB 以降は 256 GB 単位で、最大サイズ制限までプロビジョニングできます。 付属するストレージ容量と最大サイズ制限については、「[単一データベース: ストレージ サイズとコンピューティング サイズ](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)」をご覧ください。
- 単一データベースの追加ストレージは、Azure portal、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) を使ってサイズを最大に増やすことでプロビジョニングできます。
- 単一データベースの追加ストレージの料金は、追加ストレージ容量にサービス レベルの追加ストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="geo-replicated-database"></a>Geo レプリケートされたデータベース

レプリケートされたセカンダリ データベースのデータベース サイズを変更するには、プライマリ データベースのサイズを変更します。 この変更がセカンダリ データベースでもレプリケートされて実装されます。 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>最大サイズが 1 TB を超える場合の P11 と P15 の制約

現在、1 TB を超える Premium レベルのストレージは、中国東部、中国北部、ドイツ中部、ドイツ北東部、米国中西部、US DoD の各リージョンと、US Gov 中部を除くすべてのリージョンで利用できます。 これらのリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 最大サイズが 1 TB を超える P11 および P15 データベースには、次の考慮事項と制限事項が適用されます。

- P11 または P15 のデータベースの最大サイズが 1 TB を超える値に設定されていた場合、P11 または P15 のデータベースにしか復元またはコピーできません。  その後、再スケーリング操作の時に割り当てられた領域の量が新しいコンピューティング サイズの最大サイズ制限を超えなければ、データベースを異なるコンピューティング サイズに再スケーリングすることができます。
- アクティブ geo レプリケーションのシナリオの場合:
  - geo レプリケーションのリレーションシップの設定:プライマリ データベースが P11 または P15 の場合は、セカンダリも P11 または P15 である必要があります。つまり、下位のコンピューティング サイズは、1 TB を超えるサイズをサポートできないため、セカンダリとして拒否されます。
  - geo レプリケーションのリレーションシップでのプライマリ データベースのアップグレード:プライマリ データベースで最大サイズを 1 TB 超に変更すると、セカンダリ データベースでも同じ変更がトリガーされます。 プライマリに対する変更を有効にするには、両方のアップグレードが正常に完了する必要があります。 1 TB を超えるオプションに関するリージョンの制限が適用されます。 1 TB 超をサポートしていないリージョンにセカンダリが存在する場合、プライマリはアップグレードされません。
- 1 TB を超える P11/P15 データベースの読み込みに Import/Export サービスを使うことはサポートされていません。 SqlPackage.exe を使用して、データを[インポート](sql-database-import.md)および[エクスポート](sql-database-export.md)してください。

## <a name="next-steps"></a>次のステップ

全体的なリソースの制限については、[SQL Database の仮想コアペースのリソース制限 - 単一データベース](sql-database-vcore-resource-limits-single-databases.md)および [SQL Database の DTU ベースのリソース制限 - エラスティック プール](sql-database-dtu-resource-limits-single-databases.md)に関するページを参照してください。

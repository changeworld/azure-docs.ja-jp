---
title: Azure SQL Database でのリソース管理
description: この記事では、Azure SQL Database でのリソース管理の概要を示します。 また、これらのリソース制限に達した場合の動作に関する情報も提供します。
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 10/01/2021
ms.openlocfilehash: 67983b60457d652a1e4de71c6655f7eedae40542
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389762"
---
# <a name="resource-management-in-azure-sql-database"></a>Azure SQL Database でのリソース管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、Azure SQL Database でのリソース管理の概要を示します。 リソース制限が上限に達した場合の動作に関する情報を示すと共に、これらの制限の適用に利用されるリソース ガバナンスのメカニズムについて説明します。

単一データベースの価格レベルごとの特定のリソース制限 (サービス目標とも呼ばれます) については、[DTU ベースの単一データベース リソース制限](resource-limits-dtu-single-databases.md)または[仮想コアベースの単一データベース リソース制限](resource-limits-vcore-single-databases.md)に関する記事を参照してください。 エラスティック プールのリソース制限については、[DTU ベースのエラスティック プールのリソース制限](resource-limits-dtu-elastic-pools.md)または[仮想コアベースのエラスティック プールのリソース制限](resource-limits-vcore-elastic-pools.md)に関する記事を参照してください。

> [!TIP]
> Azure SQL Managed Instance の制限については、[マネージド インスタンスに関するリソース制限](../managed-instance/resource-limits.md)に関するページを参照してください。
>
> Azure Synapse Analytics 専用の SQL プール制限については、[容量の制限](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)、および[メモリとコンカレンシーの制限](../../synapse-analytics/sql-data-warehouse/memory-concurrency-limits.md)に関する記事を参照してください。

## <a name="logical-server-limits"></a>論理サーバーの制限

| リソース | 制限 |
| :--- | :--- |
| [論理サーバー](logical-servers.md)あたりのデータベース | 5000 |
| 1 つのリージョンにおけるサブスクリプションあたりの既定の論理サーバー数 | 20 |
| 1 つのリージョンにおけるサブスクリプションあたりの最大の論理サーバー数 | 250 |
| 論理サーバーあたりの DTU/eDTU クォータ | 54,000 |
| 論理サーバーあたりの仮想コア クォータ | 540 |
| 論理サーバーあたりの最大エラスティック プール | DTU または仮想コアの数によって制限されます。 たとえば、各プールが 1000 DTU の場合、1 つのサーバーで 54 プールをサポートできます。|
|||

> [!IMPORTANT]
> データベースの数が論理サーバーあたりの制限に近づくと、次の状況が発生する可能性があります。
>
> - マスター データベースに対して実行するクエリの待機時間が増えます。 これには、`sys.resource_stats` など、リソース使用率統計情報のビューも含まれます。
> - サーバー内のデータベースの列挙を要する、管理操作やポータル ビュー ポイント表示の待機時間が長くなります。

> [!NOTE]
> 既定数よりも多い DTU/eDTU クォータ、仮想コア クォータ、または論理サーバーを取得する場合は、Azure portal から新しいサポート リクエストをお送りください。 詳細については、「[Azure SQL Database のクォータの増加を要求する](quota-increase-request.md)」を参照してください。

## <a name="what-happens-when-resource-limits-are-reached"></a>リソースが制限に達したときの影響

### <a name="compute-cpu"></a>コンピューティングの CPU

データベース コンピューティングの CPU 使用率が高くなると、クエリの待機時間が長くなり、クエリがタイムアウトすることさえあります。このような状況下では、クエリはサービスによってキューに格納される場合があり、リソースが空くと実行用のリソースが提供されます。
高いコンピューティング使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたエラスティック プールのコンピューティング サイズを上げて、より多くのコンピューティング リソースをデータベースに提供します。 [シングルトンのリソースの拡大縮小に関する記事](single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](elastic-pool-scale.md)を参照してください。
- クエリを最適化して、各クエリの CPU リソース使用率を引き下げます。 詳しくは、「[クエリの調整とヒント](performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

### <a name="storage"></a>ストレージ

使用済みのデータベース領域が最大データ サイズの上限に達すると、データベースに対する挿入操作や、データ サイズを増やす更新操作が失敗し、クライアントが[エラー メッセージ](troubleshoot-common-errors-issues.md)を受け取ります。 SELECT および DELETE ステートメントは影響を受けません。

Premium および Business Critical サービス レベルでは、データ、トランザクション ログ、tempdb によるストレージ使用量の合計が最大ローカル ストレージ サイズを超えた場合にも、クライアントがエラー メッセージを受け取ります。 詳細については、「[ストレージ スペースのガバナンス](#storage-space-governance)」を参照してください。

高い容量使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたはエラスティック プールの最大データ サイズを増やすか、最大データ サイズの上限が高いサービス目標にスケールアップします。 [シングルトンのリソースの拡大縮小に関する記事](single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](elastic-pool-scale.md)を参照してください。
- データベースがエラスティック プール内にある場合は、もう 1 つの方法として、データベースをプールの外に移動し、ストレージ領域が他のデータベースと共有されないようにすることもできます。
- 未使用領域を再利用できるようにデータベースを縮小します。 エラスティック プールでは、データベースを圧縮することで、プール内の他のデータベースのストレージを増やすことができます。 詳細については、「[Manage file space in Azure SQL Database](file-space-manage.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。
- 高い領域使用率が、永続的なバージョン ストア (PVS) のサイズの急増によるものかどうかを確認します。 PVS は各データベースの一部であり、[高速データベース復旧](../accelerated-database-recovery.md)を実装するために使用されます。 現在の PVS サイズを確認するには、[PVS のトラブルシューティング](/sql/relational-databases/accelerated-database-recovery-management#troubleshooting)に関する記事を参照してください。 PVS サイズが大きい一般的な理由は、長時間 (数時間) にわたって開いているトランザクションにより、PVS での古いバージョンのクリーンアップが妨げられているためです。
- Premium および Business Critical サービス レベルの大規模なデータベースの場合、データベースの使用領域が最大データ サイズの上限を下回っている場合でも、領域不足エラーが発生することがあります。 これは、tempdb またはトランザクション ログが大量のストレージを消費し、ローカル ストレージの上限に達した場合に発生する可能性があります。 データベースまたはエラスティック プールを[フェールオーバー](high-availability-sla.md#testing-application-fault-resiliency)して、tempdb を初期の小さいサイズにリセットするか、トランザクション ログを[圧縮](file-space-manage.md#shrinking-transaction-log-file)してローカル ストレージの使用量を減らします。

### <a name="sessions-and-workers-requests"></a>セッションとワーカー (要求)

セッションおよびワーカーの最大数は、サービス レベルとコンピューティング サイズによって決まります。 セッションまたはワーカーが上限に達した場合、新しい要求は拒否され、クライアントはエラー メッセージを受け取ります。 利用可能な接続の数はアプリケーションで制御できますが、同時ワーカーの数は推定または制御が困難なことがよくあります。 クエリを長時間実行したため、ブロッキング チェーンの規模が大きいため、またはクエリの並行が過剰になっているために、データベース リソースが上限に達してワーカーが滞留している場合のピーク負荷期間には、これは特に当てはまります。

セッションまたはワーカーの使用率が高い場合は、次のような軽減オプションがあります。

- データベースまたはエラスティック プールのサービス レベルまたはコンピューティング サイズを高くします。 [シングルトンのリソースの拡大縮小に関する記事](single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](elastic-pool-scale.md)を参照してください。
- ワーカー使用率上昇の原因がコンピューティング リソースの競合である場合は、クエリを最適化して各クエリのリソース使用率を下げます。 詳しくは、「[クエリの調整とヒント](performance-guidance.md#query-tuning-and-hinting)」をご覧ください。
- [MAXDOP](configure-max-degree-of-parallelism.md) (並列処理の最大限度) の設定を減らします。
- クエリのワークロードを最適化して、発生回数とクエリ ブロックの時間を削減します。 詳細については、「[Azure SQL のブロックの問題を理解して解決する](understand-resolve-blocking.md)」を参照してください。

### <a name="memory"></a>メモリ

他のリソース (CPU、ワーカー、ストレージ) とは異なり、メモリの制限に達しても、クエリのパフォーマンスに悪影響を及ぼすことはなく、エラーや失敗も発生しません。 [メモリ管理アーキテクチャ ガイド](/sql/relational-databases/memory-management-architecture-guide)で詳しく説明されているように、データベース エンジンでは、設計上、使用可能なすべてのメモリが使用されることが多くあります。 メモリは主にデータをキャッシュするために使用され、速度の遅いストレージ アクセスが回避されます。 そのため、メモリ使用率が高いと、通常、クエリのパフォーマンスが向上します。これは、読み取りの遅いストレージではなく、読み取りの速いメモリが使用されるからです。

データベース エンジンが起動した後、ワークロードでストレージからデータの読み取りを開始すると、データベース エンジンにより、データが積極的にメモリにキャッシュされます。 この初期起動時間が経過すると、一般的に、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) の `avg_memory_usage_percent` および `avg_instance_memory_percent` 列が、100% に近いか一致することが予想されます (特に、アイドル状態ではなく、メモリに完全に収まらないデータベースの場合)。

データ キャッシュ以外にも、データベース エンジンの他のコンポーネントでメモリが使用されます。 メモリの需要があり、使用可能なすべてのメモリがデータ キャッシュによって使用されている場合、データベース エンジンでは、他のコンポーネントがメモリを使用できるようにデータ キャッシュ サイズが動的に削減され、他のコンポーネントでメモリが解放されると、データ キャッシュが動的に拡大されます。

まれに、十分に負荷の高いワークロードが原因でメモリ不足状態が発生し、メモリ不足エラーになることがあります。 これは、メモリ使用率が 0% から 100% までのどのレベルでも発生する可能性があります。 これは、コンピューティング サイズが小さく、メモリ制限が相対的に小さい場合や、クエリ処理により多くのメモリを使用するワークロード ([高密度エラスティック プール](elastic-pool-resource-management.md)など) で発生する可能性が高くなります。

メモリ不足エラーが発生した場合は、次のような軽減オプションがあります。
- データベースまたはエラスティック プールのサービス レベルまたはコンピューティング サイズを高くします。 [シングルトンのリソースの拡大縮小に関する記事](single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](elastic-pool-scale.md)を参照してください。
- クエリと構成を最適化して、メモリ使用率を引き下げます。 一般的な解決策について次の表で説明します。

|解決策|説明|
| :----- | :----- |
|メモリ許可のサイズを小さくする|メモリ許可の詳細については、[SQL Server メモリ許可の理解](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595)に関するブログ記事をご覧ください。 過度に大きなメモリ許可を回避するための一般的な解決策は、[統計](/sql/relational-databases/statistics/statistics)を最新の状態に保つことです。 これにより、クエリ エンジンによるメモリ消費量がより正確に推定されるため、不必要に大きいメモリ許可を回避できます。</br></br>互換性レベル 140 以降が使用されているデータベースでは、データベース エンジンにより、[バッチ モード メモリ許可フィードバック](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback)を使用したメモリ許可サイズの自動調整が行われる場合があります。 互換性レベル 150 以降が使用されているデータベースでは、より一般的な行モードのクエリに対して、データベースエンジンにより、[行モード メモリ許可フィードバック](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)が同じように使用されます。 この組み込み機能を使用すると、不必要に大きなメモリ許可が原因のメモリ不足エラーを回避できます。|
|クエリ プラン キャッシュのサイズを小さくする|クエリの実行ごとにクエリ プランがコンパイルされないように、データベース エンジンにより、クエリ プランがメモリ内にキャッシュされます。 1 回だけ使用されるキャッシュ プランによって発生するクエリ プラン キャッシュの肥大化を回避するには、OPTIMIZE_FOR_AD_HOC_WORKLOADS [データベース スコープ構成](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)を有効にします。|
|ロック メモリのサイズを小さくする|データベース エンジンにより、[ロック](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine)にメモリが使用されます。 可能であれば、大量のロックを取得し、ロック メモリの消費量が高くなる可能性のある大きなトランザクションは避けてください。|

## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>ユーザー ワークロードと内部プロセスによるリソース使用量

Azure SQL Database には、高可用性とディザスター リカバリー、データベースのバックアップと復元、監視、クエリ ストア、自動チューニングなどの中核的なサービス機能を実装するための、コンピューティング リソースが必要です。システムでは、[リソース ガバナンス](#resource-governance) メカニズムを使用して、これらの内部プロセス用にリソース全体の特定の限られた部分が確保されます。これにより、ユーザーのワークロードで残りのリソースを使用できるようになります。 内部プロセスでコンピューティング リソースが使用されていない場合は、システムによってユーザーのワークロードで使用できるようになります。

ユーザー ワークロードと内部プロセスによる CPU とメモリの使用量の合計は、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) および [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ビューの `avg_instance_cpu_percent` 列と `avg_instance_memory_percent` 列で報告されます。 このデータは、[単一データベース](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases)およびプール レベルの[エラスティック プール](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)について、`sqlserver_process_core_percent` および `sqlserver_process_memory_percent` Azure Monitor メトリックによって報告されます。

各データベースのユーザー ワークロードによる CPU とメモリの使用量は、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) および [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ビューの `avg_cpu_percent` 列と `avg_memory_usage_percent` 列で報告されます。 エラスティック プールの場合、プール レベルのリソース消費は、[sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで報告されます。 ユーザー ワークロードの CPU 消費量は、[単一データベース](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases)およびプール レベルの[エラスティック プール](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)について、`cpu_percent` Azure Monitor メトリックによって報告されます。

ユーザー ワークロードと内部プロセスによる最近のリソース消費の詳細な内訳は、[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) および [sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) ビューで報告されます。 これらのビューで参照されているリソース プールとワークロード グループの詳細については、「[リソース管理](#resource-governance)」を参照してください。 これらのビューでは、関連付けられているリソース プールおよびワークロード グループにおける、ユーザー ワークロードと特定の内部プロセスによるリソース使用量が報告されます。

パフォーマンスの監視とトラブルシューティングのコンテキストでは、**ユーザーの CPU 消費量** (`avg_cpu_percent`、`cpu_percent`) と、ユーザー ワークロードと内部プロセスによる **合計 CPU 消費量** (`avg_instance_cpu_percent`、`sqlserver_process_core_percent`) の両方を考慮することが重要です。

**ユーザー CPU 消費量** は、各サービス目標におけるユーザー ワークロードの上限に対する割合として計算されます。 **ユーザー CPU 使用率** が 100% の場合は、ユーザー ワークロードがサービス目標の上限に達したことを示します。 一方、**合計 CPU 消費量** が 70 から 100% の範囲に達した場合は、報告される **ユーザー CPU 消費量** が 100% を大幅に下回ったままであっても、ユーザー ワークロードのスループットのフラット化とクエリの待機時間増が見られる可能性があります。 これは、コンピューティング リソースが適度に割り当てられている、より小さいサービス目標を使用していても、[高密度エラスティック プール](elastic-pool-resource-management.md)のような比較的集中的なユーザー ワークロードの場合に、発生する可能性が高くなります。 また、データベースの新しいレプリカを作成したり、データベースをバックアップしたりする場合など、内部プロセスで一時的に追加のリソースが必要な場合は、小さいサービス目標でも発生する可能性があります。

**合計 CPU 消費量** が高い場合の軽減オプションは、「[コンピューティングの CPU](#compute-cpu)」セクションで説明したものと同じであり、サービス目標の引き上げや、ユーザー ワークロードの最適化が含まれます。

## <a name="resource-governance"></a>リソース ガバナンス

Azure SQL Database では、リソース制限を適用するためにクラウドで実行できるよう修正や拡張が行われた SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) に基づいたリソース ガバナンスの実装が使用されます。 SQL Database には複数の[リソース プール](/sql/relational-databases/resource-governor/resource-governor-resource-pool)および[ワークロード グループ](/sql/relational-databases/resource-governor/resource-governor-workload-group)があり、プール レベルおよびグループ レベル両方でリソース制限が設定され、[均衡の取れた Database-as-a-Service](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/) が提供されています。 ユーザー ワークロードと内部ワークロードは、別々のリソース プールとワークロード グループに分類されます。 プライマリ レプリカおよび読み取り可能なセカンダリ レプリカ (geo レプリカを含む) のユーザー ワークロードは、`SloSharedPool1` リソース プールと `UserPrimaryGroup.DBId[N]` ワークロード グループに分類されます。`[N]` はデータベース ID の値を表します。 さらに、さまざまな内部ワークロード用に複数のリソース プールとワークロード グループがあります。

Resource Governor を使用してデータベース エンジン内でリソースを管理するだけでなく、Azure SQL Database ではプロセス レベルのリソース管理用に Windows [ジョブ オブジェクト](/windows/win32/procthread/job-objects)、また、ストレージ クォータ管理用に Windows [ファイル サーバー リソース マネージャー (FSRM)](/windows-server/storage/fsrm/fsrm-overview) も使用します。

Azure SQL Database のリソース管理は、本質的に階層化されています。 制限は一貫して、オペレーティング システムのリソース管理メカニズムと Resource Governor を使用して OS レベルとストレージ ボリューム レベルで適用され、Resource Governor を使用してリソース プール レベルで、さらには Resource Governor を使用してワークロード グループ レベルで適用されます。 現在のデータベースまたはエラスティック プールに対して有効になっているリソース ガバナンスの制限は、[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) ビューで報告されます。

### <a name="data-io-governance"></a>データの IO 管理

データ IO の管理は、データベースのデータ ファイルに対する読み取りおよび書き込み両方の物理 IO を制限するために使用される、Azure SQL Database 内のプロセスです。 IOPS 制限はサービス レベルごとに設定され、"うるさい隣人" の影響を最小限に抑えて、マルチテナント サービス内のリソース割り当ての公平性を提供すると共に、基になるハードウェアとストレージの機能内に収めます。

単一データベースの場合、ワークロード グループの制限は、データベースに対するすべてのストレージ IO に適用されます。 エラスティック プールの場合、ワークロード グループの制限は、プール内の各データベースに適用されます。 また、リソース プールの制限は、エラスティック プールの累積 IO にも適用されます。 tempdb IO には、ワークロード グループの制限が適用されますが、より高い tempdb IO 制限が適用される Basic、Standard、General Purpose のサービス レベルを除きます。 ワークロード グループの制限はリソース プールの制限よりも低く、IOPS/スループットをより迅速に制限することから、通常、(単一またはプールされた) データベースに対するワークロードがリソース プールの制限に到達することはありません。 しかし、同じプール内の複数のデータベースに対して組み合わされたワークロードによって、プールの制限に到達する場合があります。

たとえば、あるクエリで IO リソース管理なしで 1000 IOPS が生成されるが、ワークロード グループの IOPS 上限が 900 IOPS に設定されている場合、このクエリで、900 を超える IOPS は生成できません。 しかし、リソース プールの IOPS 上限が 1500 IOPS に設定されており、リソース プールに関連付けられているすべてのワークロード グループからの IO 合計が 1500 IOPS を超過した場合、同じクエリの IO がワークロード グループの制限である 900 IOPS を下回るまで減らされる可能性があります。

[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) ビューから返される IOPS およびスループットの最大値は、保証としてではなく、制限/上限として機能します。 さらに、リソース管理によって、特定のストレージ待機時間が保証されるわけではありません。 特定のユーザーのワークロードに対して実現できる最適な待機時間、IOPS、スループットは、IO リソース管理の上限だけではなく、使用される最大 IO サイズや基になるストレージの機能にも依存します。 SQL Database で使用される IO のサイズは、512 KB から 4 MB の間で変動します。 IOPS 制限の適用という目的のために、Azure Storage 内でデータ ファイルを備えるデータベースに例外が発生した場合は、サイズに関係なくどの IO も考慮されます。 その場合、Azure Storage IO の説明に従って、256 KB より大規模な IO は、複数の 256 KB の IO として考慮されます。

Azure Storage 内のデータ ファイルを使用する Basic、Standard、General Purpose の各データベースでは、IOPS のこの数値を累積的に提供できる十分なデータ ファイルがデータベースにない場合、データがファイル間で均等に分散されていない場合、または基本となる BLOB のパフォーマンス レベルによって IOPS/スループットがリソース管理の制限より下に制限される場合、`primary_group_max_io` 値に到達することはありません。 同様に、頻繁なトランザクション コミットによって生成された小規模なログ IO では、基になる Azure Storage BLOB 上に IOPS 制限があるため、ワークロードによって `primary_max_log_rate` 値に到達することはありません。 Azure Premium Storage を使用するデータベースの場合、Azure SQL Database では、データベースのサイズに関係なく、必要な IOPS/スループットを取得するのに十分な大容量のストレージ BLOB が使用されます。 大規模なデータベースの場合、合計 IOPS/スループット容量を増やすために複数のデータファイルが作成されます。

[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)、[sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビュー上で報告される `avg_data_io_percent` および `avg_log_write_percent` などのリソース使用率の値は、リソース管理の上限の割合として計算されています。 そのため、リソース管理以外の要素によって IOPS/スループットが制限される場合は、報告されるリソース使用率が 100% を下回ったままであっても、IOPS/スループットのフラット化とワークロードの増加に伴う待機時間の増加が見られる可能性があります。

データベース ファイルごとの読み取りおよび書き込みの IOPS、スループット、および待機時間を確認するには、[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 関数を使用します。 この関数では、`avg_data_io_percent` に対しては考慮されないバックグラウンド IO を含む、データベースに対するすべての IO が網羅されますが、基になるストレージの IOPS とスループットが使用され、監視されているストレージの待機時間に影響を及ぼす可能性があります。 この関数では、IO リソース ガバナンスによって発生する可能性のある読み取りと書き込みの追加の待機時間も、それぞれ `io_stall_queued_read_ms` 列と `io_stall_queued_write_ms` 列で報告されます。

### <a name="transaction-log-rate-governance"></a>トランザクション ログ速度ガバナンス

トランザクション ログ速度ガバナンスは、一括挿入、SELECT INTO、インデックス作成などのワークロードの高いインジェクション速度を制限するために使用される、Azure SQL Database 内のプロセスです。 こうした制限は追跡され、1 秒未満のレベルでログ レコード生成速度に適用されて、データ ファイルに対して発行できる IO の数に関係なく、スループットが制限されます。 現在、トランザクション ログ生成率は、ハードウェアに依存し、サービス レベルに依存するポイントまで直線的にスケールアップされます。

ログ速度をさまざまなシナリオで実現し維持できるとともに、ユーザー負荷への影響を最小限に抑えながらシステム全体の機能を維持できように、ログ速度が設定されます。 ログ速度ガバナンスにより、トランザクション ログ バックアップは、発行された復元可能性 SLA 内で維持されます。 また、このガバナンスにより、セカンダリ レプリカ上の過剰なバックログが回避されます。そうしなければ、フェールオーバー中のダウンタイムが予想以上に長くなることがあります。

トランザクション ログ ファイルへの実際の物理的な IO は、管理または制限されません。 ログ レコードが生成されると、各操作が評価され、望ましい最大ログ速度 (MB/秒) を維持するために遅延する必要があるかどうかが判断されます。 ログ レコードがストレージにフラッシュされる場合には、遅延は追加されません。ログ速度ガバナンスは、ログ速度生成プロセス自体に適用されます。

実行時に適用される実際のログ生成速度は、フィードバック メカニズムによっても影響される可能性があります。この場合、システムを安定化するために許容ログ速度が一時的に低下します。 ログ領域の不足状態を回避しようとするログ ファイル領域管理と、データ レプリケーション メカニズムにより、全体的なシステムの制限が一時的に低くなる可能性があります。

ログ速度ガバナーのトラフィック シェイプは、次の待機の種類を使用して表示されます ([sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) および [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) ビューで公開されます)。

| 待機の種類 | Notes |
| :--- | :--- |
| LOG_RATE_GOVERNOR | データベース制限 |
| POOL_LOG_RATE_GOVERNOR | プール制限 |
| INSTANCE_LOG_RATE_GOVERNOR | インスタンス制限 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | フィードバック制御、Premium/Business Critical での可用性グループの物理的なレプリケーションが維持されていない |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | フィードバック制御、ログ領域の不足を回避するために速度を制限 |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | geo レプリケーションのフィードバック制御、geo セカンダリの長いデータ待機時間や使用不可状態を回避するためにログ速度を制限|
|||

望ましいスケーラビリティを損なうログ速度制限が発生した場合は、次のオプションを検討してください。

- サービス レベル最大のログ速度を実現したり、異なるサービス レベルに切り替えたりするには、より高いサービス レベルにスケールアップします。 [Hyperscale](service-tier-hyperscale.md) サービス レベルでは、選択したサービス レベルに関係なく、100 MB/秒のログ速度が提供されます。
- ETL プロセスでのステージング データなど、読み込まれるデータが一時的なデータである場合、tempdb に読み込むことができます (ログ記録が最小限に抑えられます)。
- 分析シナリオの場合は、クラスター化された[列ストア](/sql/relational-databases/indexes/columnstore-indexes-overview) テーブル、または[データ圧縮](/sql/relational-databases/data-compression/data-compression)を使用するインデックスを含むテーブルに読み込む必要があります。 この場合、必要なログ速度が小さくなります。 この手法は CPU 使用率を高め、クラスター化列ストア インデックスまたはデータ圧縮からベネフィットを得られるデータ セットにのみ適用されます。

### <a name="storage-space-governance"></a>ストレージ スペースのガバナンス

Premium および Business Critical サービス レベルでは、"*データ ファイル*"、"*トランザクション ログ ファイル*"、"*tempdb ファイル*" などの顧客データが、データベースまたはエラスティック プールをホストするマシンのローカル SSD ストレージに保存されます。 ローカル SSD ストレージにより、高い IOPS とスループットのほか、短い IO 待ち時間を実現しています。 顧客データに加えて、オペレーティング システム、管理ソフトウェア、監視データおよびログ、システム操作に必要な他のファイルにもローカル ストレージが使用されます。

ローカル ストレージのサイズは有限であり、ハードウェアの性能 (これにより **最大ローカル ストレージ** の制限が決まります) または顧客データ用に確保されるローカル ストレージの量に左右されます。 この制限は、安全で信頼性の高いシステム操作を確保しつつ、顧客データのストレージを最大化するために設定されています。 各サービス目標の **最大ローカル ストレージ** の値を確認するには、[単一データベース](resource-limits-vcore-single-databases.md)および [エラスティック プール](resource-limits-vcore-elastic-pools.md)のリソース制限に関するドキュメントを参照してください。

この値と、特定のデータベースまたはエラスティック プールで現在使用されているローカル ストレージの容量は、次のクエリを使用して確認することもできます。

```sql
SELECT server_name, database_name, slo_name, user_data_directory_space_quota_mb, user_data_directory_space_usage_mb
FROM sys.dm_user_db_resource_governance
WHERE database_id = DB_ID();
```

|列|説明|
| :----- | :----- |
|`server_name`|論理サーバー名|
|`database_name`|データベース名|
|`slo_name`|サービス目標名 (ハードウェアの世代を含む)|
|`user_data_directory_space_quota_mb`|**最大ローカル ストレージ** (MB)|
|`user_data_directory_space_usage_mb`|データ ファイル、トランザクション ログ ファイル、および tempdb ファイルによるローカル ストレージの現在の使用量 (MB)。 5 分ごとに更新されます。|
|||

このクエリは、マスター データベースではなく、ユーザー データベースで実行する必要があります。 エラスティック プールの場合、プール内の任意のデータベースでクエリを実行できます。 報告された値はプール全体に適用されます。

> [!IMPORTANT]
> Premium および Business Critical サービス レベルでは、ワークロードが、データ ファイル、トランザクション ログ ファイル、tempdb ファイルによるローカル ストレージの合計使用量を **最大ローカル ストレージ** の制限を超えて増やそうとすると、領域不足エラーが発生します。

データベースの作成、削除、サイズの増減に伴って、マシン上のローカル ストレージの使用量は経時的に変動します。 システムは、マシン上の使用可能なローカル ストレージが少なくなっており、データベースまたはエラスティック プールが領域不足になる危険性があることを検知すると、使用可能なローカル ストレージが十分にある別のマシンにそのデータベースまたはエラスティック プールを移動します。

この移動処理は、データベースのスケーリング操作と同様にオンラインで実行され、操作終了時の短時間 (秒単位) のフェールオーバーなど、 これと同様の[影響](single-database-scale.md#impact)を与えます。 このフェールオーバーにより、開いている接続が終了され、トランザクションがロールバックされるので、その時点でデータベースを使用しているアプリケーションに影響を与える可能性があります。

すべてのデータが別のマシンのローカル ストレージ ボリュームにコピーされるため、大規模なデータベースの移動にはかなりの時間を要する場合があります。 その間に、データベースやエラスティック プール、または tempdb データベースによるローカル領域の使用量が急激に増加すると、領域が不足する危険性が高まります。 システムでは、領域不足エラーを最小限に抑え、不要なフェールオーバーを回避するために、バランスの取れた方法でデータベースの移動が開始されます。

> [!NOTE]
> ローカル ストレージの不足によるデータベースの移動は、Premium または Business Critical サービス レベルでのみ発生します。 Hyperscale、General Purpose、Standard、Basic の各サービス レベルでは、データ ファイルがローカル ストレージに保存されないため、これらのレベルでは発生しません。

## <a name="next-steps"></a>次のステップ

- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。
- DTU と eDTU については、「[データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) の説明](purchasing-models.md#dtu-based-purchasing-model)」をご覧ください。
- tempdb のサイズ制限については、「[SQL Database の Tempdb データベース](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)」をご覧ください。

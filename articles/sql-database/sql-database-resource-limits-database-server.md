---
title: Azure SQL Database のリソース制限 | Microsoft Docs
description: この記事では、単一データベースとエラスティック プール用の Azure SQL Database リソース制限についての概要を示します。 また、これらのリソース制限に達した場合、または制限を超えた場合の動作に関する情報も提供します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: afb30a17d7a1450f169402c18f41ce249415e89d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804828"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL Database のリソース制限およびリソース管理

この記事では、単一データベースとエラスティック プールを管理する SQL Database サーバーの SQL Database リソース制限について概要を示します。 リソース制限が上限に達するか、または超過した場合の動作に関する情報を示すと共に、これらの制限の適用に利用されるリソース管理のメカニズムについて説明します。

> [!NOTE]
> Managed Instance の制限については、[マネージド インスタンスに関する SQL Database のリソース制限](sql-database-managed-instance-resource-limits.md)に関するページを参照してください。

## <a name="maximum-resource-limits"></a>最大リソース制限

| リソース | 制限 |
| :--- | :--- |
| サーバーあたりのデータベース数 | 5000 |
| 任意のリージョンにおけるサブスクリプションあたりの既定のサーバー数 | 20 |
| 任意のリージョンにおけるサブスクリプションあたりの最大サーバー数 | 200 |  
| サーバーあたりの DTU/eDTU クォータ | 54,000 |  
| サーバー/インスタンスあたりの仮想コア クォータ | 540 |
| サーバーあたりの最大プール | DTU または仮想コアの数によって制限されます。 たとえば、各プールが 1000 DTU の場合、1 つのサーバーで 54 プールをサポートできます。|
|||

> [!IMPORTANT]
> データベースの数が SQL Database サーバーあたりの制限に近づくと、次の状況が発生します。
>
> - マスター データベースに対して実行するクエリの待機時間が増えます。  これには、リソース使用率統計情報のビューも含まれます (sys.resource_stats など)。
> - サーバー内のデータベースの列挙を要する、管理操作やポータル ビュー ポイント表示の待機時間が長くなります。

> [!NOTE]
> 既定量よりも多い DTU/eDTU クォータ、仮想コア クォータ、またはサーバーを取得する場合は、Azure portal から新しいサポート リクエストを送信できます。 詳細については、「[Azure SQL Database のクォータの増加を要求する](quota-increase-request.md)」を参照してください。

### <a name="storage-size"></a>ストレージ サイズ

単一データベースのリソースのストレージ サイズについては、[DTU ベースのリソース制限](sql-database-dtu-resource-limits-single-databases.md)に関するページ、または価格レベルごとのストレージ サイズ制限についての[仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md)に関するページのいずれかを参照してください。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>データベース リソースが制限に達したときの影響

### <a name="compute-dtus-and-edtus--vcores"></a>コンピューティング (DTU と eDTU/vCores)

(DTU および eDTU または仮想コアによって測定された) データベース コンピューティングの使用率が高くなると、クエリの待機時間が増加し、そのクエリがタイムアウトすることもあります。このような状況下では、クエリはサービスによってキューに格納される場合があり、リソースが空くと実行用のリソースが提供されます。
高いコンピューティング使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたエラスティック プールのコンピューティング サイズを上げて、より多くのコンピューティング リソースをデータベースに提供します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- クエリを最適化して、各クエリのリソース使用率を引き下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

### <a name="storage"></a>ストレージ

使用済みのデータベース容量が最大サイズの上限に達すると、データのサイズが増えるデータベースの挿入および更新は失敗し、クライアントは[エラー メッセージ](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)を受け取ります。 SELECT および DELETE ステートメントは引き続き正常に実行されます。

高い容量使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたはエラスティック プールの最大サイズを増やすか、より多くの記憶域を追加します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- データベースがエラスティック プール内にある場合は、もう 1 つの方法として、データベースをプールの外に移動し、ストレージ領域が他のデータベースと共有されないようにすることもできます。
- 未使用領域を再利用できるようにデータベースを縮小します。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="sessions-and-workers-requests"></a>セッションとワーカー (要求)

セッションおよびワーカーの最大数は、サービス レベルとコンピューティング サイズ (DTU/eDTU または仮想コア) によって決まります。 セッションまたはワーカーが上限に達した場合、新しい要求は拒否され、クライアントはエラー メッセージを受け取ります。 利用可能な接続の数はアプリケーションで制御できますが、同時ワーカーの数は推定または制御が困難なことがよくあります。 クエリを長時間実行したため、ブロッキング チェーンの規模が大きいため、またはクエリの並行が過剰になっているために、データベース リソースが上限に達してワーカーが滞留している場合のピーク負荷期間には、これは特に当てはまります。

セッションまたはワーカーの使用率が高い場合は、次のような軽減オプションがあります。

- データベースまたはエラスティック プールのサービス レベルまたはコンピューティング サイズを高くします。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- ワーカー使用率上昇の原因がコンピューティング リソースの競合である場合は、クエリを最適化して各クエリのリソース使用率を下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。
- [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (並列処理の最大限度) の設定を減らします。
- クエリのワークロードを最適化して、発生回数とクエリ ブロックの時間を削減します。

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>ユーザー ワークロードと内部プロセスによるリソース使用量

各データベースのユーザー ワークロードによる CPU とメモリの使用量は、[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) および [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) ビューの `avg_cpu_percent` 列と `avg_memory_usage_percent` 列で報告されます。 エラスティック プールの場合、プール レベルのリソース消費は、[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで報告されます。 ユーザー ワークロードの CPU 消費量は、[単一データベース](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)およびプール レベルの[エラスティック プール](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)について、`cpu_percent` Azure Monitor メトリックによって報告されます。

Azure SQL Database には、高可用性とディザスター リカバリー、データベースのバックアップと復元、監視、クエリ ストア、自動チューニングなどの中核的なサービス機能を実装するための、コンピューティング リソースが必要です。システムでは、[リソース ガバナンス](#resource-governance) メカニズムを使用して、これらの内部プロセス用にリソース全体の特定の限られた部分が確保されます。これにより、ユーザーのワークロードで残りのリソースを使用できるようになります。 内部プロセスでコンピューティング リソースが使用されていない場合は、システムによってユーザーのワークロードで使用できるようになります。

単一データベースまたはエラスティック プールをホストしている SQL Server インスタンスのユーザー ワークロードと内部プロセスによる CPU およびメモリの合計使用量は、[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) および [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) ビューの `avg_instance_cpu_percent` 列と `avg_instance_memory_percent` 列で報告されます。 このデータは、[単一データベース](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)およびプール レベルの[エラスティック プール](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)について、`sqlserver_process_core_percent` および `sqlserver_process_memory_percent` Azure Monitor メトリックによって報告されます。

ユーザー ワークロードと内部プロセスによる最近のリソース消費の詳細な内訳は、[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) および [sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) ビューで報告されます。 これらのビューで参照されているリソース プールとワークロード グループの詳細については、「[リソース管理](#resource-governance)」を参照してください。 これらのビューでは、関連付けられているリソース プールおよびワークロード グループにおける、ユーザー ワークロードと特定の内部プロセスによるリソース使用量が報告されます。

パフォーマンスの監視とトラブルシューティングのコンテキストでは、**ユーザーの CPU 消費量** (`avg_cpu_percent`、`cpu_percent`) と、ユーザー ワークロードと内部プロセスによる**合計 CPU 消費量** (`avg_instance_cpu_percent`、`sqlserver_process_core_percent`) の両方を考慮することが重要です。

**ユーザー CPU 消費量**は、各サービス目標におけるユーザー ワークロードの上限に対する割合として計算されます。 **ユーザー CPU 使用率**が 100% の場合は、ユーザー ワークロードがサービス目標の上限に達したことを示します。 一方、**合計 CPU 消費量**が 70 - 100% の範囲に達した場合は、報告された**ユーザー CPU 消費量**が 100% を大幅に下回っている場合でも、ユーザー ワークロードのスループットがフラット化され、クエリの待機時間が増加している可能性があります。 これは、コンピューティング リソースが適度に割り当てられている、より小さいサービス目標を使用していても、[高密度エラスティック プール](sql-database-elastic-pool-resource-management.md)のような比較的集中的なユーザー ワークロードの場合に、発生する可能性が高くなります。 また、データベースの新しいレプリカを作成する場合など、内部プロセスで一時的に追加のリソースが必要な場合は、小さいサービス目標でも発生する可能性があります。

**合計 CPU 消費量**が高い場合の軽減オプションは、前に説明したものと同じであり、サービス目標を高くしたり、ユーザー ワークロードを最適化することが含まれます。

## <a name="resource-governance"></a>リソース管理

Azure SQL Database では、リソース制限を適用するために、SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) に基づいたリソース管理の実装を利用します。Azure 上で SQL Server データベース サービスを実行するために、修正や拡張が行われます。 サービス内の各 SQL Server インスタンス上には、複数の[リソース プール](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool)および[ワークロード グループ](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)があり、プール レベルおよびグループ レベル両方でリソース制限が設定され、[均衡の取れた Database-as-a-Service](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/) が提供されています。 ユーザー ワークロードと内部ワークロードは、別々のリソース プールとワークロード グループに分類されます。 プライマリ レプリカおよび読み取り可能なセカンダリ レプリカ (geo レプリカを含む) のユーザー ワークロードは、`SloSharedPool1` リソース プールと `UserPrimaryGroup.DBId[N]` ワークロード グループに分類されます。`N` はデータベース ID の値を表します。 さらに、さまざまな内部ワークロード用に複数のリソース プールとワークロード グループがあります。

Resource Governor を使用して SQL Server プロセス内でリソースを管理するだけでなく、Azure SQL Database ではプロセス レベルのリソース管理用に Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects)、また、ストレージ クォータ管理用に Windows [ファイル サーバー リソース マネージャー (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) も使用します。

Azure SQL Database のリソース管理は、本質的に階層化されています。 制限は一貫して、オペレーティング システムのリソース管理メカニズムと Resource Governor を使用して OS レベルとストレージ ボリューム レベルで適用され、Resource Governor を使用してリソース プール レベルで、さらには Resource Governor を使用してワークロード グループ レベルで適用されます。 現在のデータベースまたはエラスティック プールに対して有効になっているリソース管理の制限は、[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) ビューに示されます。 

### <a name="data-io-governance"></a>データの IO 管理

データ IO の管理は、データベースのデータ ファイルに対する読み取りおよび書き込み両方の物理 IO を制限するために使用される、Azure SQL Database 内のプロセスです。 IOPS 制限はサービス レベルごとに設定され、"うるさい隣人" の影響を最小限に抑えて、マルチテナント サービス内のリソース割り当ての公平性を提供すると共に、基になるハードウェアとストレージの機能内に収めます。

単一データベースの場合、ワークロード グループの制限は、データベースに対するすべてのストレージ IO に適用されます。一方、リソース プールの制限は、`tempdb` データベースを含め、同じ SQL Server インスタンス上のすべてのデータベースに対するすべてのストレージ IO に適用されます。 エラスティック プールの場合、ワークロード グループの制限はプール内の各データベースに適用されます。一方、リソース プールの制限は、`tempdb` データベースを含め、エラスティック プール全体に対して適用され、プール内のすべてのデータベース間で共有されます。 ワークロード グループの制限はリソース プールの制限よりも低く、IOPS/スループットをより迅速に制限することから、通常、(単一またはプールされた) データベースに対するワークロードがリソース プールの制限に到達することはありません。 しかし、同じ SQL Server インスタンス上の複数のデータベースに対して組み合わされたワークロードによって、プールの制限に到達する場合があります。

たとえば、IO リソース管理を伴わずにクエリでは 1000 IOPS を生成するが、ワークロード グループの IOPS 上限は 900 IOPS に設定されている場合、クエリでは 900 を超える IOPS は生成できません。 しかし、リソース プールの IOPS 上限が 1500 IOPS に設定されており、リソース プールに関連付けられているすべてのワークロード グループからの IO 合計が 1500 IOPS を超過した場合、同じクエリの IO がワークロード グループの制限である 900 IOPS を下回るまで減らされる可能性があります。

[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) ビューから返される IOPS およびスループットの最小値および最大値は、保証としてではなく、制限/上限として機能します。 さらに、リソース管理によって、特定のストレージ待機時間が保証されるわけではありません。 特定のユーザーのワークロードに対して実現できる最適な待機時間、IOPS、スループットは、IO リソース管理の上限だけではなく、使用される最大 IO サイズや基になるストレージの機能にも依存します。 SQL Server で使用される IO のサイズは、512 KB から 4 MB の間で変動します。 IOPS 制限の適用という目的のために、Azure Storage 内でデータ ファイルを備えるデータベースに例外が発生した場合は、サイズに関係なくどの IO も考慮されます。 その場合、Azure Storage IO の説明に従って、256 KB より大規模な IO は、複数の 256 KB の IO として考慮されます。

Azure Storage 内のデータ ファイルを使用する Basic、Standard、General Purpose データベースでは、IOPS のこの数値を累積的に提供できる十分なデータ ファイルがデータベースにない場合、データがファイルにわたって均等に分散されていない場合、または、基本となる BLOB のパフォーマンス レベルによってリソース管理の制限より下に IOPS/スループットが制限されている場合、`primary_group_max_io` 値には到達することはありません。 同様に、頻繁なトランザクション コミットによって生成された小規模なログ IO では、基になる Azure ストレージ BLOB 上の IOPS 制限があるため、ワークロードによって `primary_max_log_rate` 値に到達することはありません。

[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)、[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビュー上で報告される `avg_data_io_percent` および `avg_log_write_percent` などのリソース使用率の値は、リソース管理の上限の割合として計算されています。 そのため、リソース管理以外の要素によって IOPS/スループットが制限される場合は、報告されたリソース使用率が 100% を下回ったままであっても、IOPS/スループットがフラット化され、ワークロードの増加に従って待機時間が増大することが確認できます。 

データベース ファイルごとの読み取りおよび書き込みの IOPS、スループット、および待機時間を確認するには、[sys.dm_io_virtual_file_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 関数を使用します。 この関数では、`avg_data_io_percent` に対しては考慮されないバックグラウンド IO を含む、データベースに対するすべての IO が網羅されますが、基になるストレージの IOPS とスループットが使用され、監視されているストレージの待機時間に影響を及ぼす可能性があります。 また、この関数では、IO リソース管理によって発生する可能性のある読み取りと書き込みの追加の待機時間も、それぞれ `io_stall_queued_read_ms` 列と `io_stall_queued_write_ms` 列に表示されます。

### <a name="transaction-log-rate-governance"></a>トランザクション ログ速度ガバナンス

トランザクション ログ速度ガバナンスは、一括挿入、SELECT INTO、インデックス作成などのワークロードの高いインジェクション速度を制限するために使用される、Azure SQL Database 内のプロセスです。 こうした制限は追跡され、1 秒未満のレベルでログ レコード生成速度に適用されて、データ ファイルに対して発行できる IO の数に関係なく、スループットが制限されます。  現在、トランザクション ログ生成速度は、ハードウェアに依存するポイントまで直線的にスケールアップされます。許容される最大ログ速度は、仮想コア購入モデルで 96 MB/秒です。 

> [!NOTE]
> トランザクション ログ ファイルへの実際の物理的な IO は、管理または制限されません。

ログ速度をさまざまなシナリオで実現し維持できるとともに、ユーザー負荷への影響を最小限に抑えながらシステム全体の機能を維持できように、ログ速度が設定されます。 ログ速度ガバナンスにより、トランザクション ログ バックアップは、発行された復元可能性 SLA 内で維持されます。  また、このガバナンスにより、セカンダリ レプリカ上の過剰なバックログが回避されます。

ログ レコードが生成されると、各操作が評価され、望ましい最大ログ速度 (MB/秒) を維持するために遅延する必要があるかどうかが判断されます。 ログ レコードがストレージにフラッシュされる際には、遅延は追加されません。ログ速度ガバナンスは、ログ速度生成プロセス自体に適用されます。

実行時に適用される実際のログ生成速度は、フィードバック メカニズムによっても影響される可能性があります。この場合、システムを安定化するために許容ログ速度が一時的に低下します。 ログ領域の不足状態を回避しようとするログ ファイル領域管理と、可用性グループのレプリケーション メカニズムにより、全体的なシステムの制限が一時的に低くなる可能性があります。

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
- 最大 96 MB/秒のログ速度を実現するためにより高いサービス レベルにスケールアップするか、異なるサービス レベルに切り替えます。 [Hyperscale](sql-database-service-tier-hyperscale.md) サービス レベルでは、選択したサービス レベルに関係なく、100 MB/秒のログ速度が提供されます。
- ETL プロセスでのステージング データなど、読み込まれるデータが一時的なデータである場合、tempdb に読み込むことができます (ログ記録が最小限に抑えられます)。 
- 分析シナリオでは、クラスター化列ストアの対象テーブルに読み込みます。 この場合は圧縮されるため、必要なログ速度が小さくなります。 この手法では CPU 使用率が増加し、クラスター化列ストア インデックスからメリットを得られるデータ セットにのみ適用できます。 

## <a name="next-steps"></a>次のステップ

- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。
- DTU と eDTU については、「[データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) の説明](sql-database-purchase-models.md#dtu-based-purchasing-model)」をご覧ください。
- tempdb のサイズ制限については、「[SQL Database の Tempdb データベース](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)」をご覧ください。

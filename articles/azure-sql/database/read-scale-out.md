---
title: レプリカでの読み取りクエリ
description: Azure SQL では、読み取りスケールアウトと呼ばれる読み取り専用レプリカの処理能力を読み取りワークロードに使用できます。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 09/23/2021
ms.openlocfilehash: f5acdf621c04ba48664004bbb1f28ae1c0914fcb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447039"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>読み取り専用レプリカを使用して読み取り専用クエリ ワークロードをオフロードする
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[高可用性アーキテクチャ](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)の一部として、Premium および Business Critical サービス レベルの各単一データベース、エラスティック プール データベース、およびマネージド インスタンスは、1 つのプライマリ読み取り/書き込みレプリカと複数のセカンダリ読み取り専用レプリカを使用して自動的にプロビジョニングされます。 セカンダリ レプリカは、プライマリ レプリカと同じコンピューティング サイズでプロビジョニングされます。 "*読み取りスケールアウト*" 機能では、読み取り/書き込みレプリカ上で実行する代わりに、読み取り専用レプリカのいずれか 1 つのコンピューティング能力を使用して読み取り専用ワークロードをオフロードできます。 これにより、読み取り専用のワークロードを読み取り/書き込みワークロードから分離でき、パフォーマンスに影響が及ぶことがありません。 この機能は、分析などの論理的に分離された読み取り専用ワークロードを含むアプリケーション向けです。 Premium および Business Critical サービス レベルでは、余分なコストをかけることなく、この追加の処理能力を使用してパフォーマンス上のメリットをアプリケーションで得ることが可能です。

[セカンダリ レプリカ](service-tier-hyperscale-replicas.md)を少なくとも 1 つ追加していれば、Hyperscale サービス レベルでも *読み取りスケールアウト* 機能を利用できます。 Hyperscale セカンダリ[名前付きレプリカ](service-tier-hyperscale-replicas.md#named-replica-in-preview)を使用すれば、独立実行できるスケーリング、アクセスの分離、ワークロードの分離、大規模な読み取りスケールアウトなどの機能が利用できます。 読み取り専用ワークロードのロードバランシングに、単一のセカンダリ HA レプリカで利用できる以上のリソースが必要な場合は、複数のセカンダリ [HA レプリカ](service-tier-hyperscale-replicas.md#high-availability-replica)を使用できます。 

Basic、Standard、および General Purpose サービス レベルの高可用性アーキテクチャには、レプリカは一切含まれていません。 "*読み取りスケールアウト*" 機能は、これらのサービス レベルでは使用できません。

次の図に、Premium および Business Critical のデータベースとマネージド インスタンスの機能を図示します。

![読み取り専用レプリカ](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

新しい Premium、Business Critical、および Hyperscale データベースでは、"*読み取りスケールアウト*" 機能は既定で有効になっています。

> [!NOTE]
> Business Critical サービス レベルの Managed Instance と、最低 1 つのセカンダリ レプリカが存在する Hyperscale データベースでは、読み取りスケールアウトが常に有効になっています。

お使いの SQL 接続文字列が `ApplicationIntent=ReadOnly` で構成されている場合、アプリケーションは、そのデータベースまたはマネージド インスタンスの読み取り専用レプリカにリダイレクトされます。 `ApplicationIntent` プロパティの使用方法の詳細については、「[アプリケーションの目的を指定する](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)」をご覧ください。

SQL 接続文字列の `ApplicationIntent` 設定に関係なく、アプリケーションがプライマリ レプリカに確実に接続されるようにする場合は、データベースを作成するとき、またはその構成を変更するときに、読み取りスケールアウトを明示的に無効にする必要があります。 たとえば、Standard または General Purpose レベルから Premium または Business Critical にデータベースをアップグレードして、すべての接続を引き続きプライマリ レプリカに誘導するためには、読み取りスケールアウトを無効にします。無効にする詳しい方法は「[読み取りスケールアウトの有効化と無効化](#enable-and-disable-read-scale-out)」セクションをご覧ください。

> [!NOTE]
> 読み取り専用レプリカでは、クエリ ストア、SQL Profiler の各機能はサポートされていません。 

## <a name="data-consistency"></a>データの一貫性

プライマリ レプリカで行ったデータの変更は、読み取り専用レプリカに対して非同期的に伝達されます。 読み取り専用レプリカに接続されたセッション内では、読み取りが、常にトランザクション整合性が確保された状態となります。 ただし、データ伝達の待ち時間は変動するため、レプリカが異なれば、返されるデータの時点も、プライマリや他のレプリカと比べてわずかに異なる場合があります。 読み取り専用レプリカが利用不可となった後、セッションが再接続した場合、その接続先となるレプリカは、元のレプリカとは異なる時点のものである可能性があります。 同様に、アプリケーションが読み取り/書き込みセッションを使用してデータを変更し、読み取り専用セッションを使用してすぐに読み取る場合、読み取り専用レプリカに最新の変更がすぐに表示されない可能性があります。

プライマリ レプリカと読み取り専用レプリカとの間における標準的なデータ伝達の待ち時間は、数十ミリ秒から 10 ミリ秒未満の範囲で変わります。 ただしデータ伝達の待ち時間に関して、決まった上限というものはありません。 レプリカにおけるリソース使用率の高さなどの条件によって、待ち時間が大幅に延びる場合もあります。 複数セッションにわたるデータ整合性の保証が要求されるアプリケーションや、コミットされたデータの即時読み取りが要求されるアプリケーションでは、プライマリ レプリカを使用する必要があります。

> [!NOTE]
> データ伝達の待ち時間を監視するには、「[読み取り専用レプリカの監視とトラブルシューティング](#monitoring-and-troubleshooting-read-only-replicas)」を参照してください。

## <a name="connect-to-a-read-only-replica"></a>読み取り専用レプリカに接続する

データベースの読み取りスケールアウトを有効にすると、クライアントによって提供された接続文字列の `ApplicationIntent` オプションにより、接続が書き込みレプリカと読み取り専用レプリカのどちらにルーティングされるかが指定されます。 具体的には、`ApplicationIntent` 値が `ReadWrite` (既定値) の場合、接続は読み取り/書き込みレプリカにリダイレクトされます。 これは、接続文字列に `ApplicationIntent` が含まれていない場合の動作と同じです。 `ApplicationIntent` 値が `ReadOnly` の場合、接続は読み取り専用レプリカにルーティングされます。

たとえば、次の接続文字列はクライアントを読み取り専用レプリカに接続します (山かっこ内の項目は環境内の適切な値に置き換え、山かっこは削除します)。

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

次の接続文字列はどちらもクライアントを読み取り/書き込みレプリカに接続します (山かっこ内の項目は環境内の適切な値に置き換え、山かっこは削除します)。

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>接続先が読み取り専用レプリカであることを確認する

データベースのコンテキストで次のクエリを実行することにより、読み取り専用レプリカに接続しているかどうかを確認することができます。 読み取り専用レプリカに接続している場合は、READ_ONLY が返されます。

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> Premium および Business Critical サービス レベルでは、任意の時点で読み取り専用レプリカのいずれか 1 つのみにアクセスできます。 Hyperscale では、複数の読み取り専用レプリカがサポートされています。

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>読み取り専用レプリカの監視とトラブルシューティング

読み取り専用レプリカに接続されている場合、動的管理ビュー (DMV) にレプリカの状態が反映され、監視とトラブルシューティングの目的ためにこれにクエリを実行できます。 データベース エンジンには、さまざまな監視データを表示するための複数のビューが用意されています。 

次のビューは、レプリカの監視とトラブルシューティングによく使用されます。

| 名前 | 目的 |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 過去 1 時間のリソース使用率のメトリックが提供されます (サービス目標の制限に対する CPU、データ IO、ログ書き込みの使用率など)。|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| データベース エンジン インスタンスの待機統計の集計が提供されます。 |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| レプリカの正常性状態と同期の統計情報が提供されます。 再実行キューのサイズと再実行率は、読み取り専用レプリカにおけるデータ伝達の待ち時間のインジケーターとして機能します。 |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| データベース エンジンのパフォーマンス カウンターが提供されます。|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| 実行回数、CPU 使用時間など、クエリごとの実行統計が提供されます。|
|[sys.dm_exec_query_plan()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| キャッシュされたクエリ プランが提供されます。 |
|[sys.dm_exec_sql_text()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| キャッシュされたクエリ プランのクエリ テキストが提供されます。|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| クエリの実行中のリアルタイムでのクエリの進行状況が提供されます。|
|[sys.dm_exec_query_plan_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| クエリのランタイム統計を含む最後の既知の実際の実行プランが提供されます。|
|[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| すべてのデータベース ファイルのストレージ IOPS、スループット、および待機時間の統計が提供されます。 |

> [!NOTE]
> 論理マスター データベース内の `sys.resource_stats` および `sys.elastic_pool_resource_stats` DMV では、プライマリ レプリカのリソース使用率データが返されます。

### <a name="monitoring-read-only-replicas-with-extended-events"></a>拡張イベントを使用した読み取り専用レプリカの監視

読み取り専用レプリカに接続されているとき、拡張イベント セッションを作成することはできません。 ただし、Azure SQL Database では、プライマリ レプリカで作成および変更されたデータベーススコープの[拡張イベント](xevent-db-diff-from-svr.md) セッションの定義が読み取り専用レプリカ (geo レプリカを含む) にレプリケートされ、読み取り専用レプリカでのイベントがキャプチャされます。

プライマリ レプリカからのセッション定義に基づく読み取り専用レプリカの拡張イベント セッションは、プライマリ レプリカに関係なく開始および停止することができます。 拡張イベント セッションがプライマリ レプリカで削除されると、すべての読み取り専用レプリカでも削除されます。

### <a name="transaction-isolation-level-on-read-only-replicas"></a>読み取り専用レプリカでのトランザクション分離レベル

読み取り専用レプリカで実行されるクエリは、常に[スナップショット](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) トランザクション分離レベルにマップされます。 スナップショット分離では、行のバージョン管理を使用して、リーダーがライターをブロックするブロック シナリオが回避されます。

まれに、スナップショット分離トランザクションで、別の同時実行トランザクションで変更されたオブジェクト メタデータへのアクセスが行われる場合、エラー [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error)、"データベース '%.*ls' でスナップショット分離トランザクションが失敗しました。ステートメントからアクセスされるオブジェクトが、このトランザクションの開始後に別の同時実行トランザクションの DDL ステートメントで変更されました。 メタデータはバージョン管理されないため、この操作は許可されません。 メタデータに対する同時更新は、スナップショット分離と組み合わせると一貫性を損なう結果になる可能性があります。" というメッセージが表示される場合があります。

### <a name="long-running-queries-on-read-only-replicas"></a>読み取り専用レプリカでの実行時間の長いクエリ

読み取り専用レプリカでクエリを実行する場合、クエリで参照するオブジェクトのメタデータ (テーブル、インデックス、統計情報など) にアクセスする必要があります。稀なケースですが、クエリによって読み取り専用レプリカにあるオブジェクトにロックがかかっているときに、プライマリ レプリカでそのオブジェクトのメタデータに変更を加えるということが起こった場合は、そのクエリにより、プライマリ レプリカから読み取り専用レプリカに変更を適用する処理を[ブロック](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK)できます。 このようなクエリを長時間実行すると、読み取り専用レプリカのプライマリ レプリカとの同期が大きく外れる場合があります。 この場合、フェールオーバーが発生すると、フェールオーバー先になり得るレプリカ (Premium および Business Critical サービス レベルのセカンダリ レプリカ、Hyperscale HA レプリカ、すべての geo レプリカ) でデータベースの復旧が遅くなり、通常予期するよりもダウンタイムが長くなります。

読み取り専用レプリカで時間のかかるクエリを実行することにより、直接、間接にこのようなブロックが発生すると、過度のデータ遅延とデータベースの可用性への潜在的影響を避けるために、クエリが自動終了する場合があります。 このセッションでは、エラー 1219「優先度の高い DDL 操作により、セッションが切断されました」またはエラー 3947「セカンダリ コンピューティングが再実行をキャッチアップできなかったため、トランザクションが中止されました。 トランザクションを再試行してください」が表示されます。

> [!NOTE]
> 読み取り専用レプリカに対してクエリを実行したときに、エラー 3961、1219 または 3947 が発生した場合は、クエリを再試行してください。 あるいは、時間のかかるクエリをセカンダリ レプリカで実行している間、プライマリ レプリカでオブジェクトのメタデータを変更する操作 (スキーマの変更、インデックスのメンテナンス、統計情報の更新など) を避けてください。

> [!TIP]
> Premium および Business Critical サービス レベルでは、読み取り専用レプリカに接続されている場合、[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV の `redo_queue_size` および `redo_rate` 列を使用してデータ同期処理を監視でき、これは読み取り専用レプリカにおけるデータ伝達の待ち時間のインジケーターとして機能します。
> 

## <a name="enable-and-disable-read-scale-out"></a>読み取りスケールアウトの有効化と無効化

Premium、Business Critical、および Hyperscale サービス レベルでは、読み取りスケールアウトは既定で有効になっています。 Basic、Standard、または General Purpose サービス レベルで読み取りスケールアウトを有効にすることはできません。 セカンダリ レプリカを 1 つも設定していない Hyperscale データベースでは、読み取りスケールアウトは自動的に無効になります。

Premium または Business Critical サービス レベルの単一データベースとエラスティック プール データベースでの読み取りスケールアウトは、次の方法で無効にして再び有効にすることができます。

> [!NOTE]
> 単一データベースとエラスティック プール データベースでは、読み取りスケールアウトを無効にする機能は、下位互換性を維持するために提供されています。 Business Critical マネージド インスタンスでは、読み取りスケールアウトを無効にすることはできません。

### <a name="azure-portal"></a>Azure portal

読み取りスケールアウトの設定は、 **[構成]** データベース ブレードで管理できます。

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 Azure Resource Manager モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと Azure Resource Manager モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

Azure PowerShell で読み取りスケールアウトを管理するには、2016 年 12 月以降のリリースの Azure PowerShell が必要です。 最新の PowerShell リリースについては、[Azure PowerShell](/powershell/azure/install-az-ps) に関するページを参照してください。

Azure PowerShell で [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) コマンドレットを呼び出し、`-ReadScale` パラメーターに目的の値 (`Enabled` または `Disabled`) を渡すことで、読み取りスケールアウトを無効にし、再び有効にできます。

既存のデータベースの読み取りスケールアウトを無効にするには (山かっこ内の項目を自分の環境用の適切な値に置き換え、山かっこを削除してください):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

新しいデータベースの読み取りスケールアウトを無効にするには (山かっこ内の項目を自分の環境用の適切な値に置き換え、山かっこを削除してください):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

既存のデータベースの読み取りスケールアウトを再び有効にするには (山かっこ内の項目を自分の環境用の適切な値に置き換え、山かっこを削除してください):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

読み取りスケールアウトが無効なデータベースを作成する、または既存のデータベースの設定を変更するには、次の方法を使用して、以下の要求の例に示すように `readScale` プロパティを `Enabled` または `Disabled` に設定します。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

詳細については、「[データベース - 作成または更新](/rest/api/sql/databases/createorupdate)」を参照してください。

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>読み取り専用レプリカでの `tempdb` データベースの使用

プライマリ レプリカでの `tempdb` データベースは読み取り専用レプリカにはレプリケートされません。 各レプリカには、レプリカの作成時に作成された独自の `tempdb` データベースがあります。 これにより、`tempdb` が更新可能となり、クエリの実行時に変更できるようになります。 読み取り専用ワークロードで `tempdb` オブジェクトを使用する必要がある場合、これらのオブジェクトは、読み取り専用レプリカに接続した状態で、そのワークロードの一部として作成するべきです。

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>geo レプリケートされたデータベースで読み取りスケールアウトを使用する

geo レプリケーションしたセカンダリ データベースには、プライマリ データベースと同一の High Availability アーキテクチャが存在します。 読み取りスケールアウトが有効な、geo レプリケートされたセカンダリ データベースに接続している場合、`ApplicationIntent=ReadOnly` が設定されたセッションは、プライマリ書き込み可能データベース上でルーティングされるのと同じ方法で、高可用性レプリカの 1 つにルーティングされます。 `ApplicationIntent=ReadOnly` が設定されていないセッションは、geo レプリケートされたセカンダリのプライマリ レプリカ (これも読み取り専用) にルーティングされます。 

この方法により、geo レプリカを作成することで、読み取り書き込み用プライマリ データベースに、複数の読み取り専用レプリカを追加できます。 geo レプリカを 1 つ追加すると、1 組の新たな読み取り専用レプリカが追加されます。 geo レプリカは、プライマリ データベースのリージョンを含め任意の Azure リージョンに作成できます。

> [!NOTE]
> geo レプリケーションしたセカンダリ データベースのレプリカ間には、自動ラウンドロビンなどの負荷分散ルーティングの仕組みはありません。ただし、2 つ以上の HA レプリカが存在する Hyperscale geo レプリカは例外です。 この場合、読み取り専用を意図したセッションは、geo レプリカのすべての HA レプリカに分散します。

## <a name="feature-support-on-read-only-replicas"></a>読み取り専用レプリカでの機能のサポート

読み取り専用レプリカでの一部の機能の動作の一覧を次に示します。
* 読み取り専用レプリカでの監査は自動的に有効になります。 ストレージ フォルダーの階層、命名規則、ログ形式の詳細については、「[SQL Database 監査ログの形式](audit-log-format.md)」を参照してください。
* [Query Performance Insight](query-performance-insight-use.md) は、[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)のデータに依存しており、現在、読み取り専用レプリカでのアクティビティは追跡されません。 Query Performance Insight では、読み取り専用レプリカで実行されるクエリは表示されません。
* [自動チューニングに関する文書](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)で詳細に説明されているように、自動チューニングはクエリ ストアに依存しています。 自動チューニングは、プライマリ レプリカで実行されているワークロードに対してのみ機能します。

## <a name="next-steps"></a>次のステップ

- SQL Database Hyperscale オファリングの情報については、[Hyperscale サービス レベル](service-tier-hyperscale.md)に関する記事を参照してください。

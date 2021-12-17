---
title: アクティブな地理的レプリケーション
description: アクティブ geo レプリケーションを使用して、同じデータ センター リージョンまたは異なるリージョン内の Azure SQL Database に、個々のデータベースの読み取り可能なセカンダリ データベースを作成します。
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: ca958a3e7a43864caa673cd31736b1e661e7f608
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465366"
---
# <a name="active-geo-replication"></a>アクティブな地理的レプリケーション
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

アクティブ geo レプリケーションは、プライマリ データベースに対して継続的に同期された読み取り可能なセカンダリ データベースを作成できる機能です。 読み取り可能なセカンダリ データベースは、プライマリと同じ Azure リージョン、またはより一般的に別のリージョンに含まれる場合があります。 このような読み取り可能なセカンダリ データベースは、geo セカンダリまたは geo レプリカとも呼ばれます。

アクティブ geo レプリケーションはビジネス継続性ソリューションとして設計されています。このソリューションを使用すれば、地域災害または大規模な機能停止が発生した場合にディザスター リカバリーを迅速に実行することができます。 Geo レプリケーションを設定したら、別の Azure リージョンの geo セカンダリへの geo フェールオーバーを開始できます。 Geo フェールオーバーは、アプリケーションによってプログラムで、またはユーザーが手動で開始します。

> [!NOTE]
> Azure SQL Hyperscale のアクティブ geo レプリケーションは[現在、パブリック プレビュー](https://aka.ms/hsgeodr)段階です。 現在、次のような制限があります。geo セカンダリは同じまたは異なるリージョンに 1 つのみです。強制および計画されたフェールオーバーは現在サポートされていません。geo セカンダリからのデータベース復元はサポートされていません。データベース コピーのソース データベースとして、あるいは、別の geo セカンダリのためのプライマリとして geo セカンダリを使用することはサポートされていません。
> 
> geo セカンダリをプライマリ (書き込み可能なデータベース) にする必要がある場合は、次の手順に従ってください。
> 1. PowerShell の [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) コマンドレットまたは Azure CLI 向けの [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete_link) を使用して、geo レプリケーション リンクを解除します。これにより、セカンダリ データベースが読み取りと書き込みが可能なスタンドアロン データベースになります。 プライマリにコミットされていてもまだセカンダリにレプリケートされていないトランザクションは失われます。 これらの変更は、古いプライマリが使用可能な場合に復旧できます。また、古いプライマリを利用可能な最新の時点に復元することで復旧できる場合があります。
> 2. 古いプライマリが使用可能な場合はそれを削除し、新しいプライマリの geo レプリケーションを設定します (新しいセカンダリがシードされます)。 
> 3. これに応じて、アプリケーション内の接続文字列を更新します。

> [!NOTE]
> アクティブ geo レプリケーションは、 Azure SQL Managed Instance ではサポートされていません。 SQL Managed Instance の地理的なフェールオーバーについては、[自動フェールオーバー グループ](auto-failover-group-overview.md)を使用します。

> [!NOTE]
> アクティブ geo レプリケーションを使用して Azure Germany から SQL データベースを移行するには、「[アクティブ geo レプリケーションを使用した SQL Database の移行](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication)」を参照してください。

アプリケーションで geo レプリケーションに加えて安定した接続エンドポイントと geo フェールオーバーの自動サポートが必要な場合は、[自動フェールオーバーグループ](auto-failover-group-overview.md) を使用します。

次の図に、アクティブ geo レプリケーションを使用して行われる geo 冗長クラウド アプリケーションの一般的な構成を示します。

![アクティブ geo レプリケーション](./media/active-geo-replication-overview/geo-replication.png)

何らかの理由でプライマリ データベースが失敗した場合は、任意のセカンダリ データベースに geo フェールオーバーを開始できます。 セカンダリがプライマリ ロールに昇格すると、他のすべてのセカンダリが新しいプライマリに自動的にリンクされます。

Geo レプリケーションを管理し、以下を使用して geo フェールオーバーを開始できます。

- [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: 単一データベース](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: エラスティック プール](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL:単一のデータベースまたはエラスティック プール](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: 単一データベース](/rest/api/sql/replicationlinks)

アクティブ geo レプリケーションでは、[Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) テクノロジを利用して、プライマリ レプリカで生成されたトランザクション ログをすべての geo レプリカに非同期的にレプリケートします。 特定の時点におけるセカンダリ データベースは、プライマリ データベースよりもわずかに古い可能性がありますが、セカンダリ上のデータはトランザクション上の一貫性が保証されます。 つまり、コミットされていないトランザクションによって行われた変更は表示されません。 

> [!NOTE]
> アクティブ geo レプリケーションでは、プライマリ レプリカからセカンダリ レプリカにデータベース トランザクション ログをストリーミングすることで、変更がレプリケートされます。 これは、サブスクライバー上でDML (INSERT、UPDATE、DELETE) コマンドを実行して変更をレプリケートする[トランザクション レプリケーション](/sql/relational-databases/replication/transactional/transactional-replication)とは無関係です。

Geo レプリケーションで提供されるリージョン間の冗長性のため、自然災害、致命的なヒューマンエラー、または悪意のある行為によって Azure リージョン全体またはリージョンの一部が完全に失われた場合でも、アプリケーションをすばやく復旧できます。 Geo レプリケーション RPO については、「[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)」を参照してください。

次の図は、プライマリが米国中北部リージョン、geo セカンダリが米国中南部リージョンに構成されたアクティブ geo レプリケーションの例を示しています。

![geo レプリケーションのリレーションシップ](./media/active-geo-replication-overview/geo-replication-relationship.png)

アクティブ geo レプリケーションは、ディザスター リカバリーに加えて次のシナリオで使用できます。

- **データベースの移行**: アクティブ geo レプリケーションを使用して、最小限のダウンタイムでデータベースをあるサーバーから別のサーバーに移行できます。
- **アプリケーションのアップグレード**: アプリケーションのアップグレード時に、セカンダリをフェールバック コピーとして余分に作成することができます。

完全なビジネス継続性を実現するために、データベースのリージョンの冗長性を追加することは、ソリューションの一部でしかありません。 致命的な障害の後にアプリケーション (サービス) をエンド ツー エンドで復旧するには、そのサービスと依存しているサービスを構成するすべてのコンポーネントを復旧する必要があります。 このようなコンポーネントの例には、クライアント ソフトウェア (カスタム JavaScript が設定されたブラウザーなど)、Web フロント エンド、ストレージ、DNS などがあります。 すべてのコンポーネントが同じ障害に対する復元性を備えており、アプリケーションの目標復旧時間 (RTO) 以内に利用可能になることが重要です。 そのため、依存するサービスをすべて特定し、これらのサービスが提供する保証と機能について把握しておく必要があります。 そのうえで、依存するサービスのフェールオーバー中もサービスが確実に機能するように対策を講じる必要があります。 ディザスター リカバリーのソリューション設計の詳細については、[アクティブ geo レプリケーションを使用したディザスター リカバリー用のクラウド ソリューションの設計](designing-cloud-solutions-for-disaster-recovery.md)に関するページを参照してください。

## <a name="active-geo-replication-terminology-and-capabilities"></a>アクティブ geo レプリケーションの用語と機能

- **自動非同期レプリケーション**

  作成できるのは、既存のデータベースの geo セカンダリのみです。 Geo セカンダリは、プライマリ データベースを持つサーバー以外の任意の論理サーバーに作成できます。 作成されると、geo セカンダリ レプリカにプライマリ データベースのデータが設定されます。 このプロセスはシード処理と呼ばれます。 Geo セカンダリ データベースが作成およびシードされた後、プライマリ データベースの更新が geo セカンダリ データベースに非同期で自動的にレプリケートされます。 非同期レプリケーションでは、トランザクションはプライマリ データベースでコミットされた後にレプリケートされます。

- **読み取り可能なセカンダリ レプリカ**

  アプリケーションは、プライマリ データベースへのアクセスに使用されているのと同じ、または異なるセキュリティ プリンシパルを使用して、geo セカンダリ レプリカにアクセスして読み取り専用クエリを実行できます。 詳細については、「[読み取り専用レプリカを使用して読み取り専用クエリ ワークロードをオフロードする](read-scale-out.md)」を参照してください。

   > [!IMPORTANT]
   > Geo レプリケーションを使用して、同じリージョンにプライマリとしてセカンダリレプリカを作成できます。 これらのセカンダリを使用して、同じリージョンの読み取りスケールアウト シナリオを満たします。 ただし、同じリージョン内のセカンダリ レプリカは、致命的な障害や大規模な停止に対する更なる回復力を提供しないため、ディザスター リカバリーの目的に適したフェールオーバー ターゲットではありません。 また、可用性ゾーンの分離も保証されません。 [ゾーン冗長の構成](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)で Business Critical または Premium サービス レベルを使用するか、General Purpose サービス レベルの[ゾーン冗長の構成](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)を使用して、可用性ゾーンの分離を行ってください。
   >

- **計画的 geo フェールオーバー**

  計画的 geo フェールオーバーでは、完全なデータ同期が完了した後、プライマリと geo セカンダリ データベースのロールが切り替わります。 計画されたフェールオーバーでは、データ損失は発生しません。 計画された geo フェールオーバーの期間は、geo セカンダリに同期する必要があるプライマリのトランザクション ログのサイズによって異なります。 計画的 geo フェールオーバーは、次のシナリオ用に設計されています。

  - データ損失が許容されない場合は、運用環境でディザスター リカバリー (DR) ドリルを行います。 
  - データベースを別のリージョンに再配置します 
  - 機能停止が軽減 (フェールバックとして知られています) された後、データベースをプライマリ リージョンに返します

- **計画されていない geo フェールオーバー**

  計画されていないフェールオーバーや geo フェールオーバーでは、プライマリと同期せずに、geo セカンダリをすぐにプライマリ ロールに切り替えます。 プライマリにコミットされていても、セカンダリにレプリケートされていないトランザクションは失われます。 この操作は、停止中、プライマリにアクセスできないがデータベースの可用性を早急に回復しなければならない場合の復旧方法として設計されています。 元のプライマリがオンラインに戻されると、自動的に再接続され、現在のプライマリ データを使用してシードを再設定され、新しい geo セカンダリになります。

  > [!IMPORTANT]
  > 計画済み geo フェールオーバーまたは計画外 geo フェールオーバーの後、新しいプライマリの接続エンドポイントが変更されます。これは、新しいプライマリが別の論理サーバーに位置されているからです。

- **複数のセカンダリ geo データベース**

  プライマリに対して最大 4 つの geo セカンダリを作成できます。 セカンダリが 1 つしか存在しない場合に障害が発生すると、新しいセカンダリ が作成されるまで、アプリケーションは高いリスクにさらされます。 セカンダリ が複数存在する場合、セカンダリ のいずれかに障害が発生しても、アプリケーションは保護された状態が維持されます。 読み取り専用のワークロードをスケール アウトするために、追加のセカンダリを使用することもできます。

  > [!TIP]
  > グローバルに分散されたアプリケーションの構築にアクティブ geo レプリケーションを使用し、4 つを超えるリージョンにデータへの読み取り専用アクセスを提供する必要がある場合は、セカンダリのセカンダリ (チェーンと呼ばれるプロセス) を作成し、追加の geo レプリカを作成できます。 チェーンされた geo レプリカでのレプリケーションのラグは、プライマリに直接接続されている geo レプリカよりも高くなる可能性があります。 チェーン geo レプリケーション トポロジの設定は、プログラムによってのみサポートされており、Azure portal からはサポートされていません。

- **エラスティック プール内のデータベースの geo レプリケーション**

  セカンダリ データベースは、Single Database またはエラスティック プールのデータベースにできます。 Geo セカンダリ データベースごとにエラスティック プールの選択は独立しており、トポロジ内の他のレプリカ (プライマリまたはセカンダリ) の構成には依存しません。 各エラスティック プールは、1 つの論理サーバー内に含まれます。 論理サーバー上のデータベース名は一意である必要があるため、同じプライマリの複数の geo セカンダリがエラスティック プールを共有できません。

- **ユーザー制御の geo フェールオーバーとフェールバック**

  初期シード処理が完了した geo セカンダリは、アプリケーションまたはユーザーがいつでもプライマリ ロールに明示的に切り替え (フェールオーバー) できます。 プライマリにアクセスできない停止中は、計画外の geo フェールオーバーのみを使用できます。 これはすぐに geo セカンダリを新しいプライマリに昇格します。 停止が軽減された場合、システムは自動的に復旧されたプライマリを geo セカンダリにし、新しいプライマリで最新の状態に戻します。 geo レプリケーションの非同期の性質上、これらのトランザクションが geo セカンダリにレプリケートされる前にプライマリが失敗した場合、計画外の geo フェールオーバー中に最近のトランザクションが失われる可能性があります。 複数の geo セカンダリを持つプライマリがフェールオーバーすると、システムは自動的にレプリケーション関係を再構成して、いかなるユーザー介入も必要とすることなく、残りの geo セカンダリを新しく昇格されたプライマリにリンクします。 geo フェールオーバーの原因となった障害が軽減された後、プライマリを元のリージョンに復帰させることが望ましい場合があります。 これを行うには、計画された geo フェールオーバーを呼び出します。

## <a name="prepare-for-geo-failover"></a><a name="preparing-secondary-database-for-failover"></a> Geo フェールオーバーの準備

geo フェールオーバー後にアプリケーションが新しいプライマリにすぐアクセスできることが確実になるよう、セカンダリ サーバーの認証とネットワークが正しく構成されていることを確認してください。 詳細については、 [障害復旧後の SQL Database のセキュリティ](active-geo-replication-security-configure.md)に関するページを参照してください。 また、セカンダリデータベースのバックアップ保持ポリシーがプライマリのバックアップ保持ポリシーと一致していることを確認します。 この設定はデータベースの一部ではなく、プライマリからはレプリケートされません。 既定では、geo セカンダリは 7 日間の既定の PITR 保持期間で構成されます。 詳細については、「 [SQL Database 自動バックアップ](automated-backups-overview.md)」を参照してください。

> [!IMPORTANT]
> ご使用のデータベースがフェールオーバー グループのメンバーである場合、geo レプリケーション フェールオーバー コマンドを使用してそのフェールオーバーを開始することはできません。 グループのフェールオーバー コマンドを使用してください。 個々のデータベースをフェールオーバーする必要がある場合、最初にそれをフェールオーバー グループから削除する必要があります。 詳細については、[自動フェールオーバー グループ](auto-failover-group-overview.md)をご覧ください。

## <a name="configure-geo-secondary"></a><a name="configuring-secondary-database"></a>Geo セカンダリの構成

プライマリと geo セカンダリの両方が同じサービス階層を持つ必要があります。 また、geo セカンダリは、プライマリと同じバックアップ ストレージの冗長性とコンピューティング サイズ (DTU または仮想コア) で構成することも強くお勧めします。 プライマリで書き込みワークロードの負荷が高くなっている場合、コンピューティング サイズがより小さな geo セカンダリは維持できない可能性があります。 これにより、geo セカンダリでレプリケーションが発生し、最終的に geo セカンダリが利用できなくなる可能性があります。 このようなリスクを軽減するために、アクティブ geo レプリケーションは、必要に応じてプライマリのトランザクション ログ速度を減少 (スロットル) してセカンダリが追い付けるようにします。

バランスがとれていない geo セカンダリ構成の他の影響として、フェールオーバーの後、新しいプライマリのコンピューティング容量の不足のためにアプリケーションのパフォーマンスが低下する可能性があることが挙げられます。 その場合は、データベースが十分なリソースを持つように拡張する必要があります。これには膨大な時間がかかる可能性があり、スケールアップ プロセスの最後には[高可用性](high-availability-sla.md)フェールオーバーが必要となり、アプリケーションのワークロードが中断される可能性があります。

より低いコンピューティングサイズで geo セカンダリを作成する場合は、時間の経過に伴うログ IO 率をプライマリで監視する必要があります。 これにより、レプリケーションの負荷を維持するために必要な geo セカンダリの最小コンピューティングサイズを見積もることができます。 たとえば、プライマリ データベースが P6 (1000 DTU) で、ログ IO が 50% で維持されている場合、geo セカンダリは P4 (500 DTU) 以上である必要があります。 履歴ログ IO データを取得するには、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ビューを使用します。 短期間の急増を詳細に反映した最新のログ IO データを取得するには、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ビューを使用します。

> [!TIP]
> geo セカンダリのコンピューティング サイズが低いため、プライマリで行われたトランザクション ログ IO のスロットリングは、HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO の待機の種類を使用して報告され、[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) および [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) データベース ビューに表示されます。
>
> プライマリのトランザクション ログ IO は、geo セカンダリのコンピューティング サイズが小さいことに関連しない理由によりスロットルされる可能性があります。 この種類のスロットリングは、geo セカンダリのコンピューティング サイズがプライマリよりも同じかそれ以上である場合にも発生する可能性があります。 さまざまな種類のログ IO スロットリングの待機の種類などの詳細については、「[トランザクション ログ速度ガバナンス](resource-limits-logical-server.md#transaction-log-rate-governance)」を参照してください。

既定では、geo セカンダリのバックアップ ストレージの冗長性は、プライマリ データベースのものと同じです。 別のバックアップ ストレージの冗長性を使用して geo セカンダリを構成することもできます。 バックアップは、常にプライマリ データベースで実行されます。 セカンダリが異なるバックアップ ストレージの冗長性で構成されている場合、geo セカンダリがプライマリに昇格されたときの geo フェールオーバー後に、新しいプライマリ (前のセカンダリ) で選択されたストレージの種類に従って (RA-GRS, ZRS, LRS)、バックアップが保存され、課金されます。 

## <a name="cross-subscription-geo-replication"></a>サブスクリプション間 geo レプリケーション

プライマリのサブスクリプションとは異なるサブスクリプションで geo セカンダリを作成するには (同じ Azure Active Directory テナントであるかどうかにかかわらず)、このセクションの手順を実行します。

1. 以下の T-SQL コマンドを実行しているクライアントコンピューターの IP アドレスを、プライマリサーバーとセカンダリサーバーの **両方** のサーバーファイアウォールに追加します。 この IP アドレスを確認するには、同じクライアントコンピューターからプライマリサーバーに接続しているときに、次のクエリを実行します。
  
   ```sql
   select client_net_address from sys.dm_exec_connections where session_id = @@SPID;
   ``` 

   詳細については、「[ファイアウォールの構成](firewall-configure.md)」を参照してください。

2. **プライマリ** サーバーの master データベースで、アクティブ geo レプリケーションのセットアップ専用の SQL 認証ログインを作成します。 必要に応じて、ログイン名とパスワードを調整します。

   ```sql
   create login geodrsetup with password = 'ComplexPassword01';
   ```

3. 同じデータベースで、ログイン用のユーザーを作成し、`dbmanager`ロールに追加し ます。

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

4. 新しいログインの SID 値をメモしておきます。 次のクエリを使用して SID 値を取得します。

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup';
   ```

5. (master データベースではなく)**プライマリ** データベースに接続し、同じログインのユーザーを作成します。

   ```sql
   create user geodrsetup for login geodrsetup;
   ```

6. 同じデータベースで、ユーザーを`db_owner`ロールに追加します。

   ```sql
   alter role db_owner add member geodrsetup;
   ```

7. **セカンダリ** サーバーの master データベースで、同じ名前、パスワード、および SID を使用して、プライマリサーバーと同じログインを作成します。 次のサンプルコマンドの 16 進数の SID 値を、手順 4 で取得したものに置き換えます。

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E;
   ```

8. 同じデータベースで、ログイン用のユーザーを作成し、`dbmanager`ロールに追加し ます。

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

9. 新しいログインを使用して **プライマリ** サーバー上の master データベースに接続`geodrsetup`し、セカンダリサーバーで geo セカンダリの作成を開始します。 必要に応じて、データベース名とセカンダリサーバー名を調整します。 コマンドが実行されたら、**プライマリ** データベースの [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)ビューに対してクエリを実行し、**プライマリ** サーバーの master データベースの[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)ビューに対してクエリを実行することで、geo セカンダリの作成を監視できます。 Geo セカンダリの作成に必要な時間は、プライマリデータベースのサイズによって異なります。

   ```sql
   alter database [dbrep] add secondary on server [servername];
   ```

10. Geo セカンダリが正常に作成されたら、この手順で作成したユーザー、ログイン、およびファイアウォールルールを削除できます。

> [!NOTE]
> セットアップと geo フェールオーバーを含むサブスクリプション間 geo レプリケーション操作は、T-SQL コマンドを使用することでのみサポートされます。
> 
> プライマリまたはセカンダリのサーバーに[プライベート エンドポイント](private-endpoint-overview.md)が構成されているとき、[パブリック ネットワーク アクセスが拒否される](connectivity-settings.md#deny-public-network-access)場合、T-SQL で geo セカンダリを追加することはできません。 プライベート エンドポイントが構成されているが、パブリック ネットワーク アクセスが許可される場合、パブリック IP アドレスからプライマリ サーバーに接続するときに geo セカンダリを追加できます。 geo セカンダリを追加すると、パブリック アクセスを拒否できます。
> 
> プライマリまたはセカンダリのどちらかの論理サーバー上で Azure SQL に対する [Azure Active Directory](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673) 専用認証がアクティブ (有効) になっている場合、別の Azure テナント内の論理サーバー上での geo セカンダリの作成はサポートされていません。

## <a name="keep-credentials-and-firewall-rules-in-sync"></a><a name="keeping-credentials-and-firewall-rules-in-sync"></a>資格情報とファイアウォール規則の同期を保つ

データベースへの接続にパブリックネットワークアクセスを使用する場合は、geo レプリケートされたデータベースに対して[データベースレベルの IP ファイアウォール規則](firewall-configure.md)を使用することをお勧めします。 これらのルールはデータベースと共にレプリケートされます。これにより、すべての geo セカンダリがプライマリと同じ IP ファイアウォールルールを持つようになります。 このアプローチにより、プライマリとセカンダリ データベースをホストするサーバー上で、顧客がファイアウォール規則を手動で構成、管理する必要性がなくなります。 同様に、データアクセスに[包含データベースユーザー](logins-create-manage.md)を使用すると、プライマリデータベースとセカンダリデータベースの両方に同じ認証資格情報が常に割り当てられます。 これにより、geo フェールオーバー後に、認証資格情報の不一致による中断が発生しません。

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> プライマリデータベースのスケーリング

Geo セカンダリを切断せずに、プライマリデータベースを (同じサービスレベル内の) 別のコンピューティングサイズにスケールアップまたはスケールダウンすることができます。 スケールアップするときは、まず geo セカンダリをスケールアップしてから、プライマリをスケールアップすることをお勧めします。 スケールダウンする場合は、順序を逆にします。最初にプライマリをスケールダウンし、次にセカンダリをスケールダウンします。

> [!NOTE]
> フェールオーバーグループの構成の一部として geo セカンダリを作成した場合は、geo セカンダリをスケールダウンすることは推奨されません。 これは、geo フェールオーバー後の通常のワークロードを処理するのに十分な容量がデータ層にあることを確認するためのものです。

> [!IMPORTANT]
> フェールオーバー グループのプライマリ データベースを上位のサービス レベル (エディション) にスケーリングするには、最初にセカンダリ データベースを上位のレベルにスケーリングする必要があります。 たとえば、プライマリを General Purpose から Business Critical にスケールアップする場合は、まず geo セカンダリを Business Critical にスケールアップする必要があります。 この規則に違反する方法でプライマリまたは geo セカンダリをスケーリングしようとすると、次のエラーが表示されます。
>
> `The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> 重要なデータが失われないようにする

ワイドエリアネットワークの待機時間が長いため、geo レプリケーションは非同期レプリケーションメカニズムを使用します。 非同期レプリケーションを使用すると、プライマリに障害が発生した場合にデータ損失が回避される可能性があります。 重要なトランザクションをデータ損失から保護するために、アプリケーション開発者はトランザクションをコミットした直後に [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) ストアドプロシージャを呼び出すことができます。 を呼び出す `sp_wait_for_database_copy_sync` と、最後にコミットされたトランザクションが転送され、セカンダリデータベースのトランザクションログに書き込まれるまで、呼び出し元のスレッドがブロックされます。 ただし、転送されたトランザクションがセカンダリで再生 (再実行) されるのを待機することはありません。 `sp_wait_for_database_copy_sync` は、特定の geo レプリケーションリンクにスコープが設定されています。 プライマリ データベースへの接続権限を持つユーザーが、このプロシージャを呼び出すことができます。

> [!NOTE]
> `sp_wait_for_database_copy_sync` 特定のトランザクションの geo フェールオーバー後のデータ損失を防ぎますが、読み取りアクセスの完全同期は保証しません。 プロシージャ呼び出しによって発生する遅延は `sp_wait_for_database_copy_sync` 大きくなる可能性があり、呼び出し時のプライマリでまだ転送されていないトランザクションログのサイズによって異なります。

## <a name="monitor-geo-replication-lag"></a><a name="monitoring-geo-replication-lag">geo レプリケーションのラグの監視</a>

RPO に関する遅延を監視するには、プライマリ データベースの [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) の *replication_lag_sec* 列を使用します。 プライマリでコミットされたトランザクション間の遅延が秒単位で表示され、セカンダリのトランザクションログに書き込まれます。 たとえば、ラグが 1 秒の場合は、プライマリが停止によって影響を受け、geo フェールオーバーが開始されると、最後の 1 秒間にコミットされたトランザクションは失われます。

セカンダリに書き込み済みのプライマリ データベースに対する変更に関する遅延を測定するには、geo セカンダリの *last_commit* 時間を、プライマリの同じ値と比較します。

> [!TIP]
> プライマリの *replication_lag_sec* が NULL になっていることがありますが、これは、現時点でプライマリと geo セカンダリとの間の遅延時間が不明であることを意味します。 これは、プロセスが再起動した後に発生する一時的な状態であることがほとんどです。 *replication_lag_sec* から長時間にわたって NULL が返される場合は、アラートを送ることを検討してください。 接続エラーが原因で、geo セカンダリがプライマリと通信できないことを示す場合があります。
> 
> Geo セカンダリとプライマリ間の *last_commit* 時間に大きな差が生じる可能性のある状況は他にもあります。 例えば、長時間変更がなかったプライマリでコミットが行われた場合、差異は非常に大きくなりますが、すぐにゼロに戻ります。 この2つの値の差が長時間続く場合は、アラートを送信することを検討してください。

## <a name="programmatically-manage-active-geo-replication"></a><a name="programmatically-managing-active-geo-replication"></a> アクティブ geo レプリケーションのプログラムでの管理

前に説明したように、アクティブ geo レプリケーションは、T-SQL、Azure PowerShell および REST API を使用してプログラムによって管理することもできます。 次の表では、使用できるコマンド セットについて説明します。 アクティブ geo レプリケーションには、管理のための Azure Resource Manager API 一式 ([Azure SQL Database REST API](/rest/api/sql/)、[Azure PowerShell コマンドレット](/powershell/azure/)など) が含まれています。 こうした API は、Azure ロールベースのアクセス制御 (Azure RBAC) をサポートします。 アクセス ロールの実装方法の詳細については、[Azure のロール ベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) に関するページをご覧ください。

### <a name="t-sql-manage-geo-failover-of-single-and-pooled-databases"></a><a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>単一データベースおよびプールされたデータベースの geo フェールオーバーを管理します。

> [!IMPORTANT]
> これらの T-SQL コマンドは、アクティブ geo レプリケーションにのみ適用され、フェールオーバー グループには適用されません。 また、フェールオーバーグループのみをサポートする SQL Managed Instance には適用されません。

| command | 説明 |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |**ADD SECONDARY ON SERVER** 引数を使用して、既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |**FAILOVER** または **FORCE_FAILOVER_ALLOW_DATA_LOSS** を使用して、セカンダリ データベースをプライマリに切り替え、フェールオーバーを開始します |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |**REMOVE SECONDARY ON SERVER** を使用して、SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。 |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |サーバー上の各データベースの、既存のレプリケーション リンクの情報をすべて返します。 |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |最新のレプリケーション時刻、最後のレプリケーションの遅延、および指定されたデータベースのレプリケーション リンクに関する他の情報を取得します。 |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |レプリケーション リンクの変更を含むすべてのデータベース操作の状態が表示されます。 |
| [sys.sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |コミットされたすべてのトランザクションが、geo セカンダリのトランザクションログに書き込まれるまで待機します。 |
|  | |

### <a name="powershell-manage-geo-failover-of-single-and-pooled-databases"></a><a name="powershell-manage-failover-of-single-and-pooled-databases"></a>単一データベースおよびプールされたデータベースの geo フェールオーバーを管理します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

| コマンドレット | 説明 |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |1 つまたは複数のデータベースを取得します。 |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |セカンダリ データベースをプライマリに切り替えて、フェールオーバーを開始します。 |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。 |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |データベースの geo レプリケーションリンクを取得します。 |
|  | |

> [!TIP]
> サンプル スクリプトについては、[アクティブ geo レプリケーションを使用した単一データベースの構成およびフェールオーバー](scripts/setup-geodr-and-failover-database-powershell.md)に関するページ、および[アクティブ geo レプリケーションを使用したプールされたデータベースの構成およびフェールオーバー](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)に関するページをご覧ください。

### <a name="rest-api-manage-geo-failover-of-single-and-pooled-databases"></a><a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: 単一データベースおよびプールされたデータベースの geo フェールオーバーを管理します。

| API | 説明 |
| --- | --- |
| [Create または Update Database (createMode=Restore)](/rest/api/sql/databases/createorupdate) |プライマリまたはセカンダリ データベースを作成、更新、または復元します。 |
| [Get Create or Update Database Status](/rest/api/sql/databases/createorupdate) |復元操作中にステータスを返します。 |
| [Set Secondary Database as Primary (Planned Failover) (セカンダリ データベースをプライマリとして設定する (計画されたフェールオーバー))](/rest/api/sql/replicationlinks/failover) |現在のプライマリ データベースからフェールオーバーして、どのセカンダリ データベースがプライマリかを設定します。 **このオプションは、SQL Managed Instance ではサポートされていません。**|
| [Set Secondary Database as Primary (計画されていないフェールオーバー)](/rest/api/sql/replicationlinks/failoverallowdataloss) |現在のプライマリ データベースからフェールオーバーして、どのセカンダリ データベースがプライマリかを設定します。 この操作を行うとデータが失われる可能性があります。 **このオプションは、SQL Managed Instance ではサポートされていません。**|
| [Get Replication Link](/rest/api/sql/replicationlinks/get) |geo レプリケーション パートナーシップで指定されたデータベースの特定のレプリケーション リンクを取得します。 sys.geo_replication_links カタログ ビューで表示可能な情報を取得します。 **このオプションは、SQL Managed Instance ではサポートされていません。**|
| [Replication Links - List By Database](/rest/api/sql/replicationlinks/listbydatabase) | geo レプリケーション パートナーシップで指定されたデータベースのすべてのレプリケーション リンクを取得します。 sys.geo_replication_links カタログ ビューで表示可能な情報を取得します。 |
| [Delete Replication Link](/rest/api/sql/replicationlinks/delete) | データベース レプリケーション リンクを削除します。 フェールオーバー中には実行できません。 |
|  | |

## <a name="next-steps"></a>次のステップ

- サンプル スクリプトは、以下を参照してください。
  - [アクティブ geo レプリケーションを使用して、単一データベースを構成し、フェールオーバーします](scripts/setup-geodr-and-failover-database-powershell.md)。
  - [アクティブ geo レプリケーションを使用して、プールされたデータベースを構成およびフェールオーバーする](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- SQL Database でも自動フェールオーバー グループがサポートされています。 詳細については、[自動フェールオーバー グループ](auto-failover-group-overview.md)の使用に関するページを参照してください。
- ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](automated-backups-overview.md)」を参照してください
- 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](recovery-using-backups.md)に関する記事を参照してください。
- 新しいプライマリ サーバーとデータベースの認証要件については、 [障害復旧後の SQL Database のセキュリティ](active-geo-replication-security-configure.md)に関する記事を参照してください。

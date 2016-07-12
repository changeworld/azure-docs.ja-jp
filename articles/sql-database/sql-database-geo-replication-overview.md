<properties
	pageTitle="Azure SQL Database のアクティブ geo レプリケーション"
	description="アクティブ Geo レプリケーションにより、任意の Azure データ センターでデータベースのレプリカを 4 つ設定できます。"
	services="sql-database"
	documentationCenter="na"
	authors="stevestein"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
   ms.workload="sqldb-bcdr"
	ms.date="06/14/2016"
	ms.author="sstein" />

# 概要: Azure SQL Database のアクティブ geo レプリケーション

アクティブ geo レプリケーションにより、同じまたは異なるデータ センターの場所 (リージョン) に最大 4 つの読み取り可能なセカンダリ データベースを構成できます。セカンダリ データベースは、データ センターで障害が発生した場合やプライマリ データベースに接続できない場合のクエリとフェールオーバーに使用できます。

>[AZURE.NOTE] すべてのサービス レベルのすべてのデータベースでアクティブ geo レプリケーション (読み取り可能なセカンダリ) を使用できるようになりました。2017 年 4 月に、読み取り不能なタイプのセカンダリが廃止され、既存の読み取り不能なデータベースは読み取り可能なセカンダリに自動的にアップグレードされます。

 [Azure ポータル](sql-database-geo-replication-portal.md)、[PowerShell](sql-database-geo-replication-powershell.md)、[Transact-SQL](sql-database-geo-replication-transact-sql.md)、または [REST API - データベースの作成または更新](https://msdn.microsoft.com/library/azure/mt163685.aspx)を使用して、アクティブ geo レプリケーションを構成できます。

> [AZURE.SELECTOR]
- [構成: Azure ポータル](sql-database-geo-replication-portal.md)
- [構成: PowerShell](sql-database-geo-replication-powershell.md)
- [構成: T-SQL](sql-database-geo-replication-transact-sql.md)

何らかの理由でプライマリ データベースにエラーが発生したか、単にプライマリ データベースをオフラインにする必要がある場合、任意のセカンダリ データベースに*フェールオーバー*させることができます。セカンダリ データベースの 1 つに対してフェールオーバーがアクティブな場合、その他すべてのセカンダリ データベースは新しいプライマリ データベースに自動的にリンクします。

[Azure ポータル](sql-database-geo-replication-failover-portal.md)、[PowerShell](sql-database-geo-replication-failover-powershell.md)、[Transact-SQL](sql-database-geo-replication-failover-transact-sql.md)、[REST API - 計画されたフェールオーバー](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)、または[REST API - 計画されていないフェールオーバー](https://msdn.microsoft.com/library/azure/mt582027.aspx)を使用して、セカンダリにフェールオーバーできます。


> [AZURE.SELECTOR]
- [フェールオーバー: Azure ポータル](sql-database-geo-replication-failover-portal.md)
- [フェールオーバー: PowerShell](sql-database-geo-replication-failover-powershell.md)
- [フェールオーバー: T-SQL](sql-database-geo-replication-failover-transact-sql.md)

アクティブ geo レプリケーション機能は、同じ Microsoft Azure リージョン内または異なるリージョン間にデータベースの冗長性 (地理的冗長性) を提供するメカニズムを実装します。アクティブ geo レプリケーションを使用すると、データベースのコミット済みトランザクションが、別のサーバー上にある最大 4 つのデータベース コピーに非同期にレプリケートされます。アクティブ Geo レプリケーションが構成されている場合は、指定されたサーバーにセカンダリ データベースが作成されます。元のデータベースはプライマリ データベースになります。プライマリ データベースによって、コミット済みのトランザクションが各セカンダリ データベースに非同期にレプリケートされるしくみです。特定の時点におけるセカンダリ データベースは、プライマリ データベースよりもわずかに古い可能性がありますが、プライマリ データベースにコミットされた変更とのトランザクション上の整合性が常に保証されます。

アクティブ geo レプリケーションの主な利点の 1 つは、復旧に要する時間が非常に短い、データベース レベルの障害復旧ソリューションが提供されることです。別のリージョン内のサーバーにセカンダリ データベースを配置すると、アプリケーションの回復力が最大になります。リージョン間で冗長性が確保されるため、自然災害、致命的なヒューマン エラー、または悪意のある行為によってデータセンター全体またはその一部の機能が完全に失われた場合でも、アプリケーションを復旧できます。次の図は、プライマリが米国中北部リージョン、セカンダリが米国中南部リージョンにある Premium データベース上に構成されたアクティブ Geo レプリケーションの例を示しています。

![geo レプリケーションのリレーションシップ](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

もう 1 つの主要なメリットは、セカンダリ データベースが読み取り可能であり、ジョブを報告するなどの読み取り専用ワークロードの負荷を軽減するために使用できるということです。セカンダリ データベースを負荷分散のためのみに使用する場合は、プライマリと同じリージョンに作成できます。ただし、この場合、致命的な障害に対するアプリケーションの回復力は増加しません。

アクティブ geo レプリケーションを使用できるシナリオは、上記以外にも次のようなものがあります。

- **データベースの移行**: アクティブ geo レプリケーションを使用して、最小限のダウンタイムでデータベースをあるサーバーから別のサーバーにオンラインで移行できます。
- **アプリケーションのアップグレード**: アプリケーションのアップグレード時に、セカンダリをフェールバック コピーとして余分に作成することができます。

ビジネスの継続性を実現するにあたって、データセンター間のデータベース冗長性を追加することは、ソリューションのほんの一部です。致命的な障害の後にアプリケーション (サービス) をエンド ツー エンドで復旧するには、そのサービスと依存しているサービスを構成するすべてのコンポーネントを復旧する必要があります。このようなコンポーネントの例には、クライアント ソフトウェア (カスタム JavaScript が設定されたブラウザーなど)、Web フロント エンド、ストレージ、DNS などがあります。すべてのコンポーネントが同じ障害に対する復元性を備えており、アプリケーションの目標復旧時間 (RTO) 以内に利用可能になることが重要です。そのため、依存するサービスをすべて特定し、これらのサービスが提供する保証と機能について把握しておく必要があります。そのうえで、依存するサービスのフェールオーバー中もサービスが確実に機能するように対策を講じる必要があります。障害復旧のソリューション設計の詳細については、[アクティブ geo レプリケーションを使用した障害復旧用のクラウド ソリューションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)に関するページを参照してください。

## アクティブ geo レプリケーションの機能
アクティブ geo レプリケーションには、次の重要な機能が用意されています。

- **自動非同期レプリケーション**: セカンダリ データベースは、既存のデータベースに追加することによってのみ作成できます。セカンダリは、別の Azure SQL Database サーバーにのみ作成できます。作成した後、プライマリ データベースからコピーしたデータをセカンダリ データベースに設定できます。このプロセスはシード処理と呼ばれます。オンライン セカンダリ データベースが作成およびシードされた後、プライマリ データベースの更新がセカンダリ データベースに非同期で自動的にレプリケートされます。つまり、トランザクションはプライマリ データベースでコミットされた後に、セカンダリ データベースにレプリケートされます。SQL Database では、シード処理が完了すると、セカンダリ データベースのトランザクション一貫性が常に保証されます。

- **複数のセカンダリ データベース**: 2 つ以上のセカンダリ データベースを使用すると、プライマリ データベースとアプリケーションの冗長性が向上し、保護レベルが強化されます。セカンダリ データベースが複数存在する場合、セカンダリ データベースのいずれかに障害が発生しても、アプリケーションは保護された状態が維持されます。セカンダリ データベースが 1 つしか存在しない場合に障害が発生すると、新しいセカンダリ データベースが作成されるまで、アプリケーションは高いリスクにさらされます。

- **読み取り可能なセカンダリ データベース**: アプリケーションは、プライマリ データベースへのアクセスに使用されているのと同じ、または異なるセキュリティ プリンシパルを使用して、セカンダリ データベースにアクセスして読み取り専用操作を実行できます。セカンダリ データベースは、セカンダリ上で実行されるクエリによってプライマリ (ログの再生) の更新プログラムのレプリケーションが遅延しないように、スナップショット分離モードで動作します。

>[AZURE.NOTE] セカンダリがプライマリから受け取るスキーマの更新がある場合、セカンダリ データベース上にスキーマ ロックが必要であるため、セカンダリでのログの再生は遅延します。

- **エラスティック プール データベースのアクティブ geo レプリケーション**: エラスティック データベース プールでは、任意のデータベースに対して、アクティブ geo レプリケーションを構成することができます。セカンダリ データベースは、別の Elastic Database プールで指定できます。通常のデータベースの場合、サービス階層が同じであれば、セカンダリが Elastic Database プールになったり、その逆になったりすることができます。

- **セカンダリ データベースの構成可能なパフォーマンス レベル**: セカンダリ データベースは、プライマリよりも下位のパフォーマンス レベルで作成することができます。プライマリとセカンダリ、両方のデータベースが同じサービス階層を持つ必要があります。このオプションは、データベース書き込みアクティビティが高いアプリケーションにはお勧めできません。結果的にレプリケーション遅延が増大する可能性があり、したがって、フェールオーバー後に深刻なデータ損失のリスクが高くなるためです。さらに、フェールオーバー後に、新しいプライマリがより高いパフォーマンス レベルにアップグレードされるまで、アプリケーションのパフォーマンスに影響が生じます。Azure ポータル上のログ IO の割合グラフを使用すると、レプリケーションの負荷を維持するために必要なセカンダリの最小パフォーマンス レベルを適切に見積もれます。たとえば、プライマリ データベースが P6 (1000 DTU) で、ログ IO の割合が 50% の場合、セカンダリは P4 (500 DTU) 以上である必要があります。ログ IO データは、[sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) または [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) データベース ビューを使用しても取得できます。SQL Database のパフォーマンス レベルの詳細については、[SQL Database のオプションとパフォーマンス](sql-database-service-tiers.md)に関するページをご覧ください。

- **ユーザー制御のフェールオーバーとフェールバック**: アプリケーションまたはユーザーの明示的な操作によって、セカンダリ データベースをプライマリ ロールにいつでも切り替えることができます。実際のシステム停止時には、「計画されていない」 オプションを使用して、セカンダリをプライマリにすぐに昇格させる必要があります。障害が発生したプライマリが回復して、再び使用可能になると、システムは自動的にそれをセカンダリとしてマークし、新しいプライマリを使用して最新の状態にします。レプリケーションは非同期であるため、最新の変更をセカンダリにレプリケートする前に、プライマリに障害が発生した場合、計画されていないフェールオーバー時に少量のデータの失われる可能性があります。複数のセカンダリを持つプライマリがフェールオーバーすると、システムは自動的にレプリケーション関係を再構成して、いかなるユーザー介入も必要とすることなく、残りのセカンダリを新しく昇格されたプライマリにリンクします。フェールオーバーの原因となった障害が解決されたら、アプリケーションをプライマリ リージョンに復帰させることが望ましい場合があります。そうするには、[計画] オプションを使用してフェールオーバー コマンドを呼び出す必要があります。

- **資格情報とファイアウォール規則の同期を保つ**: geo レプリケートされたデータベースには、[データベースのファイアウォール規則](sql-database-firewall-configure.md)の使用をお勧めします。これらの規則がデータベースと共にレプリケートされて、すべてのセカンダリ データベースのファイアウォールの構成がプライマリと同じになるようにするためです。これにより、プライマリとセカンダリ データベースの両方をホストするサーバー上で、顧客がファイアウォール規則を手動で構成、管理する必要性がなくなります。同様に、データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使用することにより、プライマリとセカンダリの両方のデータベースが、確実かつ常に同じユーザー資格情報を持つようにして、フェールオーバーが発生した場合に、ログインとパスワードの不一致により中断が発生しないようにできます。[Azure Active Directory](../active-directory/active-directory-whatis.md) の顧客を追加すると、プライマリおよびセカンダリ データベースへのユーザー アクセスを管理でき、データベース内で資格情報を管理する必要が完全になくなります。

## 重要なデータの損失の防止
ワイド エリア ネットワークの遅延は大きいため、連続コピーでは非同期のレプリケーション メカニズムが使用されます。そのため、障害が発生した場合、部分的なデータ損失が避けられません。しかし、データ損失が許されないアプリケーションもあります。重要な更新情報を保護するには、アプリケーション開発者は、トランザクションがコミットされた直後に [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) ステム プロシージャを呼び出すことができます。**sp\_wait\_for\_database\_copy\_sync** を呼び出すと、最後にコミットされたトランザクションがセカンダリ データベースにレプリケートされるまで、呼び出しスレッドがブロックされます。このプロシージャは、キューに登録されたすべてのトランザクションがセカンダリ データベースで確認されるまで待機します。**sp\_wait\_for\_database\_copy\_sync** の対象は、特定の連続コピー リンクです。プライマリ データベースへの接続権限を持つユーザーが、このプロシージャを呼び出すことができます。

>[AZURE.NOTE] **sp\_wait\_for\_database\_copy\_sync** プロシージャを呼び出すと、大きな遅延が発生する場合があります。遅延は、その時点のトランザクション ログの長さのサイズに依存し、ログ全体がレプリケートされるまでは復帰しません。どうしても必要な場合を除き、このプロシージャを呼び出さないようにしてください。

## アクティブ geo レプリケーションのプログラムでの管理

前に説明したように、アクティブ geo レプリケーションの管理は、Azure ポータルのほかに、Azure PowerShell および REST API を使用してプログラムで実行できます。以下の表では、使用できるコマンド セットについて説明します。

- **Azure Resource Manager API とロール ベース セキュリティ**: アクティブ geo レプリケーションには、管理のための [Azure Resource Manager (ARM) API](https://msdn.microsoft.com/library/azure/mt163571.aspx) 一式 ([ARM ベースの PowerShell コマンドレット](sql-database-geo-replication-powershell.md)など) が含まれています。これらの API は、リソース グループの使用を必要とし、ロール ベース セキュリティ (RBAC) をサポートします。アクセス ロールの実装方法の詳細については、[Azure のロール ベースのアクセス制御](../active-directory/role-based-access-control-configure.md)に関するページをご覧ください。

>[AZURE.NOTE] アクティブ geo レプリケーションの多くの新機能は [Azure Resource Manager (ARM)](../resource-group-overview.md) ベースの [Azure SQL REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) と [Azure SQL Database PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)を使用する場合のみサポートされます。既存の [Azure SQL のサービス管理 (クラシック) REST API](https://msdn.microsoft.com/library/azure/dn505719.aspx) と [Azure SQL Database (クラシック) コマンドレット](https://msdn.microsoft.com/library/azure/dn546723.aspx)では、下位互換性がサポートされているため、ARM ベースの API の使用が推奨されています。


### Transact-SQL

|コマンド|説明|
|-------|-----------|
|[ALTER DATABASE (Azure SQL Database)]https://msdn.microsoft.com/ja-JP/library/mt574871.aspx)|ADD SECONDARY ON SERVER 引数を使用して、既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。|
|[ALTER DATABASE (Azure SQL Database)]https://msdn.microsoft.com/ja-JP/library/mt574871.aspx)|FAILOVER または FORCE\_FAILOVER\_ALLOW\_DATA\_LOSS を使用して、セカンダリ データベースをプライマリに切り替え、フェールオーバーを開始します。
|[ALTER DATABASE (Azure SQL Database)]https://msdn.microsoft.com/ja-JP/library/mt574871.aspx)|REMOVE SECONDARY ON SERVER を使用して、SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。|
|[sys.geo\_replication\_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx)|Azure SQL Database 論理サーバーにある各データベースの、既存の全レプリケーション リンクの情報を返します。|
|[sys.dm\_geo\_replication\_link\_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx)|最新のレプリケーション時刻、最後のレプリケーションの遅延、および指定された SQL Database のレプリケーション リンクに関する他の情報を取得します。|
|[sys.dm\_operation\_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx)|レプリケーション リンクの状態を含むすべてのデータベース操作の状態が表示されます。|
|[sp\_wait\_for\_database\_copy\_sync (Azure SQL Database)](https://msdn.microsoft.com/library/dn467644.aspx)|コミットされたすべてのトランザクションがレプリケートされ、アクティブ セカンダリ データベースによって認識されるまで、アプリケーションが待機状態になります。|
||||

### PowerShell

|コマンドレット|説明|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/ja-JP/library/azure/mt603648.aspx)|1 つまたは複数のデータベースを取得します。|
|[New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。|
|[Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/ja-JP/library/mt619393.aspx)|セカンダリ データベースをプライマリに切り替えて、フェールオーバーを開始します。|
|[Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/ja-JP/library/mt603457.aspx)|SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Azure SQL Database とリソース グループまたは SQL Server 間の geo レプリケーション リンクを取得します。|
||||

### REST API

|API|説明|
|---|-----------|
|[REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|プライマリまたはセカンダリ データベースを作成、更新、または復元します。|
|[Get Create or Update Database Status](https://msdn.microsoft.com/library/azure/mt643934.aspx)|復元操作中にステータスを返します。|
|[Set Secondary Database as Primary (計画されたフェールオーバー)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|geo レプリケーション パートナーシップのセカンダリ データベースを新しいプライマリ データベースに昇格させます。|
|[Set Secondary Database as Primary (計画されていないフェールオーバー)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|セカンダリ データベースに強制的にフェールオーバーして、セカンダリをプライマリに設定します。|
|[Get Replication Links](https://msdn.microsoft.com/library/azure/mt600929.aspx)|geo レプリケーション パートナーシップで指定された SQL データベースのすべてのレプリケーション リンクを取得します。sys.geo\_replication\_links カタログ ビューで表示可能な情報を取得します。|
|[Get Replication Link](https://msdn.microsoft.com/library/azure/mt600778.aspx)|geo レプリケーション パートナーシップで指定された SQL データベースの特定のレプリケーション リンクを取得します。sys.geo\_replication\_links カタログ ビューで表示可能な情報を取得します。|
||||



## 次のステップ

- ビジネス継続性の概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「[SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」を参照してください。
- ビジネス継続性の設計および復旧シナリオについては、[継続性のシナリオ](sql-database-business-continuity-scenarios.md)に関する記事を参照してください。
- 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
- 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0629_2016-->
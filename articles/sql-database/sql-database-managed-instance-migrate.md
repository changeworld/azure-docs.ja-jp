---
title: SQL Server インスタンスを Azure SQL Database マネージ インスタンスに移行する | Microsoft Docs
description: SQL Server インスタンスを Azure SQL Database マネージ インスタンスに移行する方法を説明します。
keywords: データベースの移行, SQL Server データベースの移行, データベース移行ツール, データベースを移行する, SQL データベースを移行する
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: bonova
ms.openlocfilehash: 4546f03294ea8ab01ecb2b2777c5b92dbc5a7f4a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Azure SQL Database マネージ インスタンスへの SQL Server インスタンスの移行

この記事では、SQL Server 2005 以降のバージョンのインスタンスを Azure SQL Database マネージ インスタンス (プレビュー) に移行する方法について説明します。 

> [!NOTE]
> 単一のデータベースを単一のデータベースまたはエラスティック プールに移行するには、[Azure SQL データベースへの SQL Server データベースの移行](sql-database-cloud-migrate.md)に関する記事をご覧ください。

SQL Database マネージ インスタンスは、既存の SQL Database サービスの拡張版で、単一のデータベース、エラスティック プールと同時に 3 つ目のデプロイ オプションを提供します。  アプリケーションを再設計せずに、データベースを完全に管理された PaaS にリフトアンドシフトできるように設計されています。 SQL Database マネージ インスタンスは、オンプレミスの SQL Server プログラミング モデルとの高い互換性を備えています。また、追加設定をすることなく SQL Server の機能や SQL Server に付随するツールとサービスの大多数を利用できます。

概要レベルでは、アプリケーションの移行プロセスは次の図のようになります。

![移行プロセス](./media/sql-database-managed-instance-migration/migration-process.png)

## <a name="assess-managed-instance-compatibility"></a>マネージ インスタンスの互換性の評価

まず、マネージ インスタンスがアプリケーションのデータベース要件を満たすかどうかを判断します。 マネージ インスタンスは、オンプレミスまたは仮想マシンで SQL Server を使用する既存のアプリケーションの大部分について簡単なリフト アンド シフト移行を提供するように設計されています。 ただし、まだサポートされていない機能が必要で、回避策を実装するコストが高すぎる場合があります。 

[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) を使用して、Azure SQL Database のデータベース機能に影響を与える可能性のある互換性の問題を検出します。 DMA はまだ移行先としてマネージ インスタンスをサポートしていませんが、Azure SQL Database に対してアセスメントを実行し、レポートされた機能パリティと互換性の問題の一覧を製品ドキュメントに照らして慎重に確認することをお勧めします。 Azure SQL Database への移行の障害となっている問題の大部分は、マネージ インスタンスで除去されました。 たとえば、複数データベース間のクエリ、同じインスタンス内の複数データベース間のトランザクション、他の SQL ソースへのリンク サーバー、CLR、グローバル一時テーブル、インスタンス レベルのビュー、Service Broker などの機能は、マネージ インスタンスで使用できます。 

ただし、[Azure の SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) など、別のオプションを検討することが必要な場合があります。 次に例をいくつか示します。

- オペレーティング システムやファイル システムへの直接アクセスが必要な場合、たとえばサード パーティ製のエージェントやカスタム エージェントを SQL Server と同じ仮想マシンにインストールする場合。
- FileStream/FileTable、PolyBase、クロス インスタンス トランザクションなど、まだサポートされていない機能に対する厳密な依存関係がある場合。
- 特定バージョンの SQL Server (たとえば 2012 など) を確実に維持する必要がある場合。
- コンピューティング要件が、マネージ インスタンスがパブリック プレビューで提供する能力よりもはるかに低く (たとえば、1 つの仮想コアなど)、データベースの統合が許容可能なオプションではない場合。

## <a name="choose-app-connectivity-option"></a>アプリの接続性オプションの選択

マネージ インスタンスは仮想ネットワークに完全に含まれるため、データに究極のレベルの分離性とセキュリティが提供されます。 次の図は、完全管理型のサービスを選択するか、フロントエンド アプリケーションのホステッド モデルを選択するかにかかわらず、Azure またはハイブリッド環境でさまざまなアプリケーション トポロジを完全にデプロイするための複数のオプションを示しています。

![アプリケーション デプロイ トポロジ](./media/sql-database-managed-instance-migration/application-deployment-topologies.png)

選択したオプションのいずれでも、データの分離性の最適なレベルを保証するプライベート IP アドレスによってのみ SQL エンドポイントに接続できます。 <!--- For more information, see How to connect your application to Managed Instance.--->

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>最適なサイズに設定されたマネージ インスタンスへのデプロイ

マネージ インスタンスは、クラウドに移行する予定のオンプレミスのワークロード向けに調整されます。 ワークロードのリソースの適切なレベルの選択において高い柔軟性を発揮する新しい購入モデルが導入されます。 オンプレミスの世界では、物理コアを使用して、これらのワークロードのサイズを設定することがおそらく一般的です。 マネージ インスタンスの新しい購入モデルは仮想コア数 "vCores" に基づき、追加のストレージと IO を個別に利用できます。 仮想コア モデルは、クラウドのコンピューティング要件と現在オンプレミスで使用しているものを把握するためのシンプルな方法です。 この新しいモデルにより、クラウド内の移行先環境を適切にサイズ設定することができます。

コンピューティング リソースとストレージ リソースをデプロイ時に選択し、後でアプリケーションのダウンタイムなしで変更できます。

![マネージ インスタンスのサイズ設定](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

VNet インフラストラクチャとマネージ インスタンスを作成する方法と、バックアップ ファイルからデータベースを復元する方法については、[マネージ インスタンスの作成](sql-database-managed-instance-tutorial-portal.md)に関する記事をご覧ください。

> [!IMPORTANT]
> 移行先の VNet とサブネットが常に[マネージ インスタンス VNET 要件](sql-database-managed-instance-vnet-configuration.md#requirements)に従っているようにすることが重要です。 非互換性があると、新しいインスタンスの作成や、既に作成したインスタンスの使用ができなくなることがあります。

## <a name="select-migration-method-and-migrate"></a>移行方法の選択と移行

マネージ インスタンスのターゲットは、オンプレミスまたは IaaS データベース実装からのデータベースの一括移行を必要とするユーザー シナリオです。 これらは、インスタンス レベルの機能や複数データベース間の機能を定期的に使用するアプリケーションのバックエンドのリフト アンド シフトが必要な場合に最適な選択肢です。 このシナリオに該当する場合は、アプリケーションを再設計する必要なしに Azure 内の対応する環境にインスタンスを移行できます。 

SQL インスタンスを移行するには、以下を慎重に計画する必要があります。

-   併置する必要のある (同じインスタンスで実行されている) すべてのデータベースの移行
-   ログイン、資格情報、SQL エージェント ジョブと演算子、サーバー レベルのトリガーなど、アプリケーションが依存するインスタンス レベルのオブジェクトの移行。 

マネージ インスタンスは、完全管理型のサービスであり、通常の DBA アクティビティの一部をそれらが組み込まれたプラットフォームにデリゲートできるようにします。 したがって、[高可用性](sql-database-high-availability.md)が組み込まれているため、定期的なバックアップや Always On 構成のメンテナンス ジョブなど、いくつかのインスタンス レベルのデータは移行する必要がありません。

マネージ インスタンスでは、次のデータベース移行オプションがサポートされます (現在は、これらの移行方法のみがサポートされます)。

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、完全管理型のサービスです。 このサービスは、Azure に既存のサード パーティ製データベースと SQL Server データベースを移行するために必要なタスクを合理化します。 パブリック プレビューのデプロイ オプションには、Azure Virtual Machine に Azure SQL Database、マネージ インスタンス、SQL Server が含まれます。 DMS は、エンタープライズ ワークロードの移行に推奨される方法です。 

![DMS](./media/sql-database-managed-instance-migration/dms.png)

このシナリオと DMS での構成手順について詳しくは、[DMS を使用したオンプレミスのデータベースのマネージ インスタンスへの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関する記事をご覧ください。  

### <a name="native-restore-from-url"></a>URL からのネイティブ復元

[Azure Storage](https://azure.microsoft.com/services/storage/) で使用可能な、オンプレミスの SQL Server または [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) から取得されたネイティブ バックアップ (.bak ファイル) の復元は、SQL DB マネージ インスタンスの主要機能の 1 つで、オフラインのデータベース移行を迅速かつ簡単に行うことができます。 

次の図では、概要レベルのプロセスについて説明します。

![移行フロー](./media/sql-database-managed-instance-migration/migration-flow.png)

次の表は、実行しているソース SQL Server のバージョンに応じて使用できる方法に関する詳細情報を示しています。

|手順|SQL エンジンとバージョン|バックアップ/復元方法|
|---|---|---|
|Azure Storage へのバックアップの格納|SQL 2012 SP1 CU2 より前|Azure Storage に .bak ファイルを直接アップロードする|
||2012 SP1 CU2 - 2016|非推奨の [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 構文を使用して直接バックアップする|
||2016 以上|[WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) を使用して直接バックアップする|
|Azure Storage からマネージ インスタンスへの復元|[SAS 資格情報での URL からの復元](sql-database-managed-instance-tutorial-portal.md#restore-the-wide-world-importers-database-from-a-backup-file)|

> [!IMPORTANT]
> システム データベースの復元はサポートされていません。 (マスターまたは msdb データベースに格納されている) インスタンス レベルのオブジェクトを移行するには、それらのスクリプトを作成し、移行先のインスタンスで T-SQL スクリプトを実行することをお勧めします。

SAS 資格情報を使用したマネージ インスタンスへのデータベース バックアップの復元を含む詳しいチュートリアルについては、[マネージ インスタンスの作成](sql-database-managed-instance-tutorial-portal.md)に関する記事をご覧ください。

### <a name="migrate-using-bacpac-file"></a>BACPAC ファイルを使用した移行

Azure SQL Database とマネージ インスタンスは、BACPAC ファイル内のデータと共に元のデータベースのコピーからインポートできます。 「[BACPAC ファイルを新しい Azure SQL Database にインポートする](sql-database-import.md)」を参照してください。

## <a name="monitor-applications"></a>アプリケーションの監視

移行後のアプリケーションの動作とパフォーマンスを追跡します。 マネージ インスタンスでは、一部の変更は[データベース互換性レベルが変更された](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)後にのみ有効になります。 Azure SQL Database へのデータベースの移行では、ほとんどの場合に元の互換性レベルが保持されます。 移行前のユーザー データベースの互換性レベルが 100 以上の場合は、移行後も同じままになります。 アップグレードされたデータベースで、移行前のユーザー データベースの互換性レベルが 90 だった場合、互換性レベルは 100 に設定されます。これは、マネージ インスタンスでサポートされている下限の互換性レベルです。 システム データベースの互換性レベルは 140 です。

移行に伴うリスクを軽減するには、パフォーマンスの監視後にのみ、データベース互換性レベルを変更します。 「[新しい SQL Server にアップグレードするときにパフォーマンスの安定性を維持する](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)」の説明に従って、データベースの互換性レベルの変更前と変更後のワークロードのパフォーマンスに関する情報を取得するための最適なツールとしてクエリ ストアを使用します。

完全管理型のプラットフォームで、SQL Database サービスの一部として自動的に提供される利点を享受できます。 たとえば、マネージ インスタンスでバックアップを作成する必要はありません。サービスによってバックアップが自動的に実行されます。 バックアップのスケジュール設定、取得、管理について心配する必要はなくなります。 マネージ インスタンスでは、[特定の時点への復旧 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) を使って、このリテンション期間内の任意の時点に復元することができます。 パブリック プレビューでは、リテンション期間は 7 日間に固定されています。
さらに、[高可用性](sql-database-high-availability.md)が組み込まれているため、高可用性の設定について心配する必要はありません。

セキュリティを強化するために、利用可能ないくつかの機能の使用を検討してください。
- データベース レベルでの Azure Active Directory 認証
- アクティビティを監視するための監査と脅威の検出
- 機密データおよび特権データのアクセス制御 ([行レベル セキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)および[動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking))。

## <a name="next-steps"></a>次の手順

- マネージ インスタンスについては、「[What is a Managed Instance? (マネージ インスタンスとは)](sql-database-managed-instance.md)」をご覧ください
- バックアップからの復元を含むチュートリアルについては、[マネージ インスタンスの作成](sql-database-managed-instance-tutorial-portal.md)に関する記事をご覧ください。
- DMS を使用した移行を示すチュートリアルについては、[DMS を使用したオンプレミスのデータベースのマネージ インスタンスへの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関する記事をご覧ください。  

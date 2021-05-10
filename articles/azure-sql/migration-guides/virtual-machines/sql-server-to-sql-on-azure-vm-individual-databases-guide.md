---
title: 'SQL Server から Azure Virtual Machines 上の SQL Server へ: 移行ガイド'
description: このガイドでは、個々の SQL Server データベースを Azure Virtual Machines 上の SQL Server に移行する方法について説明します。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550899"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>移行ガイド:SQL Server から Azure Virtual Machines 上の SQL Server

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

このガイドでは、ユーザー データベースの "*検出*" と "*評価*" を行い、バックアップ、復元、ログ配布を使用して SQL Server から Azure Virtual Machines 上の SQL Server インスタンスに "*移行*" する方法について説明します。評価には、[Data Migration Assistant](/sql/dma/dma-overview) を利用します。

オンプレミスまたは次で実行されている SQL Server を移行できます。

- SQL Server on Virtual Machines (VMs)。
- アマゾン ウェブ サービス (AWS) EC2。
- Amazon Relational Database Service (AWS RDS)。
- Compute Engine (Google Cloud Platform (GCP))。

その他の移行戦略の詳細については、[SQL Server VM 移行の概要](sql-server-to-sql-on-azure-vm-migration-overview.md)に関するページを参照してください。 その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="移行プロセス フローを示す図。":::

## <a name="prerequisites"></a>前提条件

Azure Virtual Machines 上の SQL Server への移行には、次のリソースが必要です。

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)。
- [Azure Migrate プロジェクト](../../../migrate/create-manage-projects.md)。
- [Azure Virtual Machines 上のターゲット SQL Server](../../virtual-machines/windows/create-sql-vm-portal.md) の準備 (バージョンは SQL Server ソースと同じかそれ以上)。
- [Azure とオンプレミスの間の接続](/azure/architecture/reference-architectures/hybrid-networking)。
- [適切な移行戦略の選択](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)。

## <a name="pre-migration"></a>移行前

移行を開始する前に、自分の SQL 環境のトポロジを検出し、目的とする移行の実現可能性を評価する必要があります。

### <a name="discover"></a>発見

Azure Migrate では、オンプレミス コンピューターの移行適合性を評価し、パフォーマンスに基づくサイズ設定を行い、オンプレミスでの実行にかかるコストを見積もります。 移行の計画を立てるために、Azure Migrate を使って、自分の SQL Server インスタンスで使用される[既存のデータ ソースと機能の詳細を特定](../../../migrate/concepts-assessment-calculation.md)します。 このプロセスには、ネットワークをスキャンし、組織内のすべての SQL Server インスタンス、さらにバージョンと使用中の機能を特定することが含まれます。

> [!IMPORTANT]
> SQL Server インスタンスのターゲット Azure 仮想マシンを選択する際は、[Azure Virtual Machines 上の SQL Server のパフォーマンス ガイドライン](../../virtual-machines/windows/performance-guidelines-best-practices.md)を考慮するようにしてください。

その他の検出ツールについては、「データ移行のシナリオで利用できる[サービスとツール](../../../dms/dms-tools-matrix.md#business-justification-phase)」を参照してください。

### <a name="assess"></a>アクセス

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

データ ソースをすべて検出した後、[Data Migration Assistant](/sql/dma/dma-overview) を使用して、オンプレミスの SQL Server インスタンスから Azure Virtual Machines 上の SQL Server インスタンスへの移行を評価します。これにより、ソースとターゲットのインスタンス間のギャップを把握できます。

> [!NOTE]
> SQL Server のバージョンをアップグレード "_しない_" 場合は、この手順をスキップして「[移行](#migrate)」セクションに進みます。

#### <a name="assess-user-databases"></a>ユーザー データベースへのアクセス

Data Migration Assistant は、新しいバージョンの SQL Server のデータベース機能に影響するおそれがある互換性の問題を検出することで、最新のデータ プラットフォームへの移行を支援します。 Data Migration Assistant では、ターゲット環境のパフォーマンスと信頼性を高めるための推奨事項が得られるほか、ソース サーバーからターゲット サーバーにスキーマ、データ、ログイン オブジェクトを移動できます。

詳細については、[評価](/sql/dma/dma-migrateonpremsql)に関するページを参照してください。

> [!IMPORTANT]
>評価の種類によっては、ソース SQL Server で必要となるアクセス許可が異なる場合があります。
   > - "*機能パリティ*" アドバイザーの場合、ソース SQL Server データベースに接続するために提供される資格情報は、*sysadmin* サーバー ロールのメンバーである必要があります。
   > - "*互換性の問題*" アドバイザーの場合、提供される資格情報は、少なくとも `CONNECT SQL`、`VIEW SERVER STATE`、`VIEW ANY DEFINITION` のアクセス許可を備えている必要があります。
   > - 評価を実行する前に、選択したアドバイザーに必要なアクセス許可が Data Migration Assistant によって強調表示されます。

#### <a name="assess-the-applications"></a>アプリケーションを評価する

通常、データの保持と変更のためにユーザー データベースにアクセスするのはアプリケーション レイヤーです。 Data Migration Assistant は、アプリケーションのデータ アクセス レイヤーに 2 つの方法でアクセスします。

- ユーザー データベースの[拡張イベント](/sql/relational-databases/extended-events/extended-events)または [SQL Server Profiler トレース](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler)をキャプチャして使用する。 また、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) を使用して、A/B テストにも使用できるトレース ログを作成することができます。
- [Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) を使用する。これは、コード内の SQL クエリの検出と評価に役立ちます。また、一方から他方のデータベース プラットフォームにアプリケーション ソース コードを移行するために使用されます。 このツールは、C#、Java、XML、プレーン テキストなどの一般的なファイルの種類をサポートしています。 Data Access Migration Toolkit の評価を実行する方法については、[Use Data Migration Assistant](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) のブログ投稿を参照してください。

ユーザー データベースの評価中に、Data Migration Assistant を使用して、キャプチャされたトレース ファイルまたは Data Access Migration Toolkit ファイルを[インポート](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess)します。

#### <a name="assessments-at-scale"></a>大規模な評価

Data Migration Assistant の評価が必要なサーバーが複数存在する場合は、[コマンド ライン インターフェイス](/sql/dma/dma-commandline)を使用してプロセスを自動化できます。 インターフェイスを使用して、移行の対象となる SQL Server インスタンスごとに評価コマンドを事前に準備できます。

大規模な資産全体の概要をレポートする場合は、Data Migration Assistant の評価を [Azure Migrate に統合](/sql/dma/dma-assess-sql-data-estate-to-sqldb)できるようになっています。

#### <a name="refactor-databases-with-data-migration-assistant"></a>Data Migration Assistant を使用してデータベースをリファクタリングする

Data Migration Assistant の評価の結果に応じて、移行後にユーザー データベースが正常に動作して機能するようにするための一連の推奨事項があります。 Data Migration Assistant から、影響を受けるオブジェクトの詳細と、それぞれの問題を解決する方法のリソースが示されます。 実稼働環境への移行を開始する前に、すべての破壊的変更と動作変更を解決しておくことをお勧めします。

これらの変更を回避して移行を高速化したい場合には、非推奨の機能について、本来の[互換性](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)モードでユーザー データベースを実行することを選択できます。 この操作により、非推奨の項目が解決されるまで[データベース互換性をアップグレード](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades)できなくなります。

すべての Data Migration Assistant 修正プログラムをスクリプト化し、[移行後](#post-migration)の段階でターゲットの SQL Server データベースに適用する必要があります。

> [!CAUTION]
> すべての SQL Server バージョンで互換性モードがサポートされているわけではありません。 ご自分の[ターゲット SQL Server バージョン](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)で、選択したデータベース互換性がサポートされていることをご確認ください。 たとえば、SQL Server 2019 では、互換性レベルが 90 (SQL Server 2005) のデータベースはサポートされていません。 これらのデータベースでは、少なくとも互換性レベル 100 へのアップグレードが必要です。
>

## <a name="migrate"></a>移行

移行前の手順を完了したら、ユーザー データベースとコンポーネントを移行する準備が整います。 お望みの[移行方法](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)を使用して、データベースを移行します。

以下のセクションでは、バックアップと復元を使用した移行、またはログ配布と共にバックアップと復元を使用した最小限のダウンタイムでの移行を実行する手順について説明します。

### <a name="backup-and-restore"></a>バックアップと復元

バックアップと復元を使用した標準の移行を実行するには:

1. 要件に基づいて、Azure Virtual Machines 上の SQL Server への接続を設定します。 詳細については、[Azure 上にある SQL Server 仮想マシンへの接続 (リソース マネージャー)](../../virtual-machines/windows/ways-to-connect-to-sql.md) に関する記事を参照してください。
1. 移行の対象となるデータベースを使用しているアプリケーションを一時停止または停止します。
1. [シングル ユーザー モード](/sql/relational-databases/databases/set-a-database-to-single-user-mode)を使用して、ユーザー データベースが非アクティブであることを確認します。
1. オンプレミスの場所へのデータベースの完全バックアップを実行します。
1. リモート デスクトップ、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview)、または [AzCopy コマンド ライン ユーティリティ](../../../storage/common/storage-use-azcopy-v10.md)を使用して、オンプレミスのバックアップ ファイルを自分の VM にコピーします。 (2 TB を超えるバックアップをお勧めします)。
1. データベースの完全バックアップを Azure Virtual Machines 上の SQL Server に復元します。

### <a name="log-shipping-minimize-downtime"></a>ログ配布 (ダウンタイムの最小化)

バックアップ、復元、ログ配布を使用した最小限のダウンタイムでの移行を実行するには:

1. 要件に基づいて、Azure Virtual Machines 上の SQL Server への接続を設定します。 詳細については、[Azure 上にある SQL Server 仮想マシンへの接続 (リソース マネージャー)](../../virtual-machines/windows/ways-to-connect-to-sql.md) に関する記事を参照してください。
1. 移行されるオンプレミスのユーザー データベースが、完全または一括ログ復旧モデルであることを確認します。
1. オンプレミスの保存先へのデータベースの完全バックアップを実行します。また、[COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) キーワードを使用してログ チェーンを保持するよう、データベースの完全バックアップの既存ジョブを変更します。
1. リモート デスクトップ、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview)、または [AzCopy コマンド ライン ユーティリティ](../../../storage/common/storage-use-azcopy-v10.md)を使用して、オンプレミスのバックアップ ファイルを自分の VM にコピーします。 (1 TB を超えるバックアップをお勧めします)。
1. データベースの完全バックアップを Azure Virtual Machines 上の SQL Server に復元します。
1. オンプレミス データベースと Azure Virtual Machines 上の SQL Server の間で[ログ配布](/sql/database-engine/log-shipping/configure-log-shipping-sql-server)を設定します。 このタスクは前の手順で既に完了しているため、データベースは再初期化しないでください。
1. ターゲット サーバーにカットオーバーします。
   1. 移行対象のデータベースを使用しているアプリケーションを一時停止または停止します。
   1. [シングル ユーザー モード](/sql/relational-databases/databases/set-a-database-to-single-user-mode)を使用して、ユーザー データベースが非アクティブであることを確認します。
   1. 準備ができたら、Azure Virtual Machines 上の SQL Server に対してオンプレミス データベースのログ配布の[制御されたフェールオーバー](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server)を実行します。

### <a name="migrate-objects-outside-user-databases"></a>ユーザー データベース外のオブジェクトを移行する

ユーザー データベースの移行後のシームレスな運用には、追加の SQL Server オブジェクトが必要な場合があります。

次の表では、コンポーネントと (ユーザー データベースの移行の前後に完了できる) 推奨の移行方法の一覧を示します。

| **機能** | **コンポーネント** | **移行の方法** |
| --- | --- | --- |
| **データベース** | モデル | SQL Server Management Studio でスクリプトを使用します。 |
|| TempDB | 最善のパフォーマンスが得られるように、[Azure VM 一時ディスク (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) への tempDB の移行を計画します。 自分の tempDB に合わせて、十分なローカル SSD を備えた VM サイズを選択してください。 |
|| FileStream を使用したユーザー データベース | [バックアップと復元](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore)の方法を使用して移行します。 Data Migration Assistant では、FileStream を使用したデータベースはサポートされません。 |
| **Security** | SQL Server と Windows のログイン | Data Migration Assistant を使用して、[ユーザー ログインを移行](/sql/dma/dma-migrateserverlogins)します。 |
|| SQL Server ロール | SQL Server Management Studio でスクリプトを使用します。 |
|| 暗号化プロバイダー | [Azure Key Vault の使用に切り替える](../../virtual-machines/windows/azure-key-vault-integration-configure.md)ことをお勧めします。 この手順では、[SQL VM リソース プロバイダー](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)を使用します。 |
| **サーバー オブジェクト** | バックアップ デバイス | [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md) を使用してデータベース バックアップを置換します。または、[Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) にバックアップを書き込みます (SQL Server 2012 SP1 CU2 +)。 この手順では、[SQL VM リソース プロバイダー](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)を使用します。|
|| リンク サーバー | SQL Server Management Studio でスクリプトを使用します。 |
|| サーバー トリガー | SQL Server Management Studio でスクリプトを使用します。 |
| **レプリケーション** | ローカル パブリケーション | SQL Server Management Studio でスクリプトを使用します。 |
|| ローカル サブスクライバー | SQL Server Management Studio でスクリプトを使用します。 |
| **PolyBase** | PolyBase | SQL Server Management Studio でスクリプトを使用します。 |
| **管理** | データベース メール | SQL Server Management Studio でスクリプトを使用します。 |
| **SQL Server エージェント** | ジョブ | SQL Server Management Studio でスクリプトを使用します。 |
|| 警告 | SQL Server Management Studio でスクリプトを使用します。 |
|| オペレーター | SQL Server Management Studio でスクリプトを使用します。 |
|| プロキシ | SQL Server Management Studio でスクリプトを使用します。 |
| **オペレーティング システム** | ファイル、ファイル共有 | SQL Server で使用されるその他のファイルまたはファイル共有をメモしておいて、Azure Virtual Machines ターゲットにレプリケートします。 |

## <a name="post-migration"></a>移行後

移行の段階を正常に完了したら、移行後の一連のタスクを完了して、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 このタスクを実現するには、アプリケーションの変更が必要な場合があります。

Data Migration Assistant で推奨されている修正をユーザー　データベースに適用します。 一貫性を確保し、自動化を可能にするために、これらの修正プログラムをスクリプトにする必要があります。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されます。

1. **検証テストを作成する**: データベース移行をテストするには、SQL クエリを使用する必要があります。 ソースとターゲットの両方のデータベースに対して実行する検証クエリを作成します。 その検証クエリでは、定義されているスコープに対応する必要があります。
1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行した後、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

> [!TIP]
> ターゲット SQL Server パフォーマンスを評価するには、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) を使用するのが便利です。

### <a name="optimize"></a>最適化

移行後の段階は、発生したデータの精度の問題を調整したり、完全性を検証したり、ワークロードでのパフォーマンスの潜在的な問題に対処したりするうえで非常に重要です。

それらの問題と、軽減する手順の詳細については、こちらを参照してください。

- [移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Azure SQL 仮想マシンにおけるパフォーマンスのチューニング](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Azure コストの最適化センター](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>次の手順

- SQL Server に適用されるサービスの可用性を確認するには、[Azure グローバル インフラストラクチャ センター](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)を参照してください
- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。
- Azure SQL の詳細については、以下を参照してください。
   - [デプロイ オプション](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure Virtual Machines における SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 総保有コスト (TCO) 計算ツール](https://azure.microsoft.com/pricing/tco/calculator/)

- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   - [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- ライセンスの詳細については、以下を参照してください。
   - [Azure ハイブリッド特典を使用するライセンス持ち込み](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 および SQL Server 2008 R2 の延長サポートの無料利用](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーでの A/B テストの実行方法の詳細については、「[Database Experimentation Assistant の概要](/sql/dea/database-experimentation-assistant-overview)」ページを参照してください。

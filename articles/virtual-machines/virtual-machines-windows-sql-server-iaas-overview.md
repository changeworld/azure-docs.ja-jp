<properties
	pageTitle="Virtual Machines での SQL Server の概要 | Microsoft Azure"
	description="Azure Virtual Machines 上でクラウドの SQL Server データベースの実行を開始します。このサービスとしてのインフラストラクチャ (IaaS) モデルを使用すると、Azure で SQL Server のワークロードを実行できます。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/25/2016"
	ms.author="jroth"/>

# Azure Virtual Machines における SQL Server の概要

[Azure Virtual Machines で実行されている SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) を使用すると、クラウドに SQL Server データベースをホストすることができます。たとえば、Windows Server 2012 R2 および SQL Server 2014 Enterprise Edition を使用して構成済みの Azure VM に、オンプレミスのデータベースを移行できます。ただし、高可用性やオンプレミスのネットワークと接続するハイブリッド アーキテクチャをサポートする複数のコンピューターの構成など、他にも多くのシナリオの可能性があります。

概要については、次のビデオをご覧ください。

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## SQL サービス

Azure VM で SQL Server を実行することは、リレーショナル データを Azure に格納するための 1 つのオプションです。さまざまなオファリングを以下の表にまとめています。

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| SQL サービス | 説明 |
|---:|---|---|
|![Azure Virtual Machines における SQL Server](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[Azure Virtual Machines における SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Azure Virtual Machines で SQL Server を実行します。直接仮想マシンを管理し、製品版の SQL Server でデータベースを実行します。 |
|![SQL Database](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[SQL Database](https://azure.microsoft.com/services/sql-database/)|SQL Database サービスを使用して、基になるインフラストラクチャを管理することなく、データベースにアクセスしてスケールアップ/スケールダウンします。|
|![SQL Data Warehouse](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL Data Warehouse](https://azure.microsoft.com/ja-JP/services/sql-data-warehouse/)|Azure SQL Data Warehouse を使用して、大量のリレーショナルおよび非リレーショナル データを処理します。サービスとしてスケーラブルなデータ ウェアハウジング機能を提供します。|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/ja-JP/services/sql-server-stretch-database/)|オンプレミスのトランザクション データを Microsoft SQL Server 2016 から Azure に動的にストレッチします。|

>[AZURE.NOTE] SQL VM と SQL Database の詳細な比較については、「[クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)」を参照してください。

## SQL Server VM のデプロイ

Azure で SQL Server 仮想マシンを作成するには、最初に Azure プラットフォーム サブスクリプションを入手する必要があります。Azure サブスクリプションは、複数の[購入オプション](https://azure.microsoft.com/pricing/purchase-options/)で購入できます。無料で試用するには、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)にアクセスしてください。

サブスクリプションにサインアップした後、Azure で SQL Server 仮想マシンをデプロイする最も簡単な方法は、[Azure で SQL Server マシンのギャラリー イメージをプロビジョニングする](virtual-machines-windows-portal-sql-server-provision.md)方法です。それらのイメージには、VM の価格での SQL Server のライセンスが含まれます。

Azure Virtual Machines の作成と管理には、クラシックと Resource Manager の 2 つのモデルがあることに注意してください。最新のデプロイでは、Resource Manager モデルを使用することをお勧めします。詳細については、「[リソース マネージャー デプロイと従来のデプロイを理解する](../resource-manager-deployment-model.md)」を参照してください。この記事のように、クラシックと Resource Manager の両方に適用されない限り、トピックごとに対象のモデルが明示されます。

## SQL VM イメージの選択
次の表には、仮想マシン ギャラリーで使用可能な SQL Server イメージのマトリックスが示されています。バージョン、エディション、およびオペレーティング システムに基づいて、テーブル内のリンクのいずれかをクリックします。次に、Marketplace ページで **[仮想マシンの作成]** をクリックします。

|SQL Server のバージョン|オペレーティング システム|SQL Server のエディション|
|---|---|---|
|**SQL Server 2016 RC**|Windows Server 2012 R2|[評価](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rc3evaluationwindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/)、[Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/)、[Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/)、[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/)、[Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/)、[Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/)、[Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/)、[Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/)、[Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/)、[Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/)、[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/)、[Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/)、[Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] 既定では、カスタマー エクスペリエンス向上プログラム (CEIP) が有効です。必要に応じて、仮想マシンをプロビジョニングした後に、CEIP をカスタマイズまたは無効にすることができます。リモート デスクトップを使用して VM に接続し、**SQL Server エラーと使用状況レポート** ユーティリティを実行します。

新しい SQL VM の作成についてさらにヘルプが必要な場合は、 [プロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)に関するページで詳細なチュートリアルをご覧ください。

これらの事前構成されたイメージのほかに、SQL Server がプレインストールされていない状態で [Azure 仮想マシンを作成する](virtual-machines-windows-hero-tutorial.md)こともできます。ライセンスのある SQL Server のインスタンスであればインストールできます。「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」の説明に従って、Azure 仮想マシンで SQL Server を実行するために、Azure にライセンスを移行します。このシナリオでは、仮想マシンに関連付けられた Azure Compute および Storage [コスト](https://azure.microsoft.com/pricing/details/virtual-machines/)のみを支払います。

SQL Server イメージに最適な仮想マシン構成の設定を決めるには、[SQL Server VM のパフォーマンスのベスト プラクティス](virtual-machines-windows-sql-performance.md)に関するページをご覧ください。運用環境のワークロードの場合、SQL Server Enterprise Edition の使用時に推奨される仮想マシンの最小サイズは **DS3** です。Standard Edition での運用環境のワークロードに推奨される仮想マシンの最小サイズは **DS2** です。

## データの移行

SQL Server 仮想マシンを起動した後、マシンに既存のデータベースを移行できます。移行オプションの一覧とガイダンスについては、[Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)に関するページをご覧ください。

## 高可用性

高可用性が必要な場合は、SQL Server 高可用性グループの構成を検討してください。そのために、1 つの仮想ネットワークで複数の Azure VM を使用します。Azure ポータルには、この構成が事前に設定されたテンプレートがあります。詳細については、[Azure Resource Manager 仮想マシンでの AlwaysOn 可用性グループの構成](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)に関するページをご覧ください。

可用性グループおよび関連付けられているリスナーを手動で構成する場合は、[Azure VM での AlwaysOn 可用性グループの構成](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)に関するページをご覧ください。

その他の高可用性構成については、「[Azure の仮想マシン内の SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。

## バックアップと復元
オンプレミス データベースを使う場合は、SQL Server のバックアップ ファイルを格納するためのセカンダリ データ センターとして Azure を利用できます。バックアップと復元のオプションの概要については、「[Azure Virtual Machines における SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」をご覧ください。

[SQL Server Backup to URL](https://msdn.microsoft.com/library/dn435916.aspx) を実行すると、Azure のバックアップ ファイルが Azure BLOB ストレージに格納されます。[SQL Server マネージ バックアップ](https://msdn.microsoft.com/library/dn449496.aspx)を実行すると、Azure でバックアップのスケジュールと保持期間を設定できます。これらのサービスは、オンプレミスの SQL Server インスタンスか、Azure VM で実行されている SQL Server で使用できます。Azure VM は、SQL Server の[自動バックアップ](virtual-machines-windows-classic-sql-automated-backup.md)や[修正プログラムの自動適用](virtual-machines-windows-classic-sql-automated-patching.md)を活用することもできます。

## 次のステップ

まず、[Azure ポータルで独自の SQL Server VM を作成します](virtual-machines-windows-portal-sql-server-provision.md)。

次に、[パフォーマンスのベスト プラクティス](virtual-machines-windows-sql-performance.md)と[移行手法](virtual-machines-windows-migrate-sql.md)を確認して、SQL Server のワークロードを Azure VM に移動することを検討します。

Azure 仮想マシン上の SQL Server についてご不明な点がある場合は、 まず、「[Azure Virtual Machines における SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)」をご確認ください。SQL VM に関するトピックのいずれかで、下部のセクションに質問またはコメントを追加して、Microsoft やコミュニティとやり取りすることもできます。

<!---HONumber=AcomDC_0525_2016-->
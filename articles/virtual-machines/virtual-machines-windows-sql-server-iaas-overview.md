<properties
	pageTitle="Azure 仮想マシンで SQL Server を視界する | Microsoft Azure"
	description="Azure 仮想マシンでオンプレミスの SQL Server データベースのワークロードをクラウドに移動します。事前に構成された SQL VM イメージを利用して、すぐに開始できます。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="06/03/2016"
	ms.author="jroth"/>

# Azure 仮想マシンで SQL Server を使用する

このトピックでは、SQL Server で Azure 仮想マシンを実行するためのオプションについて説明し、作業を開始するためのガイダンスとリソースを紹介します。

このトピックの読者は、オンプレミスの SQL Server ワークロードをクラウドに移行することに関心があるデータベース管理者の場合もあれば、Azure アプリケーション用に SQL Server のリレーショナル データベース機能を検討している開発者の場合もあります。Azure 仮想マシンで SQL Server のワークロードを実行する際の利点は何でしょうか。 次の概要ビデオでは、その利点について説明し、技術的な概要を示しています。

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## 利点の評価

始める前に、まず、Azure VM で SQL Server を使用することによる利点を評価します。

企業アプリケーションなど、他のワークロードを Azure に移動する場合は、パフォーマンスを向上させるために、依存する SQL Server データベースも Azure に移動することが理にかなっています。しかし、Azure VM で SQL Server をホストすると、その他にも利点があります。たとえば、グローバルな展開と障害復旧のために、自動的に複数のデータ センターにアクセスできるようになります。シナリオと利点の詳しい一覧については、[Azure VM 上の SQL Server の製品ページ](https://azure.microsoft.com/services/virtual-machines/sql-server/)を参照してください。

> [AZURE.NOTE] Azure VM 上の SQL Server を評価する際に、Azure の他のストレージや SQL オプションも確認してください。たとえば、[SQL Database](../sql-database/sql-database-technical-overview.md)、[SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)、[SQL Server Stretch Databsae](../sql-server-stretch-database/sql-server-stretch-database-overview.md) などです。1 つの詳細な比較として、「[クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)」を参照してください。

Azure VM で SQL Server を実行することを決めたら、まず、SQL Server ライセンス費用が含まれている VM イメージを使用するかどうかを決定します。ライセンス持ち込み (BYOL) により VM 自体の料金のみを支払う方法もあります。次の 2 つのセクションでは、これらのオプションについて説明します。

## オプション 1: SQL VM のデプロイ (分単位のライセンス)
次の表には、仮想マシン ギャラリーで使用可能な SQL Server イメージのマトリックスが示されています。指定したバージョン、エディション、およびオペレーティング システムで新しい SQL VM の作成を開始するには、いずれかのリンクをクリックします。すべてのイメージには、[SQL Server のライセンス費用](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)が含まれています。

詳細な手順は、チュートリアルの「[Azure ポータルでの SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」に記載されています。また、[SQL Server VM のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)の記事も参照してください。ここでは、適切なマシンのサイズと、プロビジョニング中に使用できるその他の機能の選択方法を説明しています。

|SQL Server のバージョン|オペレーティング システム|SQL Server のエディション|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL Server 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## オプション 2: SQL VM のデプロイ (BYOL)
もう 1 つのオプションは、ライセンス持ち込み (BYOL) です。このシナリオでは、SQL Server ライセンスに対する追加料金はなく、VM のみに対して料金を支払います。所有するライセンスを使用するには、以下の SQL Server のバージョン、エディション、およびオペレーティング システムのマトリックスを使用します。ポータルでは、イメージ名に **{BYOL}** というプレフィックスが付きます。

> [AZURE.IMPORTANT] BYOL VM イメージを使用するには、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)を持つエンタープライズ契約が必要です。使用する SQL Server のバージョン/エディションに有効なライセンスも必要です。VM のプロビジョニングから **10** 日以内に、[必要な BYOL 情報を Microsoft に提供する](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf)必要があります。

[プロビジョニング チュートリアル](virtual-machines-windows-portal-sql-server-provision.md)内のガイダンスが適用されますが、以下の **BYOL** イメージ オプションのいずれかを使用する必要があります。また、[SQL Server VM のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)の記事も参照してください。ここでは、適切なマシンのサイズと、プロビジョニング中に使用できるその他の機能の選択方法を説明しています。

|SQL Server のバージョン|オペレーティング システム|SQL Server のエディション|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## SQL VM の管理
SQL Server VM をプロビジョニングした後、オプションの管理タスクがいくつかあります。一部の側面では、SQL Server の構成と管理は、オンプレミスの場合とまったく同じです。しかし、一部のタスクは Azure に固有です。以降のセクションでは、それらの領域の一部について説明し、詳細情報へのリンクを付記します。

### データの移行

既存のデータベースがある場合は、新たにプロビジョニングした SQL VM にそれを移動することがあります。移行オプションの一覧とガイダンスについては、[Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)に関するページを参照してください。

### 高可用性の構成

高可用性が必要な場合は、SQL Server 高可用性グループの構成を検討してください。そのために、1 つの仮想ネットワークで複数の Azure VM を使用します。Azure ポータルには、この構成が事前に設定されたテンプレートがあります。詳細については、[Azure Resource Manager 仮想マシンでの AlwaysOn 可用性グループの構成](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)に関するページを参照してください。可用性グループおよび関連付けられているリスナーを手動で構成する場合は、[Azure VM での AlwaysOn 可用性グループの構成](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)に関するページを参照してください。

その他の高可用性構成については、「[Azure の仮想マシン内の SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。

### データのバックアップ
Blob Storage を使用して、データベースのバックアップを Azure に直接格納できます。詳細については、「[Azure Storage を使用した SQL Server のバックアップと復元](../sql-database/storage-use-storage-sql-server-backup-restore.md)」を参照してください。これは SQL VM に使うと効果的ですが、オンプレミスの SQL Server データベースにも使用できます。バックアップと復元のオプションの概要については、「[Azure Virtual Machines における SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」をご覧ください。

Azure VM は、SQL Server の[自動バックアップ](virtual-machines-windows-sql-automated-backup.md)や[修正プログラムの自動適用](virtual-machines-windows-sql-automated-patching.md)を活用することもできます。

### カスタマー エクスペリエンス向上プログラム (CEIP)
既定では、カスタマー エクスペリエンス向上プログラム (CEIP) が有効です。これは、プロビジョニング後に CEIP を無効にする場合を除き、管理タスクではありません。リモート デスクトップで VM に接続することで、CEIP をカスタマイズしたり無効にしたりすることができます。その後、**SQL Server エラーと使用状況レポート** ユーティリティを実行します。レポートを無効にするには、指示に従います。

## 次のステップ
Azure 仮想マシン上の SQL Server の[ラーニング パスを調べます](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)。

質問がある場合は、 まず、「[Azure Virtual Machines における SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)」をご確認ください。SQL VM に関するトピックのいずれかで、下部のセクションに質問またはコメントを追加して、Microsoft やコミュニティとやり取りすることもできます。

<!---HONumber=AcomDC_0608_2016-->
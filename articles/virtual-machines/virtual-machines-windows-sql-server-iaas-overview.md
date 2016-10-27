<properties
    pageTitle="Azure Virtual Machines での SQL Server の概要 | Microsoft Azure"
    description="Azure Virtual Machines で各 SQL Server エディションを実行する方法について説明します。 すべての SQL Server VM イメージと関連コンテンツへのダイレクト リンクが記載されています。"
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
    ms.date="10/11/2016"
    ms.author="jroth"/>


# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines における SQL Server の概要

このトピックでは、[ポータル イメージへのリンク](#option-1-create-a-sql-vm-with-per-minute-licensing)および[一般的なタスク](#manage-your-sql-vm)の概要と共に、Azure 仮想マシン (VM) で SQL Server を実行するための選択肢について説明します。

>[AZURE.NOTE] 既に SQL Server を使い慣れており、SQL Server VM のデプロイ方法を確認するだけの場合は、「[Azure Portal での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。

## <a name="overview"></a>Overview
データベースの管理者または開発者の場合、Azure VM には、オンプレミスの SQL Server ワークロードとアプリケーションをクラウドに移動する方法が用意されています。 次のビデオでは、SQL Server Azure VM の技術的な概要を紹介します。

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

ビデオでは次の分野を取り上げています。

|Time|領域|
|---|---|
| 00:21 | Azure VM とは |
| 01:45 | セキュリティ |
| 02:50 | 接続 |
| 03:30 | ストレージの信頼性とパフォーマンス |
| 05:20 | VM サイズ |
| 05:54 | 高可用性と SLA |
| 07:30 | 構成のサポート |
| 08:00 | 監視 |
| 08:32 | デモ: SQL Server 2016 VM の作成 |

>[AZURE.NOTE] このビデオでは、SQL Server 2016 を取り上げていますが、Azure には、SQL Server の多くのバージョン (2008、2012、2014、2016) の VM イメージが用意されています。 

## <a name="understand-your-options"></a>オプションについて
データを Azure でホストするにはさまざまな理由があります。 アプリケーションが Azure に移動している場合、データの移動でもパフォーマンスが向上します。 ただし、ほかにも利点があります。 グローバルな展開と障害復旧のために、自動的に複数のデータ センターにアクセスできるようになります。 データは、高度なセキュリティで保護され、耐久性に優れています。

Azure VM で実行されている SQL Server は、リレーショナル データを Azure に格納するための 1 つのオプションです。 次の表に、Azure における SQL サービスの簡単な概要を示します。

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| SQL サービス | Description |
|---:|---|---|
|![Azure Virtual Machines における SQL Server](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[Azure Virtual Machines における SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Azure Virtual Machines で SQL Server を実行します (このトピックの中心)。 直接仮想マシンを管理し、製品版の SQL Server でデータベースを実行します。 |
|![SQL Database](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[SQL Database](https://azure.microsoft.com/services/sql-database/)|SQL Database サービスを使用して、基になるインフラストラクチャを管理することなく、データベースにアクセスしてスケールアップ/スケールダウンします。|
|![SQL Data Warehouse](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL Data Warehouse](https://azure.microsoft.com/en-us/services/sql-data-warehouse/)|Azure SQL Data Warehouse を使用して、大量のリレーショナルおよび非リレーショナル データを処理します。 サービスとしてスケーラブルなデータ ウェアハウジング機能を提供します。|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/en-us/services/sql-server-stretch-database/)|オンプレミスのトランザクション データを Microsoft SQL Server 2016 から Azure に動的にストレッチします。|

このようにさまざまなオプションがあるため、Azure VM で実行されている SQL Server は、複数のシナリオにお勧めします。 たとえば、オンプレミスの SQL Server マシンとできるだけ同じように、Azure VM を構成することができます。 また、同じデータベース サーバー上で追加のアプリケーションとサービスを実行することもできます。 より多くの決定要因を検討するのに役立つ 2 つのリソースがあります。

 - [Azure 仮想マシン上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) に関するページでは、Azure VM で SQL Server を使用するのに最適なシナリオの概要を示します。 
 - 「[クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](../sql-database/sql-database-paas-vs-sql-server-iaas.md)」では、VM で実行されている SQL Database と SQL Server の詳細な比較を示します。

## <a name="create-a-new-sql-vm"></a>新しい SQL VM の作成
以下のセクションでは、SQL Server 仮想マシン ギャラリー イメージに関する、Azure Portal へのリンクを記載しています。 選択するイメージによっては、SQL Server のライセンス費用を支払って分単位の従量課金で利用するか、ライセンス持ち込み (BYOL) を利用するかを選択できます。

このプロセスの詳細な手順については、チュートリアル「[Azure Portal での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」に記載されています。 また、[SQL Server VM のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)の記事も参照してください。ここでは、適切なマシンのサイズと、プロビジョニング中に使用できるその他の機能の選択方法を説明しています。

## <a name="option-1:-create-a-sql-vm-with-per-minute-licensing"></a>オプション 1: 分単位のライセンスを利用した SQL VM の作成
次の表には、仮想マシン ギャラリーで使用可能な SQL Server イメージのマトリックスが示されています。 指定したバージョン、エディション、およびオペレーティング システムで新しい SQL VM の作成を開始するには、いずれかのリンクをクリックします。

|バージョン|オペレーティング システム|エディション|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2)、[Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2:-create-a-sql-vm-with-an-existing-license"></a>オプション 2: 既存のライセンスを利用した SQL VM の作成
ライセンス持ち込み (BYOL) を利用することもできます。 このシナリオでは、SQL Server ライセンスに対する追加料金はなく、VM のみに対して料金を支払います。 所有するライセンスを使用するには、以下の SQL Server のバージョン、エディション、およびオペレーティング システムのマトリックスを使用します。 ポータルでは、これらのイメージ名に **{BYOL}**というプレフィックスが付きます。

|バージョン|オペレーティング システム|エディション|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] BYOL VM イメージを使用するには、 [Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)を伴うエンタープライズ契約が必要です。 使用する SQL Server のバージョン/エディションに有効なライセンスも必要です。 VM のプロビジョニングから [10](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) 日以内に、 **必要な BYOL 情報を Microsoft に提供する** 必要があります。

## <a name="manage-your-sql-vm"></a>SQL VM の管理
SQL Server VM をプロビジョニングした後、オプションの管理タスクがいくつかあります。 多くの点について、SQL Server の構成と管理は、オンプレミスの SQL Server インスタンスを管理する場合とまったく同じです。 しかし、一部のタスクは Azure に固有です。 以降のセクションでは、それらの領域の一部について説明し、詳細情報へのリンクを付記します。

### <a name="connect-to-the-vm"></a>VM に接続します
最も基本的な管理手順の 1 つとして、SQL Server Management Studio (SSMS) などのツールを使用して SQL Server VM に接続します。 新しい SQL Server VM に接続する手順については、「[Azure での SQL Server 仮想マシンへの接続](virtual-machines-windows-sql-connect.md)」を参照してください。

### <a name="migrate-your-data"></a>データの移行
既存のデータベースがある場合は、新たにプロビジョニングした SQL VM にそれを移動することがあります。 移行オプションの一覧とガイダンスについては、 [Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)に関するページを参照してください。

### <a name="configure-high-availability"></a>高可用性の構成
高可用性が必要な場合は、SQL Server 高可用性グループの構成を検討してください。 そのために、1 つの仮想ネットワークで複数の Azure VM を使用します。 Azure ポータルには、この構成が事前に設定されたテンプレートがあります。 詳細については、 [Azure Resource Manager 仮想マシンでの AlwaysOn 可用性グループの構成](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)に関するページを参照してください。 可用性グループおよび関連付けられているリスナーを手動で構成する場合は、 [Azure VM での AlwaysOn 可用性グループの構成](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)に関するページを参照してください。

その他の高可用性構成については、「 [Azure の仮想マシン内の SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。

### <a name="back-up-your-data"></a>データのバックアップ
Azure VM では[自動バックアップ](virtual-machines-windows-sql-automated-backup.md)を活用できます。これにより、データベースのバックアップが BLOB ストレージに定期的に作成されます。 また、この手法は手動で使用できます。 詳細については、「[Azure Storage を使用した SQL Server のバックアップと復元](virtual-machines-windows-use-storage-sql-server-backup-restore.md)」を参照してください。 すべてのバックアップと復元のオプションの概要については、「[Azure Virtual Machines おける SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」を参照してください。

### <a name="automate-updates"></a>更新の自動化
Azure VM では、 [自動修正](virtual-machines-windows-sql-automated-patching.md) を使用して、Windows と SQL Server の重要な更新プログラムを自動的にインストールするメンテナンス期間をスケジュール設定できます。

### <a name="customer-experience-improvement-program-(ceip)"></a>カスタマー エクスペリエンス向上プログラム (CEIP)
既定では、カスタマー エクスペリエンス向上プログラム (CEIP) が有効です。 これにより、レポートが定期的に Microsoft に送信され、SQL Server の改善に役立ちます。 プロビジョニング後に無効にする場合を除き、CEIP で必要な管理タスクはありません。 リモート デスクトップで VM に接続することで、CEIP をカスタマイズしたり無効にしたりすることができます。 その後、 **SQL Server エラーと使用状況レポート** ユーティリティを実行します。 レポートを無効にするには、指示に従います。 

詳細については、「[使用許諾契約への同意](https://msdn.microsoft.com/library/ms143343.aspx)」の CEIP に関するセクションを参照してください。 

## <a name="next-steps"></a>次のステップ
[ラーニング パスを調べます](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) 。

質問がある場合は、 まず、「[Azure Virtual Machines における SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)」をご確認ください。 SQL VM に関するトピックのいずれかで、下部のセクションに質問またはコメントを追加して、Microsoft やコミュニティとやり取りすることもできます。



<!--HONumber=Oct16_HO2-->



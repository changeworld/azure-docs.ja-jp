<properties 
	pageTitle="Azure Virtual Machines における SQL Server の概要" 
	description="この記事は、Azure IaaS Virtual Machines でホストされている SQL Server の概要を示します。この記事には詳しいコンテンツへのリンクが掲載されています。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Azure Virtual Machines における SQL Server の概要

## 概要
AlwaysOn 可用性グループと Azure Virtual Network を使用すると、1 つのデータベース サーバーや複数のコンピューターなど、さまざまな構成で、[Azure Virtual Machines に SQL Server](http://azure.microsoft.com/services/virtual-machines/sql-server/) をホストすることができます。このトピックでは、Azure 仮想マシンで SQL Server の実行を開始するための最適なリソースをいくつか紹介します。

>[AZURE.NOTE]Azure VM で SQL Server を実行することは、リレーショナル データを Azure に格納するための 1 つのオプションです。Azure SQL Database サービスを使用することもできます。詳細については、「[Azure SQL Database と Azure VM 内の SQL Server について](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)」を参照してください。
 
## 1 つの VM での SQL Server インスタンスのデプロイ

Azure で SQL Server 仮想マシンをデプロイする最も簡単な方法は、[Azure 管理ポータルで SQL Server マシンのギャラリー イメージをプロビジョニングする](virtual-machines-provision-sql-server.md)方法です。それらのイメージには、VM の価格での SQL Server のライセンスが含まれます。

ただし、SQL Server がプレインストールされていない状態で [Azure の仮想マシンを作成する](virtual-machines-windows-tutorial.md)こともできます。ライセンスのある SQL Server のインスタンスであればインストールできます。

これらのプロビジョニングと構成の初期段階での一般的なタスクは、次のとおりです。

- [Azure VM における SQL Server のパフォーマンスに関するベスト プラクティスの確認](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Azure VM における SQL Server のセキュリティに関するベスト プラクティスの確認](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [接続のセットアップ](virtual-machines-sql-server-connectivity.md)

## 複数の VM での高可用性構成のデプロイ

SQL Server AlwaysOn 可用性グループを使用すると、SQL Server の高可用性を実現できます。そのために、1 つの仮想ネットワークで複数の Azure VM を使用します。Azure プレビュー ポータルには、この構成が事前に設定されたテンプレートがあります。詳細については、[Microsoft Azure ポータル ギャラリーで提供されている SQL Server AlwaysOn テンプレート](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)に関する記事を参照してください。

可用性グループおよび関連付けられているリスナーを手動で構成する場合は、次の記事を参照してください。

- [Azure AlwaysOn 可用性グループの構成 (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Azure での AlwaysOn 可用性グループの ILB リスナーの構成](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [オンプレミスの AlwaysOn 可用性グループの Azure への拡張](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

その他の高可用性構成については、「[Azure Virtual Machines 内の SQL Server の高可用性と災害復旧](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)」を参照してください。

## Azure でのビジネス インテリジェンス、データ ウェアハウジング、OLTP ワークロードの実行   
Azure Virtual Machines で SQL Server の一般的なワークロードを実行できます。SQL Server では、ギャラリーで提供されている、最適化された仮想マシン イメージをいくつか使用することができます。次のイメージが含まれます。

- [ビジネス インテリジェンス](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [データ ウェアハウス](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## データの移行

SQL Server 仮想マシンを起動した後、マシンに既存のデータベースを移行できます。複数の手法がありますが、SQL Server Management Studio のデプロイ ウィザードは、ほとんどのシナリオに適してします。シナリオの詳細とウィザードのチュートリアルについては、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-migrate-onpremises-database.md)」を参照してください。

## バックアップと復元
オンプレミス データベースを使う場合は、SQL Server のバックアップ ファイルを格納するためのセカンダリ データ センターとして Azure を利用できます。バックアップと復元のオプションの概要については、「[Azure Virtual Machines における SQL Server のバックアップと復元](virtual-machines-sql-server-backup-and-restore.md)」を参照してください。

[SQL Server Backup to URL](https://msdn.microsoft.com/library/dn435916.aspx) を実行すると、Azure のバックアップ ファイルが Azure BLOB ストレージに格納されます。[SQL Server マネージ バックアップ](https://msdn.microsoft.com/library/dn449496.aspx)を実行すると、Azure でバックアップのスケジュールと保持期間を設定できます。これらのサービスは、オンプレミスの SQL Server インスタンスか、Azure VM で実行されている SQL Server で使用できます。Azure VM は、SQL Server の[自動バックアップ](virtual-machines-sql-server-automated-backup.md)や[修正プログラムの自動適用](virtual-machines-sql-server-automated-patching.md)を活用することもできます。

<!---HONumber=August15_HO8-->
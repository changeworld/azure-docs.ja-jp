<properties 
	pageTitle="Azure Virtual Machines における SQL Server" 
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
	ms.date="04/17/2015"
	ms.author="jroth"/>

# Azure Virtual Machines における SQL Server

## 概要
AlwaysOn 可用性グループと Azure の仮想ネットワークを使用すると、1 つのデータベース サーバーや複数のコンピューターなど、さまざまな構成で、[Azure Virtual Machines に SQL Server][sqlvmlanding] をホストすることができます。

> [AZURE.NOTE]Azure VM で SQL Server を実行することは、リレーショナル データを Azure に格納するための 1 つのオプションです。Azure SQL Database サービスを使用することもできます。詳細については、「[Azure SQL Database と Azure VM 内の SQL Server について][sqldbcompared]」を参照してください。
 
## 1 つの VM での SQL Server インスタンスのデプロイ
自動化または [Azure ポータルを使用して Azure の仮想マシンを作成する][createvmportal]と、ライセンスがある SQL Server の任意のインスタンスをインストールすることができます。ただし、SQL Server コンピューターと他のクライアント コンピューターの間で[接続をセットアップ][setupconnectivity]する追加の手順が必要になります。
 
また、ギャラリーにある各種 SQL Server 仮想マシン イメージのいずれかをインストールすることもできます。それらのイメージには、VM の料金での SQL Server のライセンスが含まれます。詳細については、「[Azure での SQL Server 仮想マシンのプロビジョニング][provisionsqlvm]」を参照してください。

## 複数の VM での高可用性構成のデプロイ
SQL Server AlwaysOn 可用性グループを使用すると、SQL Server の高可用性を実現できます。そのために、1 つの仮想ネットワークで複数の Azure VM を使用します。Azure プレビュー ポータルには、この構成が事前に設定されたテンプレートがあります。詳細については、[Microsoft Azure ポータル ギャラリーで提供されている SQL Server AlwaysOn テンプレート][sqlalwaysonportal]に関する記事を参照してください。または、[AlwaysOn 可用性グループを手動で構成][sqlalwaysonmanual]することもできます。その他の高可用性構成については、「[Azure の仮想マシン内の SQL Server の高可用性と災害復旧][sqlhadr]」を参照してください。

## Azure でのビジネス インテリジェンス、データ ウェアハウジング、OLTP ワークロードの実行   
Azure Virtual Machines で SQL Server の一般的なワークロードを実行できます。SQL Server では、ギャラリーで提供されている、最適化された仮想マシン イメージをいくつか使用することができます。このイメージには、[ビジネス インテリジェンス][sqlbi]、[データ ウェアハウジング][sqldw]、[OLTP][sqloltp] があります。

## データの移行
SQL Server を実行する Azure VM にデータを移行するには、いくつかの方法があります。まず、Azure ポータル、PowerShell の自動化、SQL Server Management Studio のデプロイメント ウィザードのいずれかを使用して、SQL Server 仮想マシンをプロビジョニングします。最適化された SQL Server のイメージには、その価格モデルでのライセンスが含まれていますが、独自のライセンスを使用して SQL Server をインストールすることもできます。データを移行するには、デプロイメント ウィザードを使用したり、対象の仮想マシンにデータ ディスクを移行したりするなどのいくつかのオプションがあります。詳細については、「[Azure の仮想マシンに SQL Server を移行するための準備][migratesql]」を参照してください。

## バックアップと復元
オンプレミス データベースを使う場合は、SQL Server のバックアップ ファイルを格納するためのセカンダリ データ センターとして Azure を利用できます。[SQL Server Backup to URL][backupurl] を実行すると、Azure のバックアップ ファイルが Azure BLOB ストレージに格納されます。[SQL Server マネージ バックアップ][managedbackup]を実行すると、Azure でバックアップのスケジュールと保持期間を設定できます。これらのサービスは、オンプレミスの SQL Server インスタンスか、Azure VM で実行されている SQL Server で使用できます。Azure VM は、SQL Server の[自動バックアップ][autobackup]や[修正プログラムの自動適用][autopatching]を活用することもできます。詳細については、「[Azure の仮想マシンにおける SQL Server の管理タスク][managementtasks]」を参照してください。

## その他のリソース
[Azure VM 内の SQL Server][sqlmsdnlanding]

[Azure の仮想マシンにおける SQL Server の概要][sqlvmgetstarted]

[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス][sqlperf]

[Azure 仮想マシンにおける SQL Server のセキュリティに関する考慮事項][sqlsecurity]

[Azure の仮想マシンにおける SQL Server に関する技術記事][technicalarticles]

  [sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
  [sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
  [createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
  [setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
  [provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
  [sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
  [sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
  [sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
  [sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
  [sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
  [sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
  [migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
  [backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
  [managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
  [autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
  [autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
  [managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
  [sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
  [sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
  [sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
  [sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
  [technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx

<!---HONumber=July15_HO2-->
<properties 
	pageTitle="SQL Server Data Warehousing and Transactional Workloads in Azure Virtual Machines (Azure Virtual Machines における SQL Server データ ウェアハウスおよびトランザクション ワークロード)"
	description="データ ウェアハウスと OLTP ワークロード用に事前構成され、最適化された Azure の SQL Server 仮想マシン イメージについて説明します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth" />

# SQL Server Data Warehousing and Transactional Workloads in Azure Virtual Machines (Azure Virtual Machines における SQL Server データ ウェアハウスおよびトランザクション ワークロード)

Azure の仮想マシンでデータ ウェアハウスまたはトランザクションのワークロード用に SQL Server を使用するには、Azure Virtual Machines ギャラリーにある構成済み仮想マシン イメージの 1 つを使用することをお勧めします。これらのイメージは、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](https://msdn.microsoft.com/library/azure/dn133149.aspx)」に記載されている推奨事項に基づいて最適化されています。

この記事では、Azure Virtual Machines におけるこれらのワークロードの実行を中心に説明します (これは、サービスとしてのインフラストラクチャ (IaaS) とも呼ばれます)。また、データ ウェアハウスとトランザクション ワークロードを Azure でサービスとして実行することができます。詳細については、[Azure SQL Data Warehouse プレビュー](http://azure.microsoft.com/documentation/services/sql-data-warehouse/)に関するページと [Azure SQL Database](http://azure.microsoft.com/documentation/services/sql-database/) に関するページを参照してください。

## 使用できる構成済み VM イメージ

Azure VM ギャラリーには、次の構成済み VM イメージが用意されています。

- [Windows Server 2012 R2 でのトランザクション ワークロード用に最適化された SQL Server 2014 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [Windows Server 2012 R2 でのデータ ウェアハウス用に最適化された SQL Server 2014 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [Windows Server 2012 R2 でのトランザクション ワークロード用に最適化された SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [Windows Server 2012 R2 でのデータ ウェアハウス用に最適化された SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [Windows Server 2012 でのトランザクション ワークロード用に最適化された SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [Windows Server 2012 でのデータ ウェアハウス用に最適化された SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

現時点では、最も高いスループット (または累積帯域幅) を提供するために最大 16 個のデータ ディスクを接続できる VM インスタンスで、これらのイメージがサポートされます。これらのインスタンスは、Standard レベルの A4、A7、A8、A9、D4、D13、D14、F3、G4、G5 と、Basic レベルの A4 です。サイズとオプションの詳細については、[Azure の仮想マシンのサイズ](virtual-machines-size-specs.md)に関するページを参照してください。

>[AZURE.NOTE]2014 年 9 月までは、以前のトランザクションおよびデータ ウェアハウスのギャラリー イメージを使用できました。ただし、それらのイメージでは、使用可能なデータ ディスクを接続する必要がありました。上記の新しいイメージはプロビジョニング後すぐに使用できる状態になるため、新しいイメージを使用することをお勧めします。

## トランザクション/DW イメージを使用したギャラリーからの SQL VM のプロビジョニング

1. [Azure 管理ポータル](http://manage.windowsazure.com/)にサインインします。

1. 左ペインにある Azure のメニュー項目で、**[仮想マシン]** をクリックします。

1. 左下の **[新規]** をクリックして、**[Compute]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

1. 仮想マシン イメージの選択ページで、トランザクションまたはデータ ウェアハウスのイメージ用 SQL Server を 1 つ選択します。

	![Azure VM Gallery](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. **[仮想マシンの構成]** ページでの **[サイズ]** オプションで、サポートされているサイズから選択します。

	![Azure VM Gallery Configuration](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]現在サポートされているのは、Standard レベルの A4、A7、A8、A9、D4、D13、D14、G3、G4、G5 と、Basic レベルの A4 のみです。サポートされていない VM のサイズをプロビジョニングしようとすると失敗します。

1. プロビジョニングが完了するまで待ちます。待機中に、仮想マシンのページでプロビジョニングの状態を確認できます (下図参照)。プロビジョニングが完了すると、状態が **[実行中]** になり、チェックマークが付きます。

	![Azure VM Gallery Status](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## PowerShell を使用したトランザクション/DW イメージの作成

PowerShell コマンドレットの **New-AzureQuickVM** を使用して VM を作成することもできます。パラメーターとして、クラウド サービス名、VM 名、イメージ名、管理者ユーザー名とパスワード、および同様の情報を渡す必要があります。イメージの名前を取得する簡単な方法は、**Get-AzureVMImage** を使用して、利用可能なすべての VM イメージの一覧を表示することです。

たとえば、次の PowerShell コマンドは、前のセクションの一覧から、"**SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2**" というイメージ ラベルに一致する最新のイメージを返します。

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

PowerShell を使用してイメージを作成する方法の詳細については、「[Azure PowerShell を使用して Windows ベースの仮想マシンを作成と事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)」を参照してください。

## トランザクション/DW イメージに含まれる個々の構成

イメージに含まれる最適化は「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](https://msdn.microsoft.com/library/azure/dn133149.aspx)」に基づいています。具体的には、これらのイメージの構成には、次のような最適化が含まれます。

>[AZURE.NOTE]独自のライセンスを持ち込んで、一からデータ ウェアハウスまたはトランザクションの仮想マシンを作成する場合は、パフォーマンスに関する記事と、以下の構成済みギャラリー イメージの最適化の例に基づいて最適化できます。

### ディスクの構成


|---|---|
|接続されているデータ ディスクの数|15|
|記憶域|次の 2 つの記憶域プール:<br/>12 個のデータ ディスクを含む 1 つのデータ プール (固定サイズ: 12 TB、列: 12)<br/>-- 3 つのデータ ディスクを含む 1 つのログ プール (固定サイズ: 3 TB、列: 3)<br/><br/>1 つのデータ ディスクは、ユーザーが接続し、使用法を決定するためのものです。<br/><br/>**DW**: ストライプ サイズ = 256 KB<br/>**トランザクション**: ストライプ サイズ = 64 KB|
|ディスクのサイズ、キャッシュ、割り当てサイズ|それぞれ 1 TB、HostCache = なし、NTFS アロケーション ユニット サイズ = 64 KB|

### SQL Server の構成

|---|---|
|起動時のパラメーター|- T1117。データベースで自動拡張が必要な場合に、データ ファイルを同じサイズに保つことができます。<br/><br/>-T1118。tempdb スケーラビリティをサポートします (詳細については、[SQL Server (2005 および 2008) トレース フラグ 1118 (-T1118) の使用](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce)に関するページを参照してください)。|
|復旧モデル|**DW**: ALTER DATABASE を使用して、モデル データベースを "SIMPLE" に設定します。<br/>**トランザクション**: 変更しません。|
|既定の場所の設定|SQL Server のエラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。|
|データベースの既定の場所|システム データベースはデータ ディスクに移動されました。<br/><br/>ユーザー データベースを作成するための場所はデータ ディスクに変更されました。|
|ファイルの瞬時初期化|有効|
|メモリ内のページのロック|有効 (詳細については、「[Lock Pages in Memory オプションの有効化 (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)」を参照してください。|

## FAQ

- 最適化されたイメージと最適化されていないイメージの価格に差はありますか。

	いいえ。最適化されたイメージも同じ価格モデル (詳細については、[こちら](http://azure.microsoft.com/pricing/details/virtual-machines/)を参照) に準じており、追加料金はありません。VM のインスタンス サイズが大きくなると、価格が高くなることに注意してください。

- 他に検討が必要なパフォーマンスの修正点はありますか。

	はい。SQL Server に関連する次のパフォーマンスの修正点を検討してください。

	- [修正: SQL Server 2012 で SELECT INTO 一時テーブル操作を実行すると I/O のパフォーマンスが低下する](https://support.microsoft.com/kb/2958012)

	- [修正: SQL Server 2014 で SQL Server リソースを移動すると "SQL Server パフォーマンス カウンターが無効です" と表示される](http://support.microsoft.com/kb/2973444)

- 記憶域の詳細情報はどこにありますか。

	記憶域の詳細については、[記憶域に関してよく寄せられる質問 (FAQ)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx) に関するページを参照してください。

- 新しい DW イメージと以前の DW イメージとの違いは何ですか。

	以前の DW イメージの場合は、VM を作成した後でデータ ディスクを接続するなどの追加手順を実行する必要があるのに対して、新しい DW イメージの場合は、作成後すぐに使用できる状態になるため、より短時間で使用可能になり、エラーの可能性が低くなります。

- 以前の DW イメージを使用する必要がある場合はどうすればよいですか。 アクセスできる方法はありますか。

	以前の VM イメージは引き続き使用できますが、ギャラリーから直接アクセスすることはできません。代わりに、Powershell コマンドレットを引き続き使用できます。たとえば、**Get-AzureVMImage** を使用して、すべてのイメージを一覧表示できます。説明と発行日に基づいて以前の DW イメージを見つけた場合、**New-AzureVM** を使用してプロビジョニングできます。

## 次のステップ

すべての仮想マシンを SQL Server にインストールした後で、次の操作の実行が必要になる可能性があります。

- [データの移行](virtual-machines-migrate-onpremises-database.md)
- [接続のセットアップ](virtual-machines-sql-server-connectivity.md)

Azure VM での SQL Server の実行に関するその他のトピックについては、「[Azure Virtual Machines における SQL Server](virtual-machines-sql-server-infrastructure-services.md)」を参照してください。

<!---HONumber=August15_HO8-->
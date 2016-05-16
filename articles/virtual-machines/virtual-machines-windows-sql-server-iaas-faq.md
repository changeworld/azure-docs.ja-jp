<properties
	pageTitle="Azure Virtual Machines における SQL Server に関する FAQ | Microsoft Azure"
	description="この記事では、Azure VM で SQL Server を実行することに関してよく寄せられる質問に対する回答を示します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/27/2016"
	ms.author="v-shysun"/>

# Azure Virtual Machines における SQL Server に関する FAQ

このトピックでは、[Azure Virtual Machines における SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) に関して特に多く寄せられる質問への回答を示します。

## よく寄せられる質問

1. **SQL Server で Azure 仮想マシンを作成するにはどうするのですか?**

	2 つの方法があります。最も簡単な解決方法は、SQL Server を含む仮想マシンを作成することです。Azure にサインアップして、ポータルから SQL VM を作成するチュートリアルについては、「[Azure ポータルでの SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。VM に手動で SQL Server をインストールして、「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」によってオンプレミスのライセンスを再利用するオプションもあります。

1. **SQL VM と SQL Database サービスの違いは何ですか?**

	概念上、Azure 仮想マシンで SQL Server を実行することは、リモート データ センターで SQL Server を実行することと違いません。これに対し、[SQL Database](../sql-database/sql-database-technical-overview.md) はサービスとしてデータベースを提供します。SQL Database では、データベースをホストするマシンにアクセスできません。完全な比較については、「[クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](../sql-database/media/data-management-azure-sql-database-and-sql-server-iaas.md)」を参照してください。

1. ** オンプレミス SQL Server データベースをクラウドに移行するにはどうすればよいですか?**

	まず SQL Server インスタンスで、Azure 仮想マシンを作成します。次に、オンプレミス データベースをそのインスタンスに移行します。データ移行の方法については、「[Azure VM の SQL Server への SQL Server データベースの移行](virtual-machines-windows-migrate-sql.md)」を参照してください。

2. **インストールされている機能を変更したり、同じ VM に SQL Server の 2 つ目のインスタンスをインストールしたりできますか?**

	はい。SQL Server インストール メディアは、**C** ドライブ上のフォルダーにあります。その場所から、**Setup.exe** を実行して、新しい SQL Server インスタンスを追加するか、またはコンピューター上の SQL Server の他のインストールされている機能を変更します。

3. **Azure VM で SQL Server の新しいバージョン/エディションにアップグレードするにはどうすればよいですか?**

	現在、Azure VM で実行している SQL Server のインプレース アップグレードはありません。目的の SQL Server バージョン/エディションで新しい Azure 仮想マシンを作成し、標準の[データ移行方法](virtual-machines-windows-migrate-sql.md)を使用して、データベースを新しいサーバーに移行します。

4. **Azure VM に SQL Server のライセンス版をインストールするにはどうすればよいですか?**

	SQL Server インストール メディアを Windows Server VM にコピーしてから、SQL Server を VM にインストールします。ライセンス上の理由で、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)が必要です。

5. **スタンバイ/フェールオーバー用にのみ使用している場合に、VM の SQL のコストを支払う必要がありますか?**

	ギャラリーから SQL VM を作成している場合、スタンバイ SQL VM の個別のライセンスが必要であり、料金は同じです。[ライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)で、仮想マシンに SQL Server を手動でインストールする場合、フェール オーバー用に無料のパッシブ SQL インスタンスを使用するオプションがあります。制限事項と要件を確認してください。

6. **SQL Server VM に更新プログラムと Service Pack を適用するにはどうすればよいですか?**

	仮想マシンでは、更新プログラムを適用するタイミングと方法を含めて、ホスト マシンを制御できます。オペレーティング システムの場合、Windows 更新プログラムを手動で適用するか、または[自動修正](virtual-machines-windows-classic-sql-automated-patching.md)と呼ばれるスケジュール サービスを有効にすることができます。自動修正では、そのカテゴリ内の SQL Server の更新を含む、重要とマークされている更新プログラムがインストールされます。SQL Server へのその他のオプションの更新プログラムは、手動でインストールする必要があります。

7. **仮想マシン ギャラリーに表示されていない構成 (Windows 2008 R2 + SQL Server 2012 など) をセットアップすることはできますか?**

	いいえ。SQL Server を含む仮想マシン ギャラリー イメージでは、提供されているイメージのいずれかを選択する必要があります。

9. **Azure VM に SQL データ ツールをインストールするにはどうすればよいですか?**

	「[Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/ja-JP/download/details.aspx?id=42313)」から、SQL データ ツールをダウンロードして、インストールします。

## Resources

Azure Virtual Machines 上の SQL Server の概要については、ビデオ「[Azure VM is the best platform for SQL Server 2016 (Azure VM は SQL Server 2016 に最適なプラットフォーム)](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)」をご覧ください。トピック「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」も優れた入門書です。

その他のリソースには次のものがあります。

- [Azure ポータルでの SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)
- [Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)
- [Azure の仮想マシン内の SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)
- [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)
- [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

<!---HONumber=AcomDC_0504_2016-->
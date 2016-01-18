<properties
	pageTitle="SQL Server 仮想マシンのプロビジョニング | Microsoft Azure"
	description="このチュートリアルでは、Azure で SQL Server VM を作成および構成する方法を説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="jroth"/>

# Azure での SQL Server 仮想マシンのプロビジョニング

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

## 概要

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

Azure の仮想マシン イメージ ギャラリーには、Microsoft SQL Server を含むイメージがいくつか用意されています。ギャラリーからいずれかの仮想マシン イメージを選択すると、わずか数クリックで、Azure 環境への仮想マシンのプロビジョニングを行うことができます。

このチュートリアルでは、次のことについて説明します。

* [Azure クラシック ポータルに接続し、ギャラリーから仮想マシンのプロビジョニングを行う](#Provision)
* [リモート デスクトップを使用して仮想マシンを開き、セットアップを完了する](#RemoteDesktop)
* [別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する](#SSMS)
* [次のステップ](#Optional)

>[AZURE.NOTE]この記事では、既存のポータルを使用して SQL Server VM をプロビジョニングする方法を説明します。ただし、[新しいポータル](https://manage.windowsazure.com)で SQL Server VM を作成および管理することもできます。Premium Storage の使用が既定に設定されることや、自動修正、自動バックアップ、AlwaysOn 構成といったその他のオプションなど、新しいポータルにはいくつかの利点があります。今後のコンテンツで、詳細な手順を説明します。

##<a id="Provision">ギャラリーからの SQL Server 仮想マシンのプロビジョニング</a>

1. アカウントを使用して [Azure クラシック ポータル](http://manage.windowsazure.com)にログインします。Azure アカウントを持っていない場合は、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)にアクセスしてください。

2. Azure クラシック ポータルで、Web ページの左下にある **[+ 新規]** をクリックし、**[Compute]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

3. **[イメージの選択]** ページで、**[SQL SERVER]** をクリックします。SQL Server イメージを選択します。ページの右下にある [次へ] 矢印をクリックします。

	![イメージの選択](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Azure でサポートされる SQL Server イメージの最新情報については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-sql-server-infrastructure-services.md)」をご覧ください。

>[AZURE.NOTE]プラットフォーム イメージの SQL Server 評価エディションを使用して仮想マシンを作成した場合、それをギャラリーにある分単位課金エディション イメージにアップグレードすることはできません。次の 2 つのオプションのいずれかを選択できます。
>
> - ギャラリーにある分単位課金エディションの SQL Server を使用して新しい仮想マシンを作成し、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-migrate-onpremises-database)」の手順に従って、データベース ファイルをこの新しい仮想マシンに移行できます。
> - または、「[SQL Server の別のエディションへのアップグレード](https://msdn.microsoft.com/library/cc707783.aspx)」の手順に従って、「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](http://azure.microsoft.com/pricing/license-mobility/)」の合意に基づき、SQL Server 評価エディションの既存のインスタンスを異なるエディションの SQL Server にアップグレードすることもできます。SQL Server のライセンス コピーを購入する方法については、[SQL Server の購入方法に関するページ](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx)を参照してください。

4. 最初の **[仮想マシンの構成]** ページで、次の情報を指定します。
	- **バージョンのリリース日**。複数のイメージが利用できる場合は、最新のイメージを選択します。
	- 一意の**仮想マシン名**。
	- **[新しいユーザー名]** ボックスに、コンピューターのローカル管理者アカウントの一意のユーザー名を入力します。
	- **[新しいパスワード]** ボックスに、強力なパスワードを入力します。
	- **[パスワードの確認]** ボックスに、パスワードを再度入力します。
	- **[サイズ]** ドロップダウン リストで、適切なサイズを選択します。

	![VM 構成](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]仮想マシンのサイズは、プロビジョニングで指定します。
 	>
	> - 実稼働ワークロードの場合は、次の最小推奨サイズで Premium Storage を使用することをお勧めします。SQL Server Enterprise Edition の場合は **DS3**。SQL Server Standard Edition の場合は **DS2**。詳細については、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-sql-server-performance-best-practices.md)」をご覧ください。
	> - 選択したサイズにより、構成できるデータ ディスクの数が制限されます。使用可能な仮想マシンのサイズと仮想マシンに接続できるデータ ディスクの数に関する最新情報については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](virtual-machines-size-specs.md)」を参照してください。

5. VM 構成の詳細を入力後、右下にある次へ進む矢印をクリックして続行します。

5. 2 回目の **[仮想マシンの構成]** ページで、ネットワーク、ストレージ、可用性に関するリソースを構成します。
	- **[クラウド サービス]** ボックスで、**[新しいクラウド サービスの作成]** を選択します。
	- **[クラウド サービス DNS 名]** ボックスに、目的の DNS 名の最初の部分を入力すると、**TESTNAME.cloudapp.net** という形式の完全な名前を指定できます。
	- 複数のサブスクリプションがある場合は、**[サブスクリプション]** を選択します。この選択で、利用できる**ストレージ アカウントが決まります。
	- **[リージョン/アフィニティ グループ/仮想ネットワーク]** ボックスで、この仮想イメージをホストするリージョンを選択します。
	- **[ストレージ アカウント]** で、自動的にアカウントを生成するか、一覧から 1 つ選択します。**[サブスクリプション]** を変更して複数のアカウントを表示します。
	- **[可用性セット]** ボックスの一覧の **[(なし)]** を選択します。
	- 法律条項を読み、同意します。


6. 矢印をクリックして次へ進みます。


7. 続行するには、右下隅にあるチェック マークをクリックします。

8. Azure によって仮想マシンの準備が行われるまで待ちます。仮想マシンの状態は次のように進行します。

	- **開始中 (プロビジョニング)**
	- **Stopped**
	- **開始中 (プロビジョニング)**
	- **実行中 (プロビジョニング)**
	- **実行中**


##<a id="RemoteDesktop">リモート デスクトップを使用して VM を開き、設定を完了します。</a>

1. プロビジョニングが完了したら、仮想マシンの名前をクリックして [ダッシュボード] ページに移動します。ページの下部にある **[接続]** をクリックします。

2. **[開く]** ボタンをクリックします。

	![[開く] をクリックする](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. **[Windows セキュリティ]** ダイアログ ボックスで、**[別のアカウントを使用]** をクリックします。

	![[別のアカウントを使用] をクリックする](./media/virtual-machines-provision-sql-server/credentials.png)

4. `machinename\username` の形式で、ドメイン名に管理者名が付くマシンの名前を使用します。パスワードを入力し、マシンに接続します。

4. 初めてログオンした場合は、デスクトップのセットアップや Windows の更新プログラムの適用、Windows の初期構成タスク (sysprep) の完了など複数のプロセスが実行されます。Windows sysprep の完了後、SQL Server セットアップによって構成タスクが完了されます。これらのタスクを完了するには、数分の遅延が発生します。`SELECT @@SERVERNAME` は SQL Server セットアップが完了するまで、正しい名前を返さず、SQL Server Management Studio がスタート ページに表示されない場合があります。

Windows リモート デスクトップで仮想マシンに接続したら、仮想マシンは他のコンピューターと同様に使用できます。SQL Server Management Studio (仮想マシン上で実行) を使用して、通常どおりに SQL Server の既定インスタンスに接続します。

##<a id="SSMS">別のコンピューターで SSMS から SQL Server VM インスタンスに接続します。</a>

次の手順で、SQL Server Management Studio (SSMS) を使用してインターネット経由で SQL Server インスタンスに接続する方法を説明します。ただし、同じ手順が、アプリケーションのアクセスが可能な SQL Server 仮想マシンを作成し、オンプレミスと Azure クラシック デプロイ モデルの両方で実行する際に適用されます。仮想マシンがリソース マネージャー モデルでデプロイされている場合は、「[Azure での SQL Server 仮想マシンへの接続 (リソース マネージャー)](virtual-machines-sql-server-connectivity-resource-manager.md)」をご覧ください。

別の VM またはインターネットから SQL Server インスタンスに接続するには、次のタスクを完了している必要があります。詳細については、この後のセクションで説明します。

- [仮想マシン用の TCP エンドポイントを作成する](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Windows ファイアウォールで TCP ポートを開く](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [TCP プロトコルでリッスンするように SQL Server を構成する](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [混合モード認証用に SQL Server を構成する](#configure-sql-server-for-mixed-mode-authentication)
- [SQL Server 認証ログインを作成する](#create-sql-server-authentication-logins)
- [仮想マシンの DNS 名を特定する](#determine-the-dns-name-of-the-virtual-machine)
- [別のコンピューターからデータベース エンジンに接続する](#connect-to-the-database-engine-from-another-computer)

次の図は、接続パスの概要を示したものです。

![SQL Server 仮想マシンに接続する](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [VM クラシック TCP エンドポイントの SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [VM 内で SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [VM クラシック手順で SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a id="cdea">アプリケーションからデータベース エンジンに接続する</a>

Azure の仮想マシンで実行されている SQL Server のインスタンスに Management Studio から接続できる場合は、次のような接続文字列を使用して接続できます。

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

詳細については、「[How to Troubleshoot Connecting to the SQL Server Database Engine (SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法)](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)」を参照してください。

##<a id="Optional">次のステップ</a>

プラットフォーム イメージを使用してAzure に SQL Server 仮想マシンを作成し、構成する方法を見てきました。多くの場合、次の手順はこの新しい SQL Server VM にデータベースを移行することです。データベース移行方法については、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-migrate-onpremises-database.md)」を参照してください。

次の一覧には、Azure Virtual Machines における SQL Server のその他のリソースが示されています。

### Azure VM 上の SQL Server 用のお勧めのリソース:
- [Azure Virtual Machines における SQL Server の概要](virtual-machines-sql-server-infrastructure-services.md)

- [Azure での SQL Server 仮想マシンへの接続](virtual-machines-sql-server-connectivity.md)

- [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-sql-server-performance-best-practices.md)

- [Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項](virtual-machines-sql-server-security-considerations.md)

### 高可用性と障害復旧
- [Azure の仮想マシン内の SQL Server の高可用性と障害復旧](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Azure の仮想マシンにおける SQL Server のバックアップと復元](virtual-machines-sql-server-backup-and-restore.md)

### Azure での SQL Server ワークロード
- [Azure の仮想マシンでの SQL Server Business Intelligence](virtual-machines-sql-server-business-intelligence.md)

### ホワイト ペーパー
- [Understand Azure SQL Database and SQL Server in Azure Virtual Machines (Azure Virtual Machines における Azure SQL Database と SQL Server について)](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=AcomDC_0107_2016-->
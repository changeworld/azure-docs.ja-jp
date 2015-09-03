<properties 
	pageTitle="Azure での SQL Server 仮想マシンのプロビジョニング" 
	description="Azure で SQL Server 仮想マシンを作成して構成する方法を学習するチュートリアルです。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="jroth"/>

# Azure での SQL Server 仮想マシンのプロビジョニング #

Azure の仮想マシン イメージ ギャラリーには、Microsoft SQL Server を含むイメージがいくつか用意されています。ギャラリーからいずれかの仮想マシン イメージを選択すると、わずか数クリックで、Azure 環境への仮想マシンのプロビジョニングを行うことができます。

このチュートリアルでは、次のことについて説明します。

* [Azure の管理ポータルに接続し、ギャラリーから仮想マシンのプロビジョニングを行う](#Provision)
* [リモート デスクトップを使用して仮想マシンを開き、セットアップを完了する](#RemoteDesktop)
* [別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する](#SSMS)
* [次のステップ](#Optional)

##<a id="Provision">ギャラリーからの SQL Server 仮想マシンのプロビジョニング</a>

1. アカウントを使用して [Azure の管理ポータル](http://manage.windowsazure.com)にログインします。Azure アカウントを持っていない場合は、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)にアクセスしてください。

2. Azure の管理ポータルで、Web ページの左下にある **[+ 新規]** をクリックし、**[コンピューティング]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

3. **[イメージの選択]** ページで、**[SQL SERVER]** をクリックします。SQL Server イメージを選択します。ページの右下にある [次へ] 矢印をクリックします。

	![Choose an Image](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Azure でサポートされる SQL Server イメージの最新情報については、「[Azure の仮想マシンにおける SQL Server の概要](http://go.microsoft.com/fwlink/p/?LinkId=294720)」をご覧ください。

>[AZURE.NOTE]プラットフォーム イメージの SQL Server 評価エディションを使用して仮想マシンを作成した場合、それをギャラリーにある分単位課金エディション イメージにアップグレードすることはできません。次の 2 つのオプションのいずれかを選択できます。
>
> - ギャラリーにある分単位課金エディションの SQL Server を使用して新しい仮想マシンを作成し、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-migrate-onpremises-database)」の手順に従って、データベース ファイルをこの新しい仮想マシンに移行します。
> - または、「[SQL Server の別のエディションへのアップグレード](https://msdn.microsoft.com/library/cc707783.aspx)」の手順に従って、「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](http://azure.microsoft.com/pricing/license-mobility/)」の合意に基づき、SQL Server 評価エディションの既存のインスタンスを異なるエディションの SQL Server にアップグレードします。SQL Server のライセンス コピーを購入する方法については、[SQL Server の購入方法に関するページ](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx)を参照してください。

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
	> - 運用環境のワークロードで推奨される最小サイズは A2 です。 
    > - SQL Server Enterprise Edition の使用時に推奨される仮想マシンの最小サイズは A3 です。
    > - SQL Server Enterprise Edition を使用する場合は、A3 以上を選択してください。
   	> - SQL Server 2012 または 2014 Enterprise Optimized for Transactional Workloads イメージを使用する場合は A4 以上を選んでください。  
   	> - SQL Server 2012 または 2014 Enterprise Optimized for Data Warehousing Workloads イメージを使用する場合は A7 以上を選んでください。 
   	> - 最大のパフォーマンスを得るには、Premium Storage の DS2 または DS3 を使用します。詳細については、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](https://msdn.microsoft.com/library/azure/dn133149.aspx)」をご覧ください。
   	> - 選択したサイズにより、構成できるデータ ディスクの数が制限されます。使用可能な仮想マシンのサイズと仮想マシンに接続できるデータ ディスクの数に関する最新情報については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](virtual-machines-size-specs.md)」を参照してください。

5. VM 構成の詳細を入力後、右下にある次へ進む矢印をクリックして続行します。

5. 2 回目の **[仮想マシンの構成]** ページで、ネットワーキング、ストレージ、可用性に関するリソースを構成します。
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

	![Click the Open button](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. **[Windows セキュリティ]** ダイアログ ボックスで、**[別のアカウントを使用]** をクリックします。

	![Click Use another account](./media/virtual-machines-provision-sql-server/credentials.png)

4. `machinename\username` の形式で、ドメイン名に管理者名が付くマシンの名前を使用します。パスワードを入力し、マシンに接続します。

4. 初めてログオンした場合は、デスクトップのセットアップや Windows の更新プログラムの適用、Windows の初期構成タスク (sysprep) の完了など複数のプロセスが実行されます。Windows sysprep の完了後、SQL Server セットアップによって構成タスクが完了されます。これらのタスクを完了するには、数分の遅延が発生します。`SELECT @@SERVERNAME` は SQL Server セットアップが完了するまで、正しい名前を返さず、SQL Server Management Studio がスタート ページに表示されない場合があります。

Windows リモート デスクトップで仮想マシンに接続したら、仮想マシンは他のコンピューターと同様に使用できます。SQL Server Management Studio (仮想マシン上で実行) を使用して、通常どおりに SQL Server の既定インスタンスに接続します。

##<a id="SSMS">別のコンピューターで SSMS から SQL Server VM インスタンスに接続します。</a>

[AZURE.INCLUDE [VM 内で SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">アプリケーションからデータベース エンジンに接続する</a>

Azure の仮想マシンで実行されている SQL Server のインスタンスに Management Studio から接続できる場合は、次のような接続文字列を使用して接続できます。

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

詳細については、「[How to Troubleshoot Connecting to the SQL Server Database Engine (SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法)](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)」を参照してください。

##<a id="Optional">次のステップ</a>

プラットフォーム イメージを使用してAzure に SQL Server 仮想マシンを作成し、構成する方法を見てきました。多くの場合、次の手順はこの新しい SQL Server VM にデータベースを移行することです。データベース移行方法については、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-migrate-onpremises-database.md)」をご覧ください。

これらのリソースの他、[Azure の仮想マシンにおける SQL Serverに関連するその他のトピック](virtual-machines-sql-server-infrastructure-services.md)もご覧になることをお勧めします。次に、具体的なお勧めのリソースの一覧を示します。

### Azure VM 上の SQL Server 用のお勧めのリソース:
- [Azure の仮想マシンにおける SQL Server の概要](http://go.microsoft.com/fwlink/p/?LinkId=294720)

- [Azure の仮想マシンにおける SQL Server の接続に関する考慮事項](http://go.microsoft.com/fwlink/p/?LinkId=294723)

- [Azure Virtual Machines における SQL Server のパフォーマンスに関する考慮事項](http://go.microsoft.com/fwlink/?LinkId=294724)

- [Azure 仮想マシンにおける SQL Server のセキュリティに関する考慮事項](http://go.microsoft.com/fwlink/p/?LinkId=294725)

### 高可用性と障害復旧
- [Azure の仮想マシン内の SQL Server の高可用性と災害復旧](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- [Azure の仮想マシンにおける SQL Server のバックアップと復元](http://go.microsoft.com/fwlink/p/?LinkId=294728)

### Azure での SQL Server ワークロード
- [Azure の仮想マシンでの SQL Server Business Intelligence](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- [SQL Server Data Warehousing and Transactional Workloads in Azure Virtual Machines (Azure の仮想マシンにおける SQL Server データ ウェアハウスおよびトランザクション ワークロード)](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

### ホワイト ペーパー
- [Understand Azure SQL Database and SQL Server in Azure Virtual Machines (Azure Virtual Machines における Azure SQL Database と SQL Server について)](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](http://msdn.microsoft.com/library/azure/dn574746.aspx)

<!----HONumber=August15_HO8-->
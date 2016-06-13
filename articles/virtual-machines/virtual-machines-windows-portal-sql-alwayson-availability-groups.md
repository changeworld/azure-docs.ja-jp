<properties
	pageTitle="Azure Resource Manager での AlwaysOn 可用性グループの構成 | Microsoft Azure"
	description="Azure Virtual Machines を使用して Azure Resource Manager モードで AlwaysOn 可用性グループを作成します。このチュートリアルでは、ソリューション全体の自動作成に、主にユーザー インターフェイスを使用します。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/10/2016"
	ms.author="mikeray" />

# Azure Resource Manager 仮想マシン (GUI) での AlwaysOn 可用性グループの構成

> [AZURE.SELECTOR]
- [テンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [マニュアル](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)

<br/>

この詳細なチュートリアルでは、Azure リソース マネージャー仮想マシンに SQL Server の可用性グループを作成する方法について説明します。このチュートリアルでは、テンプレートの構成に Azure ブレードを使用します。このチュートリアルでは、既定の設定を確認し、必要な設定を入力し、ポータルでブレードを更新します。

>[AZURE.NOTE] Microsoft Azure 管理ポータルでは、リスナーを含む AlwaysOn 可用性グループ用に新しいギャラリーが設定されています。これを使用すると、可用性グループに必要なものすべてが自動的に構成されます。詳細については、「[SQL Server AlwaysOn Offering in Microsoft Azure Portal Gallery (Microsoft Azure ポータル ギャラリーで提供されている SQL Server AlwaysOn)](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)」をご覧ください。

チュートリアルの最後には、次の要素で構成された SQL Server 可用性グループ ソリューションが Azure で完成します。

- 複数のサブネットから成る仮想ネットワーク (フロントエンドのサブネットとバックエンドのサブネットを含む)

- Active Directory (AD) ドメインを持つ 2 つのドメイン コントローラー

- バックエンド サブネットにデプロイされ、AD ドメインに参加している 2 つの SQL Server VM

- 3 ノードの WSFC クラスター (ノード マジョリティ クォーラム モデル)

- 可用性データベースの 2 つの同期コミット レプリカを含む可用性グループ

このソリューションをグラフィカルに表すと、次の図のようになります。

![Azure での AG 向けテスト ラボ アーキテクチャ](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

このソリューションのすべてのリソースは、単一のリソース グループに属します。

このチュートリアルでは、次のことを前提としています。

- Azure アカウントを既に所有している。お持ちでない場合は、[試用版アカウントにサインアップ](http://azure.microsoft.com/pricing/free-trial/)してください。

- GUI を使用して、仮想マシン ギャラリーから SQL Server VM をプロビジョニングする方法を知っている。詳細については、[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)に関するページをご覧ください。

- 可用性グループについて十分に理解している。詳細については、「[AlwaysOn 可用性グループ (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx)」を参照してください。

>[AZURE.NOTE] SharePoint での SQL Server 可用性グループの使用に関心がある場合は、「[SQL Server 2012 の AlwaysOn 可用性グループを SharePoint 2013 用に構成する](http://technet.microsoft.com/library/jj715261.aspx)」を参照してください。

このチュートリアルでは、次を行うために Azure ポータルを使用します。

- ポータルから AlwaysOn テンプレートを選択する

- テンプレート設定を確認し、環境用にいくつかの構成設定を更新する

- Azure が環境全体を作成する過程を監視する

- ドメイン コント ローラーのいずれかと接続し、次いで SQL Server のいずれかと接続する

## リソース マネージャーのデプロイ モデルでギャラリーから可用性グループをプロビジョニングする

Azure では、ソリューション全体のギャラリー イメージを提供します。テンプレートを見つけるには、次を実行します。

1. 	アカウントを使用して Azure ポータルにログインします。
1.	Azure ポータルで **[+新規]** をクリックします。 ポータルで [新規] ブレードが開きます。
1.	[新規] ブレードで **[AlwaysOn]** を検索します。![AlwaysOn テンプレートを見つける](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.	検索結果から **[SQL Server AlwaysOn Cluster (SQL Server AlwaysOn クラスター)]** を見つけます。![AlwaysOn テンプレート](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.	**[デプロイ モデルの選択]** で **[リソース マネージャー]** を選択します。

### 基本

**[基本]** をクリックし、次を構成します。

- **[管理者ユーザー名]** は、ドメインの管理者権限を持つユーザー アカウントであり、また SQL Server の両方のインスタンスの SQL Server sysadmin 固定サーバー ロールのメンバーです。このチュートリアルでは **DomainAdmin** を使用します。

- **[パスワード]** は、ドメイン管理者アカウントのパスワードです。複雑なパスワードを使用します。パスワードを確認入力します。

- **[サブスクリプション]** は、可用性グループ用にデプロイされたすべてのリソースの実行に Azure が請求するサブスクリプションです。アカウントに複数のサブスクリプションがある場合は、別のサブスクリプションを指定できます。

- **[リソース グループ]** は、このチュートリアルで作成したすべての Azure リソースが属するグループです。このチュートリアルでは **SQL-HA-RG** を使用します。詳細については、(Azure リソース マネージャーの概要)[resource-group-overview.md/#resource-groups] を参照してください。

- **[場所]** は、このチュートリアルのリソースが作成される Azure リージョンです。インフラストラクチャをホストする Azure リージョンを選択します。

以下に **[基本]** ブレードの例を示します。

![基本](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- **[OK]** をクリックします。

### ドメインおよびネットワークの設定

この Azure ギャラリー テンプレートでは、新しいドメイン コントローラーを持つ新しいドメインを作成します。また、新しいネットワークと 2 つのサブネットも作成します。このテンプレートでは、既存のドメインまたは仮想ネットワークには、サーバーを作成できません。次の手順では、ドメインおよびネットワーク設定を構成します。

**[Domain and network settings (ドメインおよびネットワークの設定)]** ブレードで、ドメインとネットワークの設定の既定値を確認します。

- **[フォレスト ルート ドメイン名]** は、クラスターをホストする AD ドメインが使用するドメイン名です。このチュートリアルでは、**contoso.com** を使用します。

- **[仮想ネットワーク名]** は、Azure の仮想ネットワークのネットワーク名です。このチュートリアルでは **autohaVNET** を使用します。

- **[Domain Controller subnet name (ドメイン コントローラーのサブネット名)]** は、ドメイン コントローラーをホストする仮想ネットワークの部分の名前です。このチュートリアルでは **subnet-1** を使用します。このサブネットでは、アドレスのプレフィックスとして **10.0.0.0/24** を使用します。

- **[SQL Server のサブネット名]** は、SQL サーバーとファイル共有監視をホストする仮想ネットワークの部分の名前です。このチュートリアルでは **subnet-2** を使用します。このサブネットでは、アドレスのプレフィックスとして **10.0.1.0/26** を使用します。

仮想ネットワークの詳細については、[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページをご覧ください。

**[Domain and network settings (ドメインおよびネットワークの設定)]** は次のようになります。

![ドメインおよびネットワークの設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

これらの値は、必要に応じて変更することができます。このチュートリアルでは、あらかじめ設定されている値を使用します。

- 設定を確認し、**[OK]** をクリックします。

###可用性グループの設定

**[availability group settings (可用性グループの設定)]** で、事前設定されている可用性グループとリスナーの値を確認します。

- **[Availablity group name (可用性グループ名)]** は、可用性グループのクラスター化されたリソース名です。このチュートリアルでは **Contoso-ag** を使用します。

- **[可用性グループ リスナーの名前]** は、クラスターおよび内部ロード バランサーによって使用されます。SQL Server に接続するクライアントは、この名前を使用し、データベースの適切なレプリカに接続できます。このチュートリアルでは **Contoso-listener** を使用します。

-  **[可用性グループ リスナーのポート]** には、SQL Server リスナーが使用する TCP ポートを指定します。このチュートリアルでは、既定のポート **1433** を使用します。

これらの値は、必要に応じて変更することができます。このチュートリアルでは、あらかじめ設定されている値を使用します。

![可用性グループの設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- **[OK]** をクリックします。

###VM サイズおよび記憶域の設定

**[VM size, storage settings (VM サイズおよび記憶域の設定)]** では、SQL Server の仮想マシンのサイズを選択し、その他の設定を確認します。

- **[SQL Server virtual machine size (SQL Server 仮想マシンのサイズ)]** は、両方の SQL Server の Azure 仮想マシンのサイズです。ワークロードに適した仮想マシンのサイズを選択します。このチュートリアル用に環境を構築する場合は **DS2** を使用します。実稼働ワークロードでは、ワークロードをサポートできる仮想マシンのサイズを選択します。多くの実稼働ワークロードでは、**DS4** 以上が必要です。このテンプレートでは、このサイズの仮想マシンを 2 つ構築し、それぞれに SQL Server をインストールします。詳細については、[仮想マシンのサイズ](virtual-machines-linux-sizes.md)に関するページをご覧ください。

>[AZURE.NOTE]Azure によって、SQL Server Enterprise Edition がインストールされます。価格は、エディションと仮想マシンのサイズによって異なります。現在の価格の詳細については、「[Virtual Machines の価格](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql)」を参照してください。

- **[Domain controller virtual machine size (ドメイン コントローラーの仮想マシンのサイズ)]** は、ドメイン コントローラー用の仮想マシンのサイズです。このチュートリアルでは **D2** を使用します。

- **[File Share Witness virtual machine size (ファイル共有監視の仮想マシンのサイズ)]** は、ファイル共有監視の仮想マシンのサイズです。このチュートリアルでは **A1** を使用します。

- **[SQL Storage account (SQL ストレージ アカウント)]** は、SQL Server のデータとオペレーティング システム ディスクを保持するストレージ アカウントの名前です。このチュートリアルでは **alwaysonsql01** を使用します。

- **[DC Storage account (DC ストレージ アカウント)]** は、ドメイン コントローラーのストレージ アカウントの名前です。このチュートリアルでは **alwaysondc01** を使用します。

- **[SQL Server data disk size in TB (SQL Server のデータ ディスク サイズ (TB))]** は、SQL Server のデータ ディスクの TB 単位でのサイズです。1 から 4 までの数字を指定します。これは各 SQL Server に接続するデータ ディスクのサイズです。このチュートリアルでは **1** を使用します。

- **[Storage optimization (記憶域の最適化)]** では、ワークロードの種類に基づいて SQL Server の仮想マシンの特定の記憶域の構成を設定します。このシナリオのすべての SQL Server では、Azure ディスクのホスト キャッシュが読み取り専用に設定された Premium ストレージを使用します。また、次の 3 つのいずれかの設定を選択すると、ワークロード用に SQL Server の設定を最適化できます。

    - **[General workload (一般的なワークロード)]** では、特定の構成は設定しません

    - **[Transactional processing (トランザクション処理)]** では、トレース フラグ 1117 と 1118 を設定します

    - **[Data warehousing (データ ウェアハウス)]** では、トレース フラグ 1117 と 610 を設定します

このチュートリアルでは、**[General workload (一般的なワークロード)]** を使用します。

![VM サイズの記憶域の設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- 設定を確認し、**[OK]** をクリックします。

####記憶域に関する注意点

さらなる最適化には、SQL Server のデータ ディスクのサイズが関係します。Azure は、データ ディスクにテラバイトごとに、Premium ストレージ(SSD) を 1 TB 追加します。サーバーに 2 TB 以上が必要な場合、テンプレートは、各 SQL Server に記憶域プールを作成します。記憶域プールとは、容量を増やし、回復性、およびパフォーマンスを向上させるために複数のディスクが構成された仮想化された記憶域の形式です。その後、テンプレートは、記憶域プールに記憶域スペースを作成し、これをオペレーティング システムに 1 つのデータとして表示します。テンプレートは、このディスクを SQL Server のデータ ディスクとして指定します。テンプレートは、次の設定で SQL Server の記憶域プールを調整します。

- ストライプ サイズは、仮想ディスクのインターリーブ設定です。これは、トランザクション ワークロードの場合、64 KB に設定されます。データ ウェアハウス ワークロードの場合、256 KB に設定されます。

- 回復性については単純です (回復性はありません)。

>[AZURE.NOTE] Azure Premium ストレージは、1 つのリージョンに 3 つのデータのコピーを保持するローカル冗長であるため、記憶域プールに追加の回復性は不要です。

- 列数は、記憶域プール内のディスクの数と同じです。

記憶域スペースおよび記憶域プールの詳細については、次を参照してください。

- [記憶域スペースの概要](http://technet.microsoft.com/library/hh831739.aspx)。

- [Windows Server バックアップと記憶域プール](http://technet.microsoft.com/library/dn390929.aspx)

SQL Server の構成のベスト プラクティスについては、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」を参照してください。


###SQL Server の設定

**[SQL Server の設定]** では、SQL Server の VM 名のプレフィックス、SQL Server のバージョン、SQL Server のサービス アカウントとパスワード、および SQL の自動修正メンテナンス スケジュールを確認および変更できます。

- **[SQL Server Name Prefix (SQL Server 名のプレフィックス)]** は、各 SQL Server の名前を作成するために使用します。このチュートリアルでは **Contoso-ag** を使用します。SQL Server 名は、*Contoso-ag-0* と *Contoso-ag-1* になります。

- **[SQL Server のバージョン]** は、SQL Server のバージョンです。このチュートリアルでは **[SQL Server 2014]** を使用します。**[SQL Server 2012]** または **[SQL Server 2016]** を選択することもできます。

- **[SQL Server service account user name (SQL Server サービス アカウント ユーザー名)]** は、SQL Server サービスのドメイン アカウント名です。このチュートリアルでは **sqlservice** を使用します。

- **[パスワード]** は、SQL Server サービス アカウントのパスワードです。複雑なパスワードを使用します。パスワードを確認入力します。

- **[SQL Auto Patching maintenance schedule (SQL 自動修正メンテナンス スケジュール)]** は、Azure が SQL Server に修正を自動的に適用する曜日です。このチュートリアルでは、「**日曜日**」と入力します。

- **[SQL Auto Patching maintenance start hour (SQL 自動修正メンテナンスの開始時間)]** は、Azure リージョンに自動修正が開始される時間です。

>[AZURE.NOTE]各 VM の修正時間は 1 時間に調整されています。一度に 1 つの仮想マシンにのみ修正が適用され、サービスの中断を防いでいます。

![SQL Server の設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

設定を確認し、**[OK]** をクリックします。

###概要

[概要] ページでは Azure によって設定が検証されます。テンプレートをダウンロードすることもできます。概要を確認します。**[OK]** をクリックします。

###購入

この最後のブレードには **[使用条件]** と **[プライバシー ポリシー]** があります。この情報を確認します。Azure で仮想マシンと可用性グループに必要なその他のすべてのリソースを作成開始する準備ができたら **[作成]** をクリックします。

Azure ポータルによって、リソース グループとすべてのリソースが作成されます。

##デプロイの監視

Azure ポータルでデプロイの進捗状況を監視できます。デプロイを表すアイコンが、Azure ポータルのダッシュボードに自動的に固定されます。

![Azure ダッシュ ボード](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##SQL Server への接続

SQL Server の新しいインスタンスは、インターネットに接続されていない仮想マシンで実行されます。ただし、ドメイン コントローラーにはインターネット接続があります。リモート デスクトップを使用して SQL サーバーに接続するには、まずドメイン コントローラーのいずれかに RDP 接続を実行します。ドメイン コントローラーで SQL Server に 2 つ目の RDP 接続を開きます。

プライマリ ドメイン コントローラーに RDP 接続するには、次の手順に従います。

1.	Azure ポータルのダッシュ ボードで、デプロイが成功したことを確認します。

1.	**[リソース]** をクリックします。

1.	**[リソース]** ブレードで、プライマリ ドメイン コントローラーの仮想マシンのコンピューター名である **[ad-primary-dc]** をクリックします。

1.	**ad-primary-dc** のブレードで、**[接続]** をクリックします。リモート接続オブジェクトを開くか保存するか、ブラウザーによって求められます。**[開く]** をクリックします。 ![DC への接続](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.	**リモート デスクトップ接続**で、このリモート接続の発行元が識別できないことが通知される場合があります。**[接続]** をクリックします。

1.	Windows のセキュリティによって、プライマリ ドメイン コントローラーの IP アドレスに接続するための資格情報の入力が求められます。**[Use another account (別のアカウントを使用する)]** をクリックします。**[ユーザー名]** に「**contoso\\DomainAdmin**」と入力します。これが管理者のユーザー名に選択するアカウントです。テンプレートを構成したときに選択した複雑なパスワードを使用します。

1.	**リモート デスクトップ**により、セキュリティ証明書の問題のためこのリモート コンピューターを認証できなかったという警告が表示される場合があります。そのセキュリティ証明書の名前が表示されます。チュートリアルに従った場合、名前は **ad-primary-dc.contoso.com** になります。**[はい]** をクリックします。

これでプライマリ ドメイン コントローラーに接続されました。SQL Server に RDP 接続するには、次の手順に従います。

1.	ドメイン コントローラーで **[リモート デスクトップ接続]** を開きます。

1.	**[コンピューター]** には、いずれかの SQL Server の名前を入力します。このチュートリアルでは、「**sqlserver-0**」と入力します。

1.	ドメイン コントローラーへの RDP 接続に使用したのと同じユーザー アカウントとパスワードを使用します。

これで SQL Server に RDP 接続できました。これで SQL Server Management Studio を開き、SQL Server の既定のインスタンスに接続し、可用性グループが構成済みであることを確認できます。

<!---HONumber=AcomDC_0601_2016-->
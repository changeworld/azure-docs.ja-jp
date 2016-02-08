<properties 
	pageTitle="SharePoint 2013 ファームのテスト環境 | Microsoft Azure" 
	description="開発または IT プロのテスト用のハイブリッド クラウド環境で 2 層の SharePoint 2013 イントラネット ファームを作成する方法について説明します。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/10/2015" 
	ms.author="josephd"/>

# テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。
 

このトピックでは、Microsoft Azure でホストされる SharePoint イントラネット ファームをテストするためにハイブリッド クラウド環境を作成する手順について説明します。完成すると次のような構成になります。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
この構成では、インターネット上の自分の場所から Azure 運用環境の SharePoint をシミュレートします。構成は次のとおりです。

- 簡略化されたオンプレミス ネットワーク (Corpnet サブネット)。
- Microsoft Azure でホストされているクロスプレミスの仮想ネットワーク (TestVNET)
- サイト間 VPN 接続
- TestVNET 仮想ネットワーク内に 2 層 SharePoint ファームとセカンダリ ドメイン コントローラー

この構成を基盤や共通の出発点にして以下を実行できます。

- ハイブリッド クラウド環境の SharePoint イントラネット ファームでアプリケーションを開発してテストする。
- このハイブリッド クラウドをベースとした IT ワークロードのテストを実行する。

このハイブリッド クラウド テスト環境を設定する手順は、大きく次の 3 つのフェーズに分かれています。

1.	テスト用のハイブリッド クラウド環境を設定する。
2.	SQL Server コンピューター (SQL1) を構成する。
3.	SharePoint サーバー (SP1) を構成する。

Azure サブスクリプションを持っていない場合は、[Azure の無料試用版のページ](https://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップすることもできます。MSDN サブスクリプションをお持ちの場合は、「[MSDN サブスクライバー向けの Azure の特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

## フェーズ 1: ハイブリッド クラウド環境を設定する

「[テスト用のハイブリッド クラウド環境の設定](virtual-networks-setup-hybrid-cloud-environment-testing.md)」の指示に従います。このテスト環境では Corpnet サブネット上に APP1 サーバーを配置する必要がないため、シャットダウンしてかまいません。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_1.png)

> [AZURE.NOTE] フェーズ 1 では、シミュレートされたハイブリッド クラウド テスト環境を設定することもできます。方法については、「[テスト用のシミュレートされたハイブリッド クラウド環境の設定](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)」をご覧ください。
 
## フェーズ 2: SQL Server コンピューター (SQL1) を構成する

DC2 コンピューターが起動されていない場合は、Microsoft Azure 管理ポータルから起動します。

まず、CORP\\User1 の資格情報を使用して DC2 へのリモート デスクトップ接続を作成します。

次に、SharePoint ファーム管理者アカウントを作成します。DC2 で管理者レベルの Windows PowerShell プロンプトを開き、次のコマンドを実行します。

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

SPFarmAdmin アカウント パスワードを指定するよう求められたら、強力なパスワードを入力し、そのメモを安全な場所に保管します。

続いて、ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行して、SQL1 用に Azure 仮想マシンを作成します。


	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

次に、*ローカル管理者アカウントを使用して*新しい SQL1 仮想マシンに接続します。

1.	Microsoft Azure 管理ポータルの左側のウィンドウで **[仮想マシン]** をクリックし、SQL1 の [状態] 列で **[実行中]** をクリックします。
2.	タスク バーで、**[接続]** をクリックします。 
3.	SQL1.rdp を開くよう求められたら、**[開く]** をクリックします。
4.	リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.	資格情報の入力を求められたら、次の情報を使用します。
	- 名前: **SQL1**[ローカル管理者アカウントの名前]
	- パスワード: [ローカル管理者アカウントのパスワード]
6.	証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

続いて、基本的な接続テストと SQL Server のトラフィックを許可するように Windows ファイアウォールの規則を構成します。SQL1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

ping コマンドで IP アドレス 10.0.0.1 からの応答が 4 回成功する必要があります。

次に、余っているデータ ディスクを新しいボリュームとして追加し、ドライブ文字 F: を割り当てます。

1.	サーバー マネージャーの左側のウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2.	コンテンツ ウィンドウの **[ディスク]** グループで、(**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3.	**[タスク]** をクリックし、**[ボリューム]** をクリックします。
4.	新しいボリューム ウィザードの [開始する前に] ページで **[次へ]** をクリックします。
5.	[サーバーとディスクの選択] ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。メッセージが表示されたら、**[OK]** をクリックします。
6.	[ボリュームのサイズの指定] ページで、**[次へ]** をクリックします。
7.	[ドライブ文字またはフォルダーへの割り当て] ページで、**[次へ]** をクリックします。
8.	[ファイル システム形式の選択] ページで、**[次へ]** をクリックします。
9.	[選択内容の確認] ページで、**[作成]** をクリックします。
10.	作成されたら、**[閉じる]** をクリックします。

SQL1 の Windows PowerShell コマンド プロンプトで、次のコマンドを実行します。

	md f:\Data
	md f:\Log
	md f:\Backup

次に、新しいデータベースとユーザー アカウント アクセス許可に F: ドライブを使用するように SQL Server 2014 を構成します。

1.	スタート画面で「**SQL Server Management**」と入力し、**[SQL Server 2014 Management Studio]** をクリックします。
2.	**[サーバーに接続]** で、**[接続]** をクリックします。
3.	[オブジェクト エクスプローラー] ツリー ウィンドウで、**[SQL1]** を右クリックし、**[プロパティ]** をクリックします。
4.	**[サーバーのプロパティ]** ウィンドウで、**[データベースの設定]** をクリックします。
5.	**[データベースの既定の場所]** で、次の値を設定します。 
	- **[データ]** に、パス「**f:\Data**」を入力します。
	- **[ログ]** に、パス「**f:\Log**」を入力します。
	- **[バックアップ]** に、パス「**f:\Backup**」を入力します。
	- 新しいデータベースでのみ、これらの場所が使用されます。
6.	**[OK]** をクリックして、ウィンドウを閉じます。
7.	**[オブジェクト エクスプローラー]** ツリー ウィンドウで、**[セキュリティ]** を開きます。
8.	**[ログイン]** を右クリックし、**[新しいログイン]** をクリックします。
9.	**[ログイン名]** に「**CORP\User1**」と入力します。
10.	**[サーバーの役割]** ページで、**[sysadmin]** をクリックし、**[OK]** をクリックします。
11.	**[オブジェクト エクスプローラー]** ツリー ウィンドウで、**[ログイン]** を右クリックし、**[新しいログイン]** をクリックします。
12.	**[全般]** ページの **[ログイン名]** に、「**CORP\SPFarmAdmin**」と入力します。
13.	**[サーバーの役割]** ページで、**[dbcreator]** を選択し、**[OK]** をクリックします。
14.	Microsoft SQL Server Management Studio を閉じます。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_2.png)

 
## フェーズ 3: SharePoint サーバー (SP1) を構成する

まず、ローカル コンピューターの Azure PowerShell コマンド プロンプトで次のコマンドを使用して、SP1 用に Azure 仮想マシンを作成します。

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SP1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

次に、CORP\User1 の資格情報を使用して、SP1 仮想マシンに接続します。

続いて、基本的な接続テストを行うためのトラフィックを許可するよう Windows ファイアウォール規則を構成します。SP1 で管理者レベルの Windows PowerShell プロンプトから、次のコマンドを実行します。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

ping コマンドで IP アドレス 10.0.0.1 からの応答が 4 回成功する必要があります。

次に、新しい SharePoint ファームと既定のチーム サイト向けに SP1 を構成します。

1.	スタート画面で、「**SharePoint 2013 製品**」と入力し、**[SharePoint 2013 製品構成ウィザード]** をクリックします。プログラムがコンピューターに変更を加えることを許可するよう求められたら、**[はい]** をクリックします。
2.	[SharePoint 製品へようこそ] ページで **[次へ]** をクリックします。 
3.	一部のサービスでは構成中に再起動が必要になる場合があることを伝えるダイアログ ボックスが表示されたら、**[はい]** をクリックします。
4.	[サーバー ファームへの接続] ページで、**[新しいサーバー ファームの作成]**、**[次へ]** を順にクリックします。
5.	[構成データベースの設定] ページで、**[データベース サーバー]** に「**sql1.corp.contoso.com**」、**[ユーザー名]** に「**CORP\\SPFarmAdmin**」、**[パスワード]** に SPFarmAdmin アカウントのパスワードをそれぞれ入力し、**[次へ]** をクリックします。
6.	[ファームのセキュリティ設定の指定] ページで、**[パスフレーズ]** と **[パスフレーズの確認入力]** の両方に 「****P@ssphrase**」 と入力し、**[次へ]** をクリックします。
7.	[SharePoint サーバーの全体管理 Web アプリケーションの構成] ページで、**[次へ]** をクリックします。
8.	[SharePoint 製品構成ウィザードの終了] ページで、**[次へ]** をクリックします。SharePoint 製品構成ウィザードは、完了するまで数分かかる場合があります。
9.	[構成成功] ページで **[完了]** をクリックします。完了後、Internet Explorer を起動すると、[ファーム構成の初期設定ウィザード] というタブが表示されます。
10.	**[SharePoint の品質向上にご協力ください]** ダイアログ ボックスで、**[参加しない]** をクリックしてから **[OK]** をクリックします。
11.	**[SharePoint ファームの構成方法を指定してください。]** で **[ウィザードの開始]** をクリックします。
12.	[SharePoint ファームの構成] ページの **[サービス アカウント]** で、**[既存の管理アカウントを使用する]** をクリックします。
13.	**[サービス]** で、**[State Service]** の横にあるチェック ボックス以外のすべてのチェック ボックスをオフにし、**[次へ]** をクリックします。完了するまで、[ただいま処理中です] ページがしばらく表示されることがあります。
14.	[サイト コレクションの作成] ページの **[タイトルと説明]** で、**[タイトル]** に「**Contoso Corporation**」と入力し、URL「**http://sp1**/」を指定して、**[OK]** をクリックします。完了するまで、[ただいま処理中です] ページがしばらく表示されることがあります。これにより、URL http://sp1 にチーム サイトが作成されます。
15.	[これでファーム構成ウィザードは完了です。] ページで、**[完了]** をクリックします。Internet Explorer のタブに SharePoint 2013 サーバーの全体管理サイトが表示されます。
16.	CORP\User1 アカウントの資格情報を使用して CLIENT1 コンピューターにログオンし、Internet Explorer を起動します。
17.	アドレス バーに「**http://sp1/**」と入力し、Enter キーを押します。Contoso Corporation の SharePoint チーム サイトが表示されます。サイトが表示されるまで時間がかかることがあります。  

現在の構成は次のようになります。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
これで、ハイブリッド クラウド環境の SharePoint イントラネット ファームをテストする準備が整いました。

## その他のリソース

[Azure インフラストラクチャ サービスでの SharePoint](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[SharePoint サーバー ファーム](../virtual-machines/virtual-machines-sharepoint-farm-azure-preview.md)

[テスト用のハイブリッド クラウド環境の設定](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[テスト用のハイブリッド クラウドでの Office 365 ディレクトリ同期 (DirSync) の設定](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[テスト用のシミュレートされたハイブリッド クラウド環境の設定](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure ハイブリッド クラウド テスト環境](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Azure インフラストラクチャ サービス実装ガイドライン](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=AcomDC_0128_2016-->
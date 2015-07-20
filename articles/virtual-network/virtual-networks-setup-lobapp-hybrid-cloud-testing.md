<properties 
	pageTitle="テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定" 
	description="IT プロまたは開発のテスト用のハイブリッド クラウド環境で Web ベースの基幹業務アプリケーションを作成する方法について説明します。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定

このトピックでは、Microsoft Azure でホストされるイントラネット基幹業務 (LOB) アプリケーションをテストするためにハイブリッド クラウド環境を作成する手順について説明します。完成すると次のような構成になります。

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)

Azure でホストされる運用 LOB アプリケーションの例については、「[アーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)」で**基幹業務アプリケーション**のアーキテクチャ ブループリントをご覧ください。

この構成では、インターネット上の自分の場所から Azure 運用環境の LOB アプリケーションをシミュレートします。構成は次のとおりです。

- 簡略化されたオンプレミス ネットワーク (Corpnet サブネット)。
- Azure でホストされているクロスプレミス仮想ネットワーク (TestVNET)。
- サイト間 VPN 接続
- TestVNET 仮想ネットワーク内に基幹業務サーバー、SQL Server、セカンダリ ドメイン コントローラー

この構成を基盤や共通の出発点にして以下を実行できます。

- Azure の SQL Server 2014 データベース バックエンドを使用してインターネット インフォメーション サービス (IIS) でホストされている LOB アプリケーションを開発し、テストする。
- このハイブリッド クラウドをベースとした IT ワークロードのテストを実行する。

このハイブリッド クラウド テスト環境を設定する手順は、大きく次の 3 つのフェーズに分かれています。

1.	テスト用のハイブリッド クラウド環境を設定する。
2.	SQL Server コンピューター (SQL1) を構成する。
3.	LOB サーバー (LOB1) を構成する。

Azure サブスクリプションを持っていない場合は、[Azure の無料試用版のページ](http://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップすることもできます。MSDN サブスクリプションをお持ちの場合は、「[MSDN サブスクライバー向けの Azure の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

## フェーズ 1: ハイブリッド クラウド環境を設定する

「[テスト用のハイブリッド クラウド環境の設定](virtual-networks-setup-hybrid-cloud-environment-testing.md)」の指示に従います。このテスト環境では Corpnet サブネット上に APP1 サーバーを配置する必要がないため、シャットダウンしてかまいません。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_1.png)

> [AZURE.NOTE]フェーズ 1 では、シミュレートされたハイブリッド クラウド テスト環境を設定することもできます。方法については、「[テスト用のシミュレートされたハイブリッド クラウド環境の設定](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)」をご覧ください。
 
## フェーズ 2: SQL Server コンピューター (SQL1) を構成する

DC2 コンピューターが起動されていない場合は、Azure 管理ポータルから起動します。

続いて、ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行して、SQL1 用に Azure 仮想マシンを作成します。これらのコマンドを実行する前に、変数の値を入力し、< and > の文字を削除します。

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount –StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

次に、*ローカル管理者アカウントを使用して*新しい SQL1 仮想マシンに接続します。

1.	Azure 管理ポータルの左側のウィンドウで **[仮想マシン]** をクリックし、SQL1 の [状態] 列で **[実行中]** をクリックします。
2.	タスク バーで、**[接続]** をクリックします。 
3.	SQL1.rdp を開くよう求められたら、**[開く]** をクリックします。
4.	リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.	資格情報の入力を求められたら、次の情報を使用します。
	- 名前: **SQL1\**[ローカル管理者アカウントの名前]
	- パスワード: [ローカル管理者アカウントのパスワード]
6.	証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

続いて、基本的な接続テストと SQL Server のトラフィックを許可するように Windows ファイアウォールの規則を構成します。SQL1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	New-NetFirewallRule -DisplayName “SQL Server” -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action allow 
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
10.	完了したら、**[閉じる]** をクリックします。

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
	- **[データ]** に、パス「**f:\\Data**」を入力します。
	- **[ログ]** に、パス「**f:\\Log**」を入力します。
	- **[バックアップ]** に、パス「**f:\\Backup**」を入力します。
	- 注: 新しいデータベースでのみ、これらの場所が使用されます。
6.	**[OK]** をクリックして、ウィンドウを閉じます。
7.	**[オブジェクト エクスプローラー]** ツリー ウィンドウで、**[セキュリティ]** を開きます。
8.	**[ログイン]** を右クリックし、**[新しいログイン]** をクリックします。
9.	**[ログイン名]** に「**CORP\\User1**」と入力します。
10.	**[サーバーの役割]** ページで、**[sysadmin]** をクリックし、**[OK]** をクリックします。
11.	Microsoft SQL Server Management Studio を閉じます。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_2.png)
 
## フェーズ 3: LOB サーバー (LOB1) を構成する

まず、ローカル コンピューターの Azure PowerShell コマンド プロンプトで次のコマンドを使用して、LOB1 用に Azure 仮想マシンを作成します。

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for LOB1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name LOB1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

次に、CORP\\User1 アカウントの資格情報を使用して、LOB1 仮想マシンに接続します。

続いて、基本的な接続テストを行うためのトラフィックを許可するよう Windows ファイアウォール規則を構成します。LOB1 で管理者レベルの Windows PowerShell コマンド プロンプトから、次のコマンドを実行します。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

ping コマンドで IP アドレス 10.0.0.1 からの応答が 4 回成功する必要があります。

次に、IIS 用に LOB1 を構成し、CLIENT1 からアクセスをテストします。

1.	サーバー マネージャーを実行し、**[役割と機能の追加]** をクリックします。
2.	[開始する前に] ページで **[次へ]** をクリックします。
3.	[インストールの種類の選択] ページで **[次へ]** をクリックします。
4.	[対象サーバーの選択] ページで **[次へ]** をクリックします。
5.	[サーバーの役割] ページの **[役割]** の一覧で、**[Web サーバー (IIS)]** をクリックします。
6.	メッセージが表示されたら、**[機能の追加]** をクリックし、**[次へ]** をクリックします。
7.	[機能の選択] ページで **[次へ]** をクリックします。
8.	[Web サーバー (IIS)] ページで **[次へ]** をクリックします。
9.	[役割サービスの選択] ページで、LOB アプリケーションをテストするための必要に応じて、サービスのチェック ボックスをオンまたはオフにし、**[次へ]** をクリックします。
10.	[インストール オプションの確認] ページで、**[インストール]** をクリックします。
11.	コンポーネントのインストールが完了するまで待ってから、**[閉じる]** をクリックします。
12.	CORP\\User1 アカウントの資格情報を使用して CLIENT1 コンピューターにログオンし、Internet Explorer を起動します。
13.	アドレス バーに「**http://lob1/**」と入力し、Enter キーを押します。既定の IIS 8 Web ページが表示されます。
現在の構成は次のようになります。

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)
 
この環境で、Web ベース アプリケーションを LOB1 に展開し、Corpnet サブネットから機能とパフォーマンスをテストできます。

## その他のリソース

[Microsoft ソフトウェア アーキテクチャのダイアグラムとブループリント](http://msdn.microsoft.com/dn630664)

[ホストが容易な Web サーバー プラットフォーム (IIS)](http://technet.microsoft.com/library/hh831818)

[テスト用のハイブリッド クラウド環境の設定](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[テスト用のハイブリッド クラウドでの Office 365 ディレクトリ同期 (DirSync) の設定](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[テスト用のシミュレートされたハイブリッド クラウド環境の設定](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure ハイブリッド クラウド テスト環境](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Azure インフラストラクチャ サービス実装ガイドライン](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->
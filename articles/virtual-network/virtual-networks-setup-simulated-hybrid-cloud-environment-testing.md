<properties 
	pageTitle="シミュレートされたハイブリッド クラウドのテスト環境 | Microsoft Azure" 
	description="2 つの Azure 仮想ネットワークと 1 つの VNet 間接続を使用して、IT プロや開発テスト用のシミュレートされたハイブリッド クラウド環境を作成します。" 
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
	ms.date="03/28/2016" 
	ms.author="josephd"/>

# シミュレートされたテスト用ハイブリッド クラウド環境のセットアップ (クラシック デプロイ モード)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](../virtual-machines/virtual-machines-setup-simulated-hybrid-cloud-environment-testing.md).

この記事では、2 つの Azure 仮想ネットワークを使用して、Microsoft Azure でシミュレートされたテスト用のハイブリッド クラウド環境を作成する手順について説明します。インターネットに直接接続できず、利用可能なパブリック IP アドレスがない場合は、「[テスト用のハイブリッド クラウド環境の設定](virtual-networks-setup-hybrid-cloud-environment-testing.md)」の代わりに、この構成を使用してください。完成すると次のような構成になります。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

この構成は、ハイブリッド クラウド運用環境をシミュレートしています。構成は次のとおりです。

- Azure 仮想ネットワークでホストされているシミュレートされ、簡素化されたオンプレミス ネットワーク (TestLab 仮想ネットワーク)。
- Azure でホストされているシミュレートされたクロスプレミス仮想ネットワーク (TestVNET)。
- 2 つの仮想ネットワークをつなぐ VNet 間接続。
- TestVNET 仮想ネットワークのセカンダリ ドメイン コントローラー。

この構成を基盤や共通の出発点にして以下を実行できます。

- シミュレートされたハイブリッド クラウド環境でアプリケーションを開発およびテストする。
- コンピューターのテスト構成 (TestLab 仮想ネットワークと TestVNET 仮想ネットワーク内にそれぞれコンピューターを配置) を作成して、ハイブリッド クラウド ベースの IT ワークロードをシミュレートする。

このハイブリッド クラウド テスト環境を設定する手順は、大きく次の 4 つのフェーズに分かれています。

1.	TestLab 仮想ネットワークを構成する。
2.	クロスプレミス仮想ネットワークを作成する。
3.	VNet 間 VPN 接続を作成する。
4.	DC2 を構成する。 

Azure サブスクリプションをまだ取得していない場合は、[Azure の無料試用版のページ](https://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップすることもできます。MSDN サブスクリプションをお持ちの場合は、「[MSDN サブスクライバー向けの Azure の特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

>[AZURE.NOTE] Azure の仮想マシンと仮想ネットワーク ゲートウェイは、稼働していると継続的に費用が発生します。その費用は、無料試用版、MSDN サブスクリプション、または有料のサブスクリプションに対して請求されます。このテスト環境を使用していないときに稼働費用を削減する方法の詳細については、この記事の「[この環境の継続的な費用を最小限に抑える](#costs)」をご覧ください。


## フェーズ 1: TestLab 仮想ネットワークを構成する

「[基本構成テスト環境](../virtual-machines/virtual-machines-windows-classic-test-config-env.md)」の手順を使用して、TestLab という Azure Virtual Network で DC1、APP1、および CLIENT1 の各コンピューターを構成します。

ローカル コンピューターで Microsoft Azure 管理ポータルから CORP\\User1 の資格情報を使用して DC1 に接続します。コンピューターとユーザーが認証にローカル ドメイン コントローラーを使用するよう CORP ドメインを構成するために、管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

現在の構成は次のようになります。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## フェーズ 2: TestVNET 仮想ネットワークを作成する

まず、TestVNET という名前の新しい仮想ネットワークを作成します。

1.	Microsoft Azure 管理ポータルのタスク バーで、**[新規]、[Network Services]、[Virtual Network]、[カスタム作成]** の順にクリックします。
2.	[Virtual Network の詳細] ページで、**[名前]** に「**TestVNET**」と入力します。
3.	**[場所]** で、適切な場所を選択します。
4.	次へ進む矢印をクリックします。
5.	[DNS サーバーおよび VPN 接続] ページで、**[DNS サーバー]** の **[名前の選択または入力]** に「**DC1**」と入力し、次へ進む矢印をクリックします。
6.	[Virtual Network アドレス空間] ページで、次の内容を構成します。
	- **[アドレス空間]** の **[開始 IP]** で、「**192.168.0.0**」を選択または入力します。
	- **[サブネット]** で、**[Subnet-1]** をクリックし、名前を「**TestSubnet**」に変更します。 
	- TestSubnet の **[CIDR (アドレス数)]** 列で、**[/24 (256)]** をクリックします。
7.	[完了] アイコンをクリックします。仮想ネットワークが作成されるまで待ってから、次に進みます。

次に、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」に記載されている手順に従って、ローカル コンピューターに Azure PowerShell をインストールします

次に、TestVNET 仮想ネットワークの新しいクラウド サービスを作成します。一意の名前を選ぶ必要があります。たとえば、**TestVNET-***UniqueSequence* という名前を付けることができます。*UniqueSequence* は組織の略称です。たとえば、組織の名前が Tailspin Toys であれば、クラウド サービスに **TestVNET-Tailspin** という名前を付けることができます。

名前が一意かどうかは、ローカル コンピューターで次の Azure PowerShell コマンドを使用することで確認できます。

	Test-AzureName -Service <Proposed cloud service name>

このコマンドで "False" が返された場合は、提案した名前は一意です。次のコマンドでクラウド サービスを作成します。

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

現在の構成は次のようになります。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##フェーズ 3: VNet 間接続を作成する

まず、各仮想ネットワークのアドレス空間を表すローカル ネットワークを作成します。

1.	ローカル コンピューターで Microsoft Azure 管理ポータルから **[新規]、[Network Services]、[Virtual Network]、[ローカル ネットワークの追加]** の順にクリックします。
2.	[ローカル ネットワークの詳細を指定する] ページで、**[名前]** に「**TestLabLNet**」、**[VPN デバイスの IP アドレス]** に「**131.107.0.1**」と入力し、右矢印をクリックします。
3.	[アドレス空間を指定します] ページで、**[開始 IP]** に「**10.0.0.0**」と入力します。
4.	**[CIDR (アドレス数)]** 列で **[/24 (256)]** をクリックし、チェック マークをクリックします。
5.	**[新規]、[Network Services]、[Virtual Network]、[ローカル ネットワークの追加]** の順にクリックします。
6.	[ローカル ネットワークの詳細を指定する] ページで、**[名前]** に「**TestVNETLNet**」、**[VPN デバイスの IP アドレス]** に「**131.107.0.2**」と入力し、右矢印をクリックします。
7.	[アドレス空間を指定します] ページで、**[開始 IP]** に「**192.168.0.0**」と入力します。
8.	**[CIDR (アドレス数)]** 列で **[/24 (256)]** をクリックし、チェック マークをクリックします。

VPN デバイスの IP アドレス 131.107.0.1 と 131.107.0.2 は、2 つの仮想ネットワーク用のゲートウェイを構成するまでの一時的なプレースホルダー値です。

次に、サイト間 VPN 接続と、もう一方の仮想ネットワークに対応するローカル ネットワークを使用するように各仮想ネットワークを構成します。

1.	ローカル コンピューターで Microsoft Azure 管理ポータルから左側のウィンドウの **[ネットワーク]** をクリックし、**TestLab** の **[状態]** 列が **[作成済み]** に設定されていることを確認します。
2.	**[TestLab]** をクリックし、**[構成]** をクリックします。[TestLab] ページの **[サイト間接続]** セクションで、**[ローカル ネットワークに接続する]** をクリックします。 
3.	**[ローカル ネットワーク]** で **[TestVNETLNet]** を選択します。
4.	メニュー バーで **[保存]** をクリックします。場合によっては、**[ゲートウェイ サブネット]** をクリックすることによって、Azure VPN Gateway で使用されるサブネットを作成することが必要です。
5.	左側のウィンドウで **[ネットワーク]** をクリックし、TestVNET の **[状態]** 列が **[作成済み]** に設定されていることを確認します。
6.	**[TestVNET]** をクリックし、**[構成]** をクリックします。[TestVNET] ページの **[サイト間接続]** セクションで、**[ローカル ネットワークに接続する]** をクリックします。 
7.	**[ローカル ネットワーク]** で **[TestLabLNet]** を選択します。
8.	メニュー バーで **[保存]** をクリックします。場合によっては、**[ゲートウェイ サブネット]** をクリックすることによって、Azure VPN Gateway で使用されるサブネットを作成することが必要です。

次に、2 つの仮想ネットワーク用の仮想ネットワーク ゲートウェイを作成します。

1.	Microsoft Azure 管理ポータルの **[ネットワーク]** ページで、**[TestLab]** をクリックします。[ダッシュボード] ページに、**"ゲートウェイは作成されませんでした"** という状態が表示されます。
2.	タスク バーで **[ゲートウェイの作成]** をクリックし、**[動的ルーティング]** をクリックします。確認を求めるメッセージが表示されたら、**[はい]** をクリックします。ゲートウェイが作成され、ゲートウェイの状態が **"接続中"** に変わるまで待ちます。この処理には数分かかります。
3.	[ダッシュボード] ページで、**[ゲートウェイ IP アドレス]** の値をメモします。これは、TestLab 仮想ネットワーク用の Azure VPN Gateway のパブリック IP アドレスです。VNet 間接続を構成する際に必要になるため、この IP アドレスをメモします。
4.	タスク バーで、**[キーの管理]** をクリックし、キーの横にあるコピー アイコンをクリックして、クリップボードにコピーします。このキーをドキュメントに貼り付けて保存します。VNet 間接続を構成する際に、このキーの値が必要になります。
5.	[ネットワーク] ページで **[TestVNET]** をクリックします。[ダッシュボード] ページに、**"ゲートウェイは作成されませんでした"** という状態が表示されます。
6.	タスク バーで **[ゲートウェイの作成]** をクリックし、**[動的ルーティング]** をクリックします。確認を求めるメッセージが表示されたら、**[はい]** をクリックします。ゲートウェイが作成され、ゲートウェイの状態が **"接続中"** に変わるまで待ちます。この処理には数分かかります。
7.	[ダッシュボード] ページで、**[ゲートウェイ IP アドレス]** の値をメモします。これは、TestVNET 仮想ネットワーク用の Azure VPN Gateway のパブリック IP アドレスです。VNet 間接続を構成する際に必要になるため、この IP アドレスをメモします。

次に、仮想ネットワーク ゲートウェイを作成して取得したパブリック IP アドレスを使用して、TestLabLNet と TestVNETLNet ローカル ネットワークを構成します。

1.	Azure 管理ポータルの [ネットワーク] ページで、**[ローカル ネットワーク]** をクリックします。 
2.	**[TestLabLNet]** をクリックし、タスク バーの **[編集]** をクリックします。
3.	[ローカル ネットワークの詳細を指定する] ページで、**[VPN デバイスの IP アドレス (省略可能)]** に、(前の手順 3. の) TestLab 仮想ネットワーク用の仮想ネットワーク ゲートウェイの IP アドレスを入力し、右矢印をクリックします。
4.	[アドレス空間を指定します] ページで、チェック マークをクリックします。
5.	[ローカル ネットワーク] ページで、**[TestVNETLNet]** をクリックし、タスク バーの **[編集]** をクリックします。
6.	[ローカル ネットワークの詳細を指定する] ページで、**[VPN デバイスの IP アドレス (省略可能)]** に、(前の手順 7. の) TestVNET 仮想ネットワーク用の仮想ネットワーク ゲートウェイの IP アドレスを入力し、右矢印をクリックします。
7.	[アドレス空間を指定します] ページで、チェック マークをクリックします。

次に、両方のゲートウェイの事前共有キーを、同じ値 (Microsoft Azure 管理ポータルで TestLab 仮想ネットワークに対して自動的に決定されたキーの値) を使用するように構成します。ローカル コンピューターで管理者レベルの Azure PowerShell コマンド プロンプトから次のコマンドを実行して、TestLab の事前共有キーの値を設定します。

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

次に、ローカル コンピューターから Microsoft Azure 管理ポータルの [ネットワーク] ページで、**TestLab** 仮想ネットワークをクリックし、**[ダッシュボード]** をクリックして、タスク バーの **[接続]** をクリックします。TestLab 仮想ネットワークが接続された状態になるまで待ちます。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## フェーズ 4: DC2 を構成する

まず、DC2 用の Azure の仮想マシンを作成します。ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行します。

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

次に、新しい DC2 仮想マシンにログオンします。

1.	Microsoft Azure 管理ポータルの左側のウィンドウで、**[Virtual Machines]** をクリックし、DC2 の **[状態]** 列で **[実行中]** をクリックします。
2.	タスク バーで、**[接続]** をクリックします。 
3.	DC2.rdp を開くよう求められたら、**[開く]** をクリックします。
4.	リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.	資格情報の入力を求められたら、次の情報を使用します。
- 名前: **DC2\**[ローカル管理者アカウントの名前]
- パスワード: [ローカル管理者アカウントのパスワード]
6.	証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

続いて、基本的な接続テストを行うためのトラフィックを許可するよう Windows ファイアウォール規則を構成します。DC2 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

ping コマンドで IP アドレス 10.0.0.4 からの応答が 4 回成功する必要があります。これは VNet 間接続を介したトラフィックのテストです。

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

次に、DC2 を corp.contoso.com ドメインのレプリカ ドメイン コントローラーとして構成します。DC2 で Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

CORP\\User1 のパスワードとディレクトリ サービス復元モード (DSRM) のパスワードの両方を入力し、DC2 を再起動するよう求められます。

これで TestVNET 仮想ネットワーク独自の DNS サーバー (DC2) が設定されたので、この DNS サーバーを使用するように TestVNET 仮想ネットワークを構成する必要があります。

1.	Microsoft Azure 管理ポータルの左側のウィンドウで、**[ネットワーク]** をクリックし、**[TestVNET]** をクリックします。
2.	**[構成]** をクリックします。
3.	**[DNS サーバー]** で、10.0.0.4 のエントリを削除します。
4.	**[DNS サーバー]** で、名前が **DC2** で、IP アドレスが **192.168.0.4** のエントリを追加します。 
5.	下部のコマンド バーで **[保存]** をクリックし、確認を求めるメッセージが表示されたら **[はい]** をクリックします。TestVNet ネットワークの更新が完了するまで待ちます。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
これで、シミュレートされたハイブリッド クラウド環境でテストする準備が整いました。

## 次のステップ

- TestVNET 仮想ネットワークに、[SharePoint イントラネット ファーム](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)、[Web ベースの基幹業務アプリケーション](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)、または [Office 365 ディレクトリ同期 (DirSync) サーバー](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)をセットアップします。


## <a id="costs"></a>この環境の継続的な費用を最小限に抑える

この環境で稼働中の仮想マシンの費用を最小限に抑えるためには、できるだけ迅速に必要なテストとデモンストレーションを行ってからそれらの仮想マシンを削除するか、使用していない間は仮想マシンをシャットダウンします。たとえば、Azure Automation と Runbook を使用して、各営業日の終わりに TestLab と Test\_VNET 仮想ネットワーク内の仮想マシンを自動的にシャットダウンすることができます。企業ネットワーク サブネット上の仮想マシンを再度起動する場合は、最初に DC1 を起動します。

Azure VPN Gateway は、2 台 1 組みの Azure の仮想マシンとして実装されており、継続的な費用が発生します。詳細については、[Virtual Network 料金](https://azure.microsoft.com/pricing/details/virtual-network/)に関するページを参照してください。2 つの VPN Gateway (それぞれ TestLab 用と TestVNET 用) の費用を最小限に抑えるためには、テスト環境を作成し、できる限り迅速に必要なテストとデモンストレーションを行うか、次の手順でゲートウェイを削除します。
 
1.	ローカル コンピューターで Microsoft Azure 管理ポータルから左側のウィンドウの **[ネットワーク]** をクリックし、**[TestLab]**、**[ダッシュボード]** の順にクリックします。
2.	タスク バーで **[ゲートウェイの削除]** をクリックします。確認を求めるメッセージが表示されたら、**[はい]** をクリックします。ゲートウェイが削除され、ゲートウェイの状態が **[ゲートウェイは作成されませんでした]** に変わるまで待ちます。
3.	左側のウィンドウで **[ネットワーク]** をクリックし、**[TestVNET]**、**[ダッシュボード]** の順にクリックします。
4.	タスク バーで **[ゲートウェイの削除]** をクリックします。確認を求めるメッセージが表示されたら、**[はい]** をクリックします。ゲートウェイが削除され、ゲートウェイの状態が **[ゲートウェイは作成されませんでした]** に変わるまで待ちます。

ゲートウェイを削除した後で、このテスト環境を復元する場合は、まず新しいゲートウェイを作成する必要があります。

1.	ローカル コンピューターで Azure の管理ポータルから左側のウィンドウの **[ネットワーク]** をクリックし、**[TestLab]** をクリックします。[ダッシュボード] ページに、**"ゲートウェイは作成されませんでした"** という状態が表示されます。
2.	タスク バーで **[ゲートウェイの作成]** をクリックし、**[動的ルーティング]** をクリックします。確認を求めるメッセージが表示されたら、**[はい]** をクリックします。ゲートウェイが作成され、ゲートウェイの状態が **"接続中"** に変わるまで待ちます。この処理には数分かかります。
3.	[ダッシュボード] ページで、**[ゲートウェイ IP アドレス]** の値をメモします。これは、TestLab 仮想ネットワーク用の Azure VPN Gateway の新しいパブリック IP アドレスです。この IP アドレスは、TestLabLNet ローカル ネットワークを再構成するために必要です。
4.	タスク バーで、**[キーの管理]** をクリックし、キーの横にあるコピー アイコンをクリックして、クリップボードにコピーします。このキーの値をドキュメントに貼り付けて保存します。このキーの値は、TestVNET 仮想ネットワーク用の VPN Gateway を再構成するために必要です。
5.	ローカル コンピューターで Microsoft Azure 管理ポータルから左側のウィンドウの **[ネットワーク]** をクリックし、**[TestVNET]** をクリックします。[ダッシュボード] ページに、**"ゲートウェイは作成されませんでした"** という状態が表示されます。
6.	タスク バーで **[ゲートウェイの作成]** をクリックし、**[動的ルーティング]** をクリックします。確認を求めるメッセージが表示されたら、**[はい]** をクリックします。ゲートウェイが作成され、ゲートウェイの状態が [接続中] に変わるまで待ちます。この処理には数分かかります。
7.	[ダッシュボード] ページで、**[ゲートウェイ IP アドレス]** の値をメモします。これは、TestVNET 仮想ネットワーク用の Azure VPN Gateway の新しいパブリック IP アドレスです。この IP アドレスは、TestVNETLNet ローカル ネットワークを再構成するために必要です。

次に、仮想ネットワーク ゲートウェイを作成して取得した新しいパブリック IP アドレスを使用して、TestLabLNet と TestVNETLNet ローカル ネットワークを構成します。

1.	Azure 管理ポータルの [ネットワーク] ページで、**[ローカル ネットワーク]** をクリックします。 
2.	**[TestLabLNet]** をクリックし、タスク バーの **[編集]** をクリックします。
3.	[ローカル ネットワークの詳細を指定する] ページで、**[VPN デバイスの IP アドレス (省略可能)]** に、(前の手順 3. の) TestLab 仮想ネットワーク用の仮想ネットワーク ゲートウェイの IP アドレスを入力し、右矢印をクリックします。
4.	[アドレス空間を指定します] ページで、チェック マークをクリックします。
5.	[ローカル ネットワーク] ページで、**[TestVNETLNet]** をクリックし、タスク バーの **[編集]** をクリックします。
6.	[ローカル ネットワークの詳細を指定する] ページで、**[VPN デバイスの IP アドレス (省略可能)]** に、(前の手順 7. の) TestVNET 仮想ネットワーク用の仮想ネットワーク ゲートウェイの IP アドレスを入力し、右矢印をクリックします。
7.	[アドレス空間を指定します] ページで、チェック マークをクリックします。

次に、両方のゲートウェイの事前共有キーを、同じ値 (Microsoft Azure 管理ポータルで TestLab 仮想ネットワークに対して自動的に決定されたキーの値) を使用するように構成します。ローカル コンピューターで管理者レベルの Azure PowerShell コマンド プロンプトから次のコマンドを実行して、TestLab の事前共有キーの値を設定します。

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

次に、Azure の管理ポータルの [ネットワーク] ページで、**TestLab** 仮想ネットワークをクリックし、タスク バーの **[接続]** をクリックします。TestLab 仮想ネットワークが TestVNET ローカル ネットワークに接続された状態になるまで待ちます。

<!---HONumber=AcomDC_0413_2016-->
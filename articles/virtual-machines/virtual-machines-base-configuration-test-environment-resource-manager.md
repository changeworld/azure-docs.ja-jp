<properties
	pageTitle="Azure リソース マネージャーでの基本構成テスト環境"
	description="イントラネットをシミュレーションする単純な開発/テスト環境を Microsoft Azure のリソース マネージャーを使用して作成する方法について説明します。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="josephd"/>

# Azure リソース マネージャーでの基本構成テスト環境

この記事では、リソース マネージャーで作成された仮想マシンコンピューターを使用し、Microsoft Azure Virtual Network 内に基本構成テスト環境を作成する手順について説明します。

完成したテスト環境は、次の用途に使うことができます。

- アプリケーションの開発とテストを行う。
- 追加の仮想マシンと Azure サービスを含む独自設計の拡張テスト環境の初期構成。

基本構成テスト環境は、TestLab という、クラウドのみの仮想ネットワーク内の Corpnet サブネットから成ります。インターネットに接続された単純なプライベート イントラネットが TestLab によってシミュレーションされます。

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

その構成要素を次に示します。

- Azure Virtual Machines (×1)。イントラネットのドメイン コントローラーおよびドメイン ネーム システム (DNS) サーバーとして構成された、DC1 という名前の Windows Server 2012 R2 を実行します。
- Azure Virtual Machines (×1)。一般的なアプリケーション/Web サーバーとして構成された、APP1 という名前の Windows Server 2012 R2 を実行します。
- Azure Virtual Machines (×1)。CLIENT1 という名前の Windows Server 2012 R2 を実行し、イントラネットのクライアントとして機能します。

この構成では、DC1、APP1、CLIENT1 をはじめとする、Corpnet サブネットに属しているコンピューターに対して、次のことを実行できます。

- インターネットに接続して更新プログラムをインストールする、インターネット リソースにリアルタイムでアクセスする、パブリック クラウド テクノロジ (Microsoft Office 365、各種 Azure サービスなど) を利用する。
- インターネットや社内のネットワークに接続されている遠隔コンピューターからリモート デスクトップ接続を介して管理する。

Windows Server 2012 R2 基本構成テスト環境の Corpnet サブネットを Azure にセットアップする手順は、次の 4 つのフェーズから成ります。

1.	仮想ネットワークを作成する。
2.	DC1 を構成する。
3.	APP1 を構成する。
4.	CLIENT1 を構成する。

まだ Azure アカウントがない場合は、[こちら](http://azure.microsoft.com/pricing/free-trial/)から無料評価版にサインアップしてください。MSDN サブスクリプションをお持ちの場合は、「[MSDN サブスクライバー向けの Azure の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

> [AZURE.NOTE]Azure で仮想マシンを実行しているときは継続的に料金コストが発生します。その費用は、無料評価版、MSDN サブスクリプション、または有料のサブスクリプションに対して請求されます。Azure Virtual Machines を実行するコストの詳細については、[Virtual Machines 料金](http://azure.microsoft.com/pricing/details/virtual-machines/)と [Azure 料金計算ツール](http://azure.microsoft.com/pricing/calculator/)に関するページを参照してください。コストを低く抑える方法については、「[Azure のテスト環境の仮想マシンで生じるコストを最小限に抑える方法](#costs)」を参照してください。

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [基本構成テスト環境](virtual-machines-base-configuration-test-environment.md)


## フェーズ 1: 仮想ネットワークを作成する

最初に、必要であれば、「[Azure PowerShell のインストールと構成方法](../install-configure-powershell.md)」の手順に従って、ローカル コンピューターに Azure PowerShell をインストールします。Azure PowerShell プロンプトを開きます。

次に、以下のコマンドで、適切な Azure サブスクリプションを選択します。引用符内のすべての文字 (< and > を含む) を、適切な名前に置き換えてください。

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

サブスクリプション名は、**Get-AzureSubscription** コマンドで表示される SubscriptionName プロパティから取得できます。

次に、Azure PowerShell をリソース マネージャー モードに切り替えます。

	Switch-AzureMode AzureResourceManager 

次に、基本構成テスト ラボ用の新しいリソース グループを作成します。一意のリソース グループ名を確認するには、次のコマンドを実行して、既存のリソース グループの一覧を取得します。

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

リソース マネージャー ベースの仮想マシンを作成できる Azure の場所一覧を取得するには、次のコマンドを実行します。

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

これらのコマンドを使用して、新しいリソース グループを作成します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

リソース マネージャー ベースの仮想マシンには、リソース マネージャー ベースのストレージ アカウントが必要です。小文字のアルファベットと数字のみが含まれているストレージ アカウントのグローバルに一意の名前を選択する必要があります。既存のストレージ アカウントの一覧を取得するには、次のコマンドを実行します。

	Get-AzureStorageAccount | Sort Name | Select Name

選択したストレージ アカウント名がグローバルに一意かどうかをテストするには、PowerShell の Azure サービス管理モードで **Test-AzureName** コマンドを実行する必要があります。次のコマンドを実行します。

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Test-AzureName コマンドで **False** と表示される場合、指定した名前は一意です。一意の名前だと判断できたら、次のコマンドで Azure PowerShell をリソース マネージャー モードに戻ります。

	Switch-AzureMode AzureResourceManager 

次のコマンドを実行して、新しいテスト環境の新しいストレージ アカウントを作成します。

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

次に、基本構成の Corpnet サブネットをホストすることになる TestLab という Azure Virtual Network を作成します。

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

現在の構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG01.png)

## フェーズ 2: DC1 を構成する

DC1 は、Active Directory ドメイン サービス (AD DS) のドメイン corp.contoso.com のドメイン コントローラーとして、また、TestLab 仮想ネットワークの仮想マシンに使用される DNS サーバーとして機能します。

まず、ローカル コンピューターの Azure PowerShell コマンド プロンプトでリソース グループの名前、Azure の場所、ストレージ アカウント名を入力して、これらのコマンドを実行し、DC1 の Azure Virtual Machines を作成します。

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

続けて、DC1 仮想マシンに接続します。

1.	Azure プレビュー ポータルで、左側のウィンドウの **[すべて参照]** をクリックし、**[参照]** リストの **[仮想マシン]** をクリックして、**DC1** 仮想マシンをクリックします。  
2.	**[DC1]** ウィンドウで、**[接続]** をクリックします。
3.	メッセージが表示されたら、DC1.rdp ダウンロード ファイルを開きます。
4.	リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.	資格情報の入力を求められたら、次の情報を使用します。
- 名前: **DC1\\**[ローカル管理者のアカウント名]
- パスワード: [ローカル管理者アカウントのパスワード]
6.	証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

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

次に、corp.contoso.com ドメインのドメイン コントローラー兼 DNS サーバーとして DC1 を構成します。管理者レベルの Windows PowerShell コマンド プロンプトで以下のコマンドを実行します。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

DC1 を再起動した後、DC1 の仮想マシンに再接続します。

1.	Azure プレビュー ポータルで、左側のウィンドウの [すべて参照] をクリックし、[参照] リストの [仮想マシン] をクリックして、DC1 仮想マシンをクリックします。
2.	[DC1] ウィンドウで、[接続] をクリックします。
3.	DC1.rdp を開くように求められたら、**[開く]** をクリックします。
4.	リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.	資格情報の入力を求められたら、次の情報を使用します。
- 名前: **CORP\\**[ローカル管理者のアカウント名]
- パスワード: [ローカル管理者アカウントのパスワード]
6.	証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

次に、CORP ドメインのメンバー コンピューターにログインする際に使用するユーザー アカウントを Active Directory に作成します。管理者レベルの Windows PowerShell コマンド プロンプトで以下のコマンドを 1 つずつ実行します。

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

1 つ目のコマンドを実行すると、User1 アカウントのパスワードを入力するよう求められます。このアカウントは、CORP ドメインに属しているすべてのコンピューターのリモート デスクトップ接続に使用されるため、強力なパスワードを選んでください。強度を確認するには、[パスワード チェッカーの強力なパスワードの使用](https://www.microsoft.com/security/pc-security/password-checker.aspx)に関するページを参照してください。User1 アカウントのパスワードをメモし、安全な場所に保管してください。

DC1 とのリモート デスクトップ セッションを閉じ、CORP\\User1 アカウントを使用して再接続します。

今度は、Ping ツールのトラフィックを許可します。管理者レベルの Windows PowerShell コマンド プロンプトで以下のコマンドを実行してください。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

現在の構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG02.png)

## フェーズ 3: APP1 を構成する

APP1 は、Web サービスとファイル共有サービスを提供します。

まず、ローカル コンピューターの Azure PowerShell コマンド プロンプトでリソース グループの名前、Azure の場所、ストレージ アカウント名を入力して、これらのコマンドを実行し、APP1 の Azure Virtual Machines を作成します。

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

次に、APP1 のローカル管理者のアカウント名とパスワードを使用して APP1 仮想マシンに接続し、管理者レベルの Windows PowerShell コマンド プロンプトを開きます。

名前解決が正しく実行されることと、APP1 と DC1 間のネットワーク通信を確認するために、「**ping dc1.corp.contoso.com**」コマンドを実行し、応答が 4 回返されることを確認します。

次に、Windows PowerShell プロンプトでこれらのコマンドを使用して CORP ドメインに APP1 仮想マシンを参加させます。

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Add-Computer コマンドを入力した後、CORP\\User1 ドメイン アカウントの資格情報を指定する必要があることに注意してください。

APP1 が再起動した後、CORP\\User1 のアカウント名とパスワードを使用して APP1 に接続し、管理者レベルの Windows PowerShell コマンド プロンプトを開きます。

次に、APP1 に Web サーバーをインストールします。Windows PowerShell コマンド プロンプトで以下のコマンドを実行してください。

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

次に、以下のコマンドを実行して、APP1 上に共有フォルダーを作成し、そのフォルダー内にテキスト ファイルを作成します。

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

現在の構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG03.png)

## フェーズ 4: CLIENT1 を構成する

CLIENT1 は、Contoso イントラネット上の標準的なノート PC、タブレット、またはデスクトップ コンピューターとなります。

まず、ローカル コンピューターの Azure PowerShell コマンド プロンプトでリソース グループの名前、Azure の場所、ストレージ アカウント名を入力して、これらのコマンドを実行し、CLIENT1 の Azure Virtual Machines を作成します。

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id	
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

次に、CLIENT1 のローカル管理者のアカウント名とパスワードを使用して CLIENT1 仮想マシンに接続し、管理者レベルの Windows PowerShell コマンド プロンプトを開きます。

名前解決が正しく実行されることと、CLIENT1 と DC1 間のネットワーク通信を確認するために、Windows PowerShell コマンド プロンプトから「**ping dc1.corp.contoso.com**」コマンドを実行し、応答が 4 回返されることを確認します。

次に、Windows PowerShell プロンプトでこれらのコマンドを使用して CORP ドメインに CLIENT1 仮想マシンを参加させます。

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Add-Computer コマンドを入力した後、CORP\\User1 ドメイン アカウントの資格情報を指定する必要があることに注意してください。

CLIENT1 が再起動した後、CORP\\User1 のアカウント名とパスワードを使用して APP1 に接続し、管理者レベルの Windows PowerShell コマンド プロンプトを開きます。

次に、APP1 上にある Web リソースとファイル共有リソースに CLIENT1 からアクセスできることを確認します。

1.	サーバー マネージャーのツリー ウィンドウで、**[ローカル サーバー]** をクリックします。
2.	**CLIENT1 のプロパティ**で、**[IE セキュリティ強化の構成]** の横にある **[有効]** をクリックします。
3.	**[Internet Explorer セキュリティ強化の構成]** で、**[Administrators]** と **[Users]** の **[オフ]** をクリックし、**[OK]** をクリックします。
4.	スタート画面で、**[Internet Explorer]** をクリックして、**[OK]** をクリックします。
5.	アドレス バーに「**http://app1.corp.contoso.com/**」と入力し、Enter キーを押します。APP1 の既定のインターネット インフォメーション サービス Web ページが表示されます。6.	デスクトップのタスク バーからエクスプローラー アイコンをクリックします。
7.	アドレス バーに「**\\\\app1\\Files**」と入力し、Enter キーを押します。
8.	フォルダー ウィンドウに、Files 共有フォルダー内容が表示されます。
9.	**[Files]** 共有フォルダー ウィンドウで、**Example.txt** ファイルをダブルクリックします。Example.txt ファイルの内容が表示されます。
10.	**[example.txt - メモ帳]** ウィンドウと **[Files]** 共有フォルダー ウィンドウを閉じます。

最終的な構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Azure の基本構成の準備が整いました。アプリケーションの開発やテスト、および追加のテスト環境に使用できます。

## その他のリソース

[ハイブリッド クラウド テスト環境](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[基本構成テスト環境](virtual-machines-base-configuration-test-environment.md)


## <a id="costs"></a>Azure のテスト環境の仮想マシンで生じるコストを最小限に抑える方法

テスト環境の仮想マシンを実行する際に発生するコストは、次のいずれかの方法で最小限に抑えることができます。

- テスト環境の作成および必要なテストとデモンストレーションをできるだけ短時間で実行する。完了したらテスト環境の仮想マシンを削除してください。
- テスト環境の仮想マシンをシャットダウンして割り当て解除済みの状態にする。

Azure PowerShell で仮想マシンをシャットダウンするには、リソース グループの名前を入力し、以下のコマンドを実行します。

	$rgName="<your resource group name>"
	Stop-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

停止 (割り当て解除済み) 状態のすべての仮想マシンを起動して、正しく動作させるためには、次の順序で起動する必要があります。

1.	DC1
2.	APP1
3.	CLIENT1

Azure PowerShell で仮想マシンを順番に起動するには、リソース グループの名前を入力し、以下のコマンドを実行します。

	$rgName="<your resource group name>"
	Start-AzureVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=July15_HO5-->
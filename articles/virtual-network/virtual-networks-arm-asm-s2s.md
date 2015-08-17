<properties 
   pageTitle="Azure で従来の VNet と ARM Vnet を接続する方法 - ソリューション ガイド"
   description="従来の VNet と新しい VNet の間の VPN 接続を作成する方法を学習します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2015"
   ms.author="telmos" />

# 従来の VNet を新しい VNet に接続する

Azure には現在、Azure サービス マネージャー (従来型と呼ばれます) と Azure リソース マネージャー (ARM) という 2 つの管理モードがあります。これまで Azure を使用してきているユーザーであれば、おそらく Azure VM および従来の VNet 上で実行されているインスタンス ロールを利用されていることでしょう。そして、新しい VM とロール インスタンスが、ARM で作成された VNet 上で実行されていることも考えられます。

このような場合、新しいインフラストラクチャと従来のリソースが確実に通信できるようにする必要があります。これを実行するには、2 つの VNet 間で VPN 接続を作成します。次の図は、2 つの VNet (従来の VNet と ARM VNet)、およびこれらの VNet 間にあるセキュリティ保護されたトンネル接続を含む環境の例です。

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]このドキュメントでは、テストを目的として、エンド ツー エンドのソリューションを説明します。ご自身の VNets を既に設定済みで、VPN Gateway および Azure のサイト間接続について理解されている場合は、[Configure a S2S VPN between an ARM VNet and a classic VNet (ARM VNet と従来の VNet の間の S2S VPN 接続を構成する)](../virtual-networks-arm-asm-s2s-howto.md) を参照Gateway

このシナリオをテストするには、次を実行します。

1. [従来の VNet 環境を作成する](#Create-a-classic-VNet-environment)
2. [新しい VNet 環境を作成する](#Create-a-new-VNet-environment)
3. [これら 2 つの VNet を接続する](#Connect-the-two-VNets)

上記の手順を実行するにはまず、従来のポータル、ネットワーク構成ファイル、および Azure サービス マネージャーの PowerShell コマンドレットを含む従来の Azure 管理ツールを使用します。その後に、新しい Azure ポータル、ARM テンプレート、および ARM PowerShell コマンドレットを含む、新しい管理ツールを使用します。

>[AZURE.IMPORTANT]2 つの VNet を相互接続するには、CIDR ブロック競合を回避する必要があります。VNet にはそれぞれ、必ず一意の CIDR ブロックを使用してください。

## 従来の VNet 環境を作成する

既存の従来の VNet を使用して、新しい ARM VNet に接続できます。この例では、2 つのサブネット、ゲートウェイ、および VM を含む従来の VNet を、新たにテスト用に作成する方法を説明します。

### 手順 1: 従来の VNet を作成する

上の図 1 に対応する新しい VNet を作成するには、以下の手順に従います。

1. PowerShell コンソールで、次のコマンドを実行してご自身の Azure アカウントを追加します。

		Add-AzureAccount

2. サインイン ダイアログ ボックスに表示される指示に従い、Azure アカウントでログオンします。

3. Azure サービス管理 PowerShell コマンドレットが確実に使用されるように、次のコマンドを実行します。

		Switch-AzureMode AzureServiceManagement

4. 次のコマンドを実行して、Azure ネットワークの構成ファイルをダウンロードします。

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. ダウンロードしたファイルを開き、アドレスのプレフィックスに *10.2.0.0/16* 、VPN Gateway のアドレスに任意の有効パブリック IP アドレスを使用する、*vnet02* という名前のローカル ネットワーク サイトを追加します。このような追加を実行するには、**LocalNetworkSite** 要素を構成ファイル内の **LocalNetworkSites** 要素に追加します。次のファイル スニペットは、**LocalNetworksSites** 要素の例です。

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. **VirtualNetworkSites** 要素で、上記のシナリオの図に従って、アドレス プレフィックスが *10.1.0.0/16* で 2 つのサブネットを含む新しい仮想ネットワークを追加します。次のファイル スニペットは、**VirtualNetworkSites** 要素の例です。
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. ファイルを保存してから、次のコマンドを実行して Azure にアップロードします。必要に応じて、ファイル パスを環境に合わせて変更してください。

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### 手順 2: 従来の VNet で VM を作成する

Azure サービス マネージャーの PowerShell コマンドレットを使用して、従来の VNet に VM を作成するには、以下の手順に従います。

1. Azure から VM イメージを取得します。次の PowerShell コマンドを使用すると、入手可能な最新の Windows Server 2012 R2 イメージが取得できます。

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. 次のコマンドを実行して、VM の仮想ハード ドライブ (VHD) を保存するストレージ アカウントを作成します。

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  次のコマンドを実行して、VM を作成します。必ずユーザー名とパスワードの値を置き換えてください。

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  次のコマンドを実行して、VM を *Subnet1* に接続します。

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. 次のコマンドを実行して、VM をホストする新しいクラウド サービスを作成します。

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### 手順 3: 従来の VNet 用の VPN Gateway を作成する 

従来の Azure ポータルを使用して vnet01 の VPN Gateway を作成するには、以下の手順に従います。

1. https://manage.windowsazure.com で、従来の Azure ポータルを起動します。必要に応じて、資格情報を指定します。
2. **[すべてのアイテム] **一覧を下へスクロールして、**[ネットワーク] **をクリックします。
3. 仮想ネットワークの一覧で、**[vnet01]**、**[構成]**の順にクリックします。
4. **[サイト間接続]** で、**[ローカル ネットワークに接続する]** チェック ボックスをオンにします。
5. **[ローカル ネットワーク]** の一覧で、**[vnet02]**、**[保存]**、**[はい]** の順にクリックします。
6. **[ダッシュ ボード] **をクリックすると、次の図 2 で示すように、ゲートウェイがまだ作成されていないというメッセージが表示されます。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. 次の図 3 で示すように、**[ゲートウェイの作成]** をクリックして、vnet01 の VPN Gateway を作成します。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. ゲートウェイの種類の一覧で、**[動的]**、**[はい]** の順にクリックします。ゲートウェイの作成中、ゲートウェイが黄色で表示されるため、ダッシュ ボード イメージが変わります。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]この操作には数分かかる場合があります。

9. ゲートウェイが作成されたら、以下に示されるそのゲートウェイのパブリック IP アドレスを書き留めます。このアドレスは、後で ARM VNet のローカル ネットワークを作成するときに必要になります。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## 新しい VNet 環境の作成

これで VM とゲートウェイを含む従来の VNet が作成できたので、次に ARM VNet を作成します。

### 手順 1: ARM で新しい VNet を作成する

ARM テンプレートを使用して、2 つのサブネットおよび従来の VNet のローカル ネットワークを含む ARM VNet を作成するには、次の手順に従います。

1. azuredeploy.json ファイルと azuredeploy parameters.json ファイルを [[GitHub]](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s) からダウンロードします。
2. Visual Studio で azuredeploy.json ファイルを開くと、このテンプレートでは次の 4 つのリソースを作成できることがわかります。 

	- **ローカル ゲートウェイ**: このリソースは、接続先となる VNet 用に作成されたゲートウェイを示します。この場合、vnet01 用のゲートウェイがそれにあたります。
	- **仮想ネットワーク**: このリソースは、作成される ARM VNet を示します。この場合、vnet02 がそれにあたります。
	- **ゲートウェイのパブリック IP**: このリソースは、ARM VNet 用に作成されるゲートウェイのパブリック IP アドレスを示します。 
	- **ゲートウェイ**: このリソースは、ARM VNet (vnet02) 用に作成されるゲートウェイを示します。

3. このファイルで使用されるパラメーターに注意してください。これらのほとんどには既定値があります。これらの値は、必要に応じて変更できます。

4. Visual Studio で azuredeploy-parameters.json ファイルを開きます。このファイルには、テンプレート ファイル内のパラメーターに使用する値が含まれます。必要に応じて、次の値を編集します。

	- **subscriptionId**: サブスクリプション ID を編集して貼り付けます。ご自身のサブスクリプション ID が不明な場合は、**Get-azuresubscription** PowerShell コマンドレットを実行して ID を取得します。
	- **location**: VNet を作成する Azure の場所を指定します。この場合、**Central US** がそれにあたります。
	- **virtualNetworkName**: これは、作成する ARM VNet の名前です。この場合、**vnet02** がそれにあたります。
	- **localGatewayName**: これは、新しい ARM VNet から接続したいローカル ネットワークです。この場合、**vnet01** がそれにあたります。
	- **localGatewayIpAddress**: これは、接続先となるネットワーク用に作成されたゲートウェイのパブリック IP アドレスです。この場合、これは、**vnet01** 用の VPN Gateway を作成した上記の手順 9 で書き留めた IP アドレスです。
	- **localGatewayAddressPrefix**: これは、VNet の接続先となるローカル ネットワークで使用される CIDR ブロックです。この場合、接続先となる VNet は **vnet01** で、CIDR ブロックは **10.1.0.0/16** です。
	- **gatewayPublicIPName**: これは、ARM VNet 用に作成されるゲートウェイのパブリック IP アドレスに対して作成される IP オブジェクトの名前です。
	- **gatewayName**: これは、ARM VNet 用に作成されるゲートウェイ オブジェクトの名前です。
	- **addressPrefix**: これは、ARM VNet の CIDR ブロックです。この場合、**10.2.0.0/16** がそれにあたります。
	- **subnet1Prefix**: これは、ARM VNet 内のサブネットの CIDR ブロックです。この場合、**10.2.0.0/24** がそれにあたります。
	- **gatewaySubnetPrefix**: これは、ARM VNet 内のゲートウェイ サブネットで使用される CIDR ブロックです。この場合、**10.2.200.0/29** がこれにあたります。
	- **connectionName**: これは、作成される接続オブジェクトの名前です。
	- **sharedKey**: これは、接続に使用する IPSec 共有キーです。この場合、**abc123** がそれにあたります。

5. **RG1** という名前の新しいリソース グループに、ARM VNet とそれに関連するオブジェクトを作成するには、次の PowerShell コマンドを実行します。テンプレート ファイルと、パラメーター ファイルのパスを必ず変更してください。

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]この操作には数分かかる場合があります。

7. ブラウザーで https://ms.portal.azure.com/ に移動し、必要に応じて資格情報を入力します。
8. 以下に示すように、Azure ポータルで **[RG1]** リソース グループのタイルをクリックします。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. ARM テンプレートを使用してグループにリソースが追加されたことがわかります。

### 手順 2: ARM で新しい VM を作成する

Azure ポータルから、新しい VNet 内に VM を作成するには、以下の手順に従います。

1. ポータルで、**[新規]**ボタン、**[コンピューティング]**、**[Windows Server 2012 R2 Datacenter]** の順にクリックします。
2. 以下に示すように、右側のウィンドウの下部にある **[コンピューティング スタックを選択]** で **[マネージャー スタックを使用]** を選択してから、**[作成]** をクリックします。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. **[基本]** ブレードで、VM 名、ユーザー名、パスワード、サブスクリプション、リソース グループ、および VM の場所を入力してから **[OK]** をクリックします。次の図は、このシナリオの設定を示しています。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. **[サイズの選択]** ブレードで、サイズを選択してから **[選択]** をクリックします。この場合、**[D2]** を選択します。
5. **[設定]** ブレードで、**[仮想ネットワーク]**、**[vnet02]** の順にクリックします。
6. **[サブネット]**、**[Subnet1]**、**[OK]** の順にクリックします。**[概要]** ブレードは、次の図のように表示されます。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. **[OK]** をクリックしてから、**[作成]** をクリックして VM を作成します。以下に示すように、ポータルに新しいタイルが表示され、VM がプロビジョニングされているのがわかります。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]この操作には数分かかる場合があります。このドキュメントの次の説明に進んでください。

## これら 2 つの VNet を接続する

VM に接続された 2 つの VNet が作成できたので、すでに確立済みのゲートウェイを使用してこれらの VNet を接続し、その接続をテストしましょう。

### 手順 1: 従来の VNet 用のゲートウェイを構成する

ARM VNet (vnet02) 用に作成されたゲートウェイの IP アドレスが使用されるように従来の VNet を構成し、各 VNet から接続を確立する必要があります。これを実行するには、次の手順に従ってください。

1. ARM VNet のゲートウェイで使用されている IP アドレスを取得するには、次のコマンドを実行して出力を参照します。このアドレスを書き留めます。後で、従来の VNet 用のローカル ネットワーク設定を変更するときに必要になります。

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. 次のコマンドを実行すると、PowerShell コマンドに Azure サービス管理 API が確実に使用されます。

		Switch-AzureMode AzureServiceManagement

3. 次のコマンドを実行して、Azure のネットワーク構成ファイルをダウンロードします。

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. ダウンロードしたファイルを開き、上記の手順 1 で取得した新しい VNet 用のゲートウェイの IP アドレスが追加されるように、**vnet02** の **LocalNetworkSite** 要素を編集します。この要素は、次の例のように表示されます。

	      <LocalNetworkSite name="vnet03">
	        <AddressSpace>
	          <AddressPrefix>10.3.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. ファイルを保存してから、従来の VNet を構成するために次のコマンドを実行します。上記の手順 4 で保存したファイルをポイントするように、パスを必ず変更してください。

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. 次のコマンドを実行して、従来の VNet ゲートウェイの IPSec 共有キーを設定します。次に示すような出力が表示されます。ご自身の既存の VNet を使用している場合は、VNet の名前を必ず変更してください。

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### 手順 2: ARM VNet 用のゲートウェイを構成する

従来の VNet ゲートウェイを構成できたので、接続を確立しましょう。これを実行するには、次の手順に従ってください。

1. PowerShell コンソールで、次のコマンドを実行して ARM モードに切り替えます。 

		Switch-AzureMode AzureResourceManager

2. 次のコマンドを実行して、ゲートウェイ間の接続を作成します。

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. https://manage.windowsazure.com で Azure ポータルを開き、必要に応じて資格情報を入力します。
4. **[すべてのアイテム]** で下にスクロールし、**[ネットワーク]**、**[vnet01]**、**[ダッシュ ボード]** の順にクリックします。以下に示すように、**vnet01** と **vnet02** の間の接続が確立されたのがわかります。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. 従来の VNet とその接続は従来のポータルから管理できますが、新しい Azure ポータルを使用することをお勧めします。新しいポータルを開くには、https://ms.portal.azure.com に移動します。
6. 新しいポータルで、**[すべて参照]**、**[仮想ネットワーク (従来のもの)]**、**[vnet01]** の順にクリックします。次の **[VPN 接続]** ウィンドウを参照してください。

	![VNet のダッシュ ボード](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### 手順 3: 接続をテストする

2 つの VNet が接続できたので、一方の VM からもう一方の VM に対して ping を実行することで、接続をテストしましょう。一方の VM のファイアウォール設定を ICMP が使用できるように変更し、その VM に対してもう一方の VM から ping を実行する必要があります。これを実行するには、次の手順に従ってください。

1. Azure ポータルで、**[すべて参照]**、**[仮想マシン]**、**[VM02]** の順にクリックします。
2. **[VM02]** ブレードで、**[接続]** をクリックします。必要に応じて、ブラウザーのセキュリティ バナーで **[開く]** をクリックして RDP ファイルを開きます。
3. **[リモート デスクトップ接続]** ダイアログ ボックスで、**[接続]** をクリックします。
4. **[Windows セキュリティ]** ダイアログ ボックスに、VM のユーザー名とパスワードを入力します。 
5. **[リモート デスクトップ接続]** ダイアログ ボックスで、**[はい]** をクリックします。
6. VM に接続した後、**[サーバー マネージャー]** で、**[ツール]**、**[セキュリティが強化された Windows ファイアウォール]** の順にクリックします。
7. **[セキュリティが強化された Windows ファイアウォール]** ウィンドウで、**[受信の規則]** をクリックします。
8. **[受信の規則]** 一覧で、**[ファイルとプリンタの共有 (エコー要求 - ICMPv4 受信)]** を右クリックしてから、**[ルールの有効化]** をクリックします。
9. タスク バーで、**[Windows PowerShell]** ボタンをクリックしてから、次のコマンドを実行します。

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. VM の IP アドレスを書き留めます。この場合、**10.2.0.101** がそれにあたります。もう一方の VM からそのアドレスを ping することで、VNet 間の接続をテストします。
11. Azure ポータルで、**[仮想マシン (従来のもの)]**、**[VM01]**、**[接続]** の順にクリックします。必要に応じて、ブラウザーのセキュリティ バナーで **[開く]** をクリックして RDP ファイルを開きます。
12. **[リモート デスクトップ接続]** ダイアログ ボックスで、**[接続]** をクリックします。
13. **[Windows セキュリティ]** ダイアログ ボックスに、VM のユーザー名とパスワードを入力します。 
14. **[リモート デスクトップ接続]** ダイアログ ボックスで、**[はい]** をクリックします。
15. リモート VM のタスク バーで、**[Windows PowerShell]** ボタンをクリックしてから、次のコマンドを実行します。

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## 次のステップ

- [ネットワーク リソース プロバイダー](../resource-groups-networking.md)についてさらに学習できます。
- [create a S2S VPN connection between a classic VNet and an ARM VNet (従来の VNet と ARM VNet の間の S2S VPN 接続を作成する)](../virtual-networks-arm-asm-s2s-howto.md) 方法に関する一般的なガイドラインを参照してください。

<!---HONumber=August15_HO6-->
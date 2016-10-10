<properties 
   pageTitle="仮想マシンの複数の IP アドレス - PowerShell | Microsoft Azure"
   description="Azure PowerShell を使用して仮想マシンに複数の IP アドレスを割り当てる方法を説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# 仮想マシンに複数の IP アドレスを割り当てる

Azure 仮想マシン (VM) には、1 つ以上ネットワーク インターフェイス (NIC) をアタッチできます。各 NIC には、1 つ以上のパブリックまたはプライベート IP アドレスを割り当てることができます。Azure 内の IP アドレスの詳細については、「[Azure 内の IP アドレス](virtual-network-ip-addresses-overview-arm.md)」の記事をご覧ください。この記事では、Azure Resource Manager デプロイメント モデルで Azure PowerShell を使用して、NIC に複数の IP アドレスを割り当てる方法について説明します。

NIC に複数の IP アドレスを割り当てると、VM で次のことが可能になります。

- 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
- ファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

プレビューに登録するには、[複数の IP 係](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)まで、メールでサブスクリプション ID と使用目的をご連絡ください。

## <a name = "create"></a>複数の IP アドレスを持つ VM を作成する

1. PowerShell コマンド プロンプトを開き、1 つの PowerShell セッション内で、このセクションの残りの手順を完了します。まだ PowerShell をインストール、構成していない場合は、「[Azure PowerShell のインストールと構成](../powershell-install-configure.md)」の手順を実行してください。

2. 次の $ 変数の "値" を、NIC に付ける名前、割り当てる[リソース グループ](../resource-group-overview.md#resource-groups)、作成する Azure の[場所](https://azure.microsoft.com/regions)に変更します。

		$NicName     = "VM1-NIC1"
		$NicRgName   = "RG1"
		$NicLocation = "westus"

	既存の Azure の場所やリソース グループの名前がわからない場合は、次のコマンドを入力します。

		Get-AzureRmLocation 	 | Format-Table Location
		Get-AzureRmResourceGroup | Format-Table ResourceGroupName	
 
3. <a name="subnet"></a>NIC は、NIC と同じ場所および同じ[サブスクリプション](../azure-glossary-cloud-terminology.md#subscription)にある既存 Azure の仮想ネットワーク (VNet) 内のサブネットに接続する必要があります。VNet の詳細については、「[仮想ネットワークの概要](virtual-networks-overview.md)」を、作成方法については、「[VNet を作成する](virtual-networks-create-vnet-arm-ps.md)」の記事をご覧ください。次の $ 変数の "値" を、NIC の接続先である VNet とサブネットの名前と、VNet が所属するリソース グループの名前に変更します。

		$VNetName   = "VNet1"
		$SubnetName = "Subnet1"
		$VNetRgName = "Network"

	既存の VNet の名前がわからない場合は、次のコマンドを入力し、前の変数の *VNet1* を VNet の名前で置き換えます。
		
		Get-AzureRmVirtualNetwork | Format-Table Name
		
	返される一覧が空の場合は、VNet を作成する必要があります。方法については、「[仮想ネットワークを作成する](virtual-networks-create-vnet-arm-ps.md)」記事をご覧ください。

	VNet 内のサブネットの名前を取得するには、次のコマンドを入力し、*Subnet1* をサブネットの名前に置き換えます。
		
		$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $VNetRgName
		$VNet.Subnets | Format-Table Name, AddressPrefix

4. 次のコマンドを入力してサブネットを取得し、変数に代入します。

		$Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>NIC に割り当てる IP 構成を定義します。各構成には、1 つの静的または動的なプライベート IP アドレスを割り当てることができ、静的アドレスまたは動的アドレスのパブリック IP アドレス リソースを 1 つ関連付けられます。

	次のように、3 つのプライベート IP アドレスと 1 つのパブリック IP アドレスが割り当てられた構成が作成され、NIC に割り当てられます。

	- **IPConfig 1**: 動的なプライベート IP アドレス (既定値) と、*PIP1* という名前のパブリック IP アドレス リソースからのパブリック IP アドレス。
	- **IPConfig 2**: 静的なプライベート IP アドレス。パブリック IP アドレスなし。
	- **IPConfig 3**: 動的なプライベート IP アドレス。パブリック IP アドレスなし。

	NIC に関連付ける IP アドレスの数と設定の内容に応じて、構成の数を追加または削除します。

	**IPConfig 1**

	*PIP1* の値を、NIC を作成する場所に存在し、別の NIC に関連付けられていない既存のパブリック IP アドレス リソースの名前に変更します。*RG1* を、パブリック IP アドレス リソースのあるリソース グループの名前に変更します。*IPConfig-1* を、最初の IP 構成に付ける名前に変更します。次のコマンドを入力します。

		$PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName "RG1"

		$IpConfigName1 = "IPConfig-1"
		$IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary 

	*-Primary* スイッチにご注意ください。NIC に複数の IP 構成を割り当てる場合は、1 つの構成を*プライマリ*として割り当てる必要があります。既存のパブリック IP アドレス リソースの名前がわからない場合は、次のコマンドを入力します。

		Get-AzureRMPublicIPAddress |Format-Table Name, Location, IPAddress, IpConfiguration

	返された出力の **IPConfiguration** 列に値がない場合、パブリック IP アドレス リソースは既存の NIC に関連付けられていないため、使用できます。リストが空または使用できるパブリック IP アドレス リソースがない場合は、**New-AzureRmPublicIPAddress** コマンドを使用して作成できます。

	>[AZURE.NOTE] パブリック IP アドレスには、わずかな費用がかかります。IP アドレスの料金の詳細については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページをご覧ください。

	**IPConfig-2**

	*IPConfig-2* を、2 番目の IP 構成に付ける名前に変更し、*10.0.0.5* を NIC の割り当て先のサブネットの未使用の有効な IP アドレスに変更します。次のコマンドを入力します。

		$IPConfigName2 = "IPConfig-2"
		$IPAddress = 10.0.0.5

		$IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress

	サブネットに割り当てられた IP アドレスの範囲がわからない場合は、次のコマンドを入力します。

		$VNet.Subnets | Format-Table Name, AddressPrefix
		
	**IPConfig-3**

	*IPConfig-3* を、3 番目の IP 構成に付ける名前に変更し、次のコマンドを入力します。

		$IPConfigName3 = "IPConfig-3"
		$IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
		
	>[AZURE.NOTE] 最大 250 個のプライベート IP アドレスを NIC に割り当てることができます。サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。詳細については、「[Azure 制限](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager)」の記事をご覧ください。

6. 前の手順で定義した IP 構成を使用して NIC を作成します。

		$nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRgName -Location $NicLocation -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. 「[VM を作成する](../virtual-machines/virtual-machines-windows-ps-create.md)」記事にある手順に従って VM を作成するときに、NIC をアタッチします。記事では Windows Server を実行する VM を作成していますが、Linux VM の場合も、選択するオペレーティング システムが異なる以外の手順は同じです。記事の手順 1～3 を完了します。記事の手順 4 と 5 をスキップし、手順 6 を完了します。

	>[AZURE.WARNING] この記事の手順 6 で $nic という名前の変数を変更した場合、またはこの記事の前の手順を完了していない場合は、「VM を作成する」の記事の手順 6 は失敗します。

8. 次のコマンドを入力して、Azure DHCP が NIC に割り当てたプライベート IP アドレスと、NIC に割り当てられたパブリック IP アドレスを表示します。

		$nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>すべてのセカンダリ プライベート IP アドレス (前の手順の出力で **[Primary]** 列が「*False*」になっている IP アドレス) を、オペレーティング システムの TCP/IP 構成に手動で追加します。手順 5 で *IPConfig-1* に割り当てられたプライベート IP アドレスは、*プライマリ*の構成になるため、Azure DHCP を介してオペレーティング システムに自動的に割り当てられます。

	**Windows**

	1. コマンド プロンプトで、「ipconfig /all」と入力します。*プライマリ*の IP アドレス (DHCP 経由) のみを表示できます。
	2. ネットワーク アダプターのプロパティを開きます。
	3. **[インターネット プロトコル バージョン 4]** のプロパティを開きます
		- **[次の IP アドレスを使う]** をクリックし、次の値を入力します。
			- **IP アドレス**: *プライマリ*のプライベート IP アドレスを入力します。
			- **サブネット マスク**: 自分のサブネットに基づいて設定します。たとえば、たとえば、サブネットが/24 サブネットであれば、サブネット マスクは 255.255.255.0 になります。
			- **デフォルト ゲートウェイ**: サブネット内の最初の IP アドレスです。サブネットが 10.0.0.0/24m の場合は、ゲートウェイの IP アドレスは 10.0.0.1 になります。
		- **[次の DNS サーバーのアドレスを使う]** をクリックし、次の値を入力します。
			- **優先 DNS サーバー:** 独自の DNS サーバーを使用していない場合は、「168.63.129.16」と入力します。使用している場合は、DNS サーバーの IP アドレスを入力します。
		- **[詳細]** ボタンをクリックし、他の IP アドレスを追加します。手順 8 にある各セカンダリ プライベート IP アドレスを、プライマリ IP アドレスに指定されたのと同じサブネットの NIC に追加します。
		- **[OK]** をクリックして TCP/IP 設定を閉じ、もう一度 **[OK]** をクリックしてアダプター設定を閉じます。
	4. コマンド プロンプトで、「*ipconfig /all*」と入力します。追加したすべての IP アドレスが表示され、DHCP が無効になります。

	**Linux (Ubuntu)**
	
	1. ターミナル ウィンドウを開きます。
	2. 自身がルート ユーザーになっていることを確認します。ルート ユーザーになっていない場合は、次のコマンドを使います。
	
			sudo -i 
	3. ネットワーク インターフェイスの構成ファイルを更新します (‘eth0’ と仮定)。
		- DHCP の既存の行アイテムを保持します。これによって、以前と同様にプライマリ IP アドレスが設定されます。
		- 次のコマンドを使用して、追加の静的 IP アドレスの構成を追加します。

				cd /etc/network/interfaces.d/
				ls

		.cfg ファイルが表示されます。
	4. ファイルを開きます: vi *filename*。

		ファイルの末尾に次の行が表示されます。

			auto eth0
			iface eth0 inet dhcp
	5. このファイルの行の最後に、次の行を追加します。

			iface eth0 inet static
			address <your private IP address here>
	6. 次のコマンドを使用して、ファイルの内容を保存します。

			:wq
	7.  次のコマンドを使用して、ネットワーク インターフェイスをリセットします。

			sudo ifdown eth0 && sudo ifup eth0

		>[AZURE.IMPORTANT]リモート接続を使用する場合は、同じ行で ifdown と ifup の両方を実行します。
	8. 次のコマンドを使用して、IP アドレスがネットワーク インターフェイスに追加されたことを確認します。

			ip addr list eth0

		追加した IP アドレスが、リストの一部として表示されます。

	**Linux (Redhat、CentOS、その他)**
	
	1. ターミナル ウィンドウを開きます。
	2. 自身がルート ユーザーになっていることを確認します。ルート ユーザーになっていない場合は、次のコマンドを使います。

			sudo -i
	3. パスワードを入力し、画面の指示に従います。ルート ユーザーになったら、次のコマンドを使用して、ネットワーク スクリプト フォルダーに移動します。

			cd /etc/sysconfig/network-scripts
	4. 次のコマンドを使用して、関連する ifcfg ファイルをリストアップします。

			ls ifcfg-*

		ファイルのうちの 1 つに *ifcfg-eth0* があります。
	5. 次のコマンドを使って、*ifcfg-eth0* ファイルをコピーして、名前を「*ifcfg-eth0:0*」に変更します。

			cp ifcfg-eth0 ifcfg-eth0:0
	6. 次のコマンドを使って、*ifcfg-eth0:0* ファイルを編集します。

			vi ifcfg-eth1
	7. 次のコマンドを使って、ファイル内のデバイスを適切な名前 (ここでは *eth0:0*) に変更します。

			DEVICE=eth0:0
	8. *IPADDR = YourPrivateIPAddress* 行を、IP アドレスを反映するように変更します。
	9. 次のコマンドを使用して、ファイルの内容を保存します。

			(:wq)
	10. ネットワーク サービスを再起動し、次のコマンドを実行して、変更が成功したかどうかを確認します。

			/etc/init.d/network restart
			Ipconfig

		返されるリストに、追加した IP アドレス「*eth0:0*」が表示されることを確認します。

## <a name="add"></a>既存の VM に IP アドレスを追加する

既存の NIC に IP アドレスを追加するには、次の手順を完了します。

1. PowerShell コマンド プロンプトを開き、1 つの PowerShell セッション内で、このセクションの残りの手順を完了します。まだ PowerShell をインストール、構成していない場合は、「[Azure PowerShell のインストールと構成](../powershell-install-configure.md)」の手順を実行してください。

2. 次の $ 変数の "値" を、IP アドレスを追加する NIC の名前、リソース グループ、NIC の場所に変更します。

		$NicName     = "RG1-VM1-NI1"
		$NicRgName   = "RG1"
		$NicLocation = "westus"

	NIC の名前がわからない場合は、次のコマンドを入力してから、前述の変数の値を変更します。

		Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. 変数を作成し、次のコマンドを入力して、NIC に設定します。

		$nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRgName

4. この記事の「複数の IP アドレスを持つ VM を作成する」セクションの[手順 3](#subnet) を完了して、NIC の接続先のサブネット ID を取得します。

5. この記事の「複数の IP アドレスを持つ VM を作成する」セクションの[手順 5](#ipconfigs) の手順に従って、ネットワークに追加する IP 構成を作成します。

6. *$IPConfigName4* を、前の手順で作成した IP 構成の名前に変更します。構成を追加するには、次のコマンドを入力します。

		Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1 

7. NIC に IP 構成を設定するには、次のコマンドを入力します。

		Set-AzureRmNetworkInterface -NetworkInterface $nic

8. この記事の「複数の IP アドレスを持つ VM を作成する」セクションの[手順 9](#os) の手順に従って、NIC に追加した IP アドレスを、VM オペレーティング システムに追加します。

<!---HONumber=AcomDC_0928_2016-->
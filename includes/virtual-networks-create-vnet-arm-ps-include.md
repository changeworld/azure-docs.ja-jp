## PowerShell を使用して VNet を作成する方法

PowerShell を使用して VNet を作成するには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。
2. 次に示すように、Azure PowerShell プロンプトで **Switch-AzureMode** コマンドレットを実行して、リソース マネージャー モードに切り替えます。

		Switch-AzureMode AzureResourceManager
	
	上記のコマンドで想定される出力を次に示します。

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Switch-AzureMode コマンドレットは間もなく廃止予定です。これらが廃止された場合は、すべてのリソース マネージャー コマンドレットの名前が変更されます。
	
3. 必要に応じて、次に示すように **New-AzureResourceGroup** コマンドレットを実行して、新しいリソース グループを作成します。このシナリオでは、*TestRG* という名前のリソース グループを作成します。リソース グループの詳細については、[Azure リソース マネージャーの概要](resource-group-overview.md/#resource-groups)を参照してください。

		New-AzureResourceGroup -Name TestRG -Location centralus

	上記のコマンドで想定される出力を次に示します。
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG	

4. 次に示すように、**New-AzureVirtualNetwork** コマンドレットを実行して新しい VNet を作成します。

		New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	上記のコマンドで想定される出力を次に示します。

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : []

5. 次に示すように、**Get-AzureVirtualNetwork** コマンドレットを実行して、仮想ネットワーク オブジェクトを変数に格納します。

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]**$vnet = New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus** を実行すると、手順 4 と手順 5 を組み合わせることができます。

6. 次に示すように、**Add-AzureVirtualNetworkSubnetConfig** コマンドレットを実行して新しい VNet にサブネットを追加します。

		Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	上記のコマンドで想定される出力を次に示します。

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": null,
		                        "Id": null,
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": null,
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": null
		                      }
		                    ]

7. 作成するサブネットごとに、上記の手順 6. を繰り返します。このシナリオでは、次のコマンドで *BackEnd* サブネットを作成します。

		Add-AzureVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. サブネットは作成されますが、この時点では、上記の手順 4. で作成する、VNet の取得に使用するローカル変数にのみ格納されています。Azure への変更を保存するには、次のように **Set-AzureVirtualNetwork** コマンドレットを実行します。

		Set-AzureVirtualNetwork -VirtualNetwork $vnet	
		
	上記のコマンドで想定される出力を次に示します。

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": []
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Sept15_HO2-->
<properties 
	pageTitle="PowerShell を使用して VNET にアプリを接続する" 
	description="PowerShell を利用し、V1 または V2 VNET を接続し、使用する方法に関する指示" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2016" 
	ms.author="ccompy"/>

# PowerShell を利用し、VNET にアプリを接続する #

## 概要 ##

Azure App Service では、アプリ (Web、モバイル、API) をサブスクリプションの VNETに接続できます。この機能は VNET 統合と呼ばれています。VNET 統合機能を App Service Environment 機能と混同しないでください。App Service Environment 機能の場合、VNET の Azure App Service のインスタンスを実行できます。

VNET 統合の新しいポータルには UI があり、V1 または V2 VNET を統合できます。機能の詳細については、「アプリを Azure Virtual Network に統合する」にアクセスしてください。

この記事は UI の使用方法についてではなく、PowerShell を利用して統合を有効にする方法について書かれています。V1 VNET のコマンドは V2 VNET のコマンドとは異なるため、この記事は 2 部構成になっています。

このドキュメントを読み進める前に、次の用意ができていることをご確認ください。

1. 最新の Azure PowerShell SDK インストールしていること。これは Web Platform Installer でインストールできます。
1. Standard または Premium SKU で実行されている Azure App Service のアプリ。

## V1(Classic) VNETS ##

このドキュメントでは、V1 VNET の 3 つの項目を取り上げています。

- ゲートウェイがあり、ポイント対サイトに構成されている既存の V1 VNET へのアプリの接続
- アプリの VNET 統合情報の更新
- V1 VNET からのアプリの切断

### V1(Classic) VNET にアプリを接続します。 ###

Virtual Network (VNET) にアプリを接続する作業には 3 つのプロセスがあります。

1. 特定の VNET に参加する Web アプリを宣言します。ポイント対サイト接続のために VNET に与えられる証明書がアプリにより生成されます。
1. VNET に Web アプリ証明書をアップロードし、ポイント対サイト VPN パッケージ URI を取得します。
1. ポイント対サイトのパッケージ URI で Web アプリ VNET 接続を更新します。

上記の手順 1) と手順 3) は完全にスクリプト化されていますが、手順 2) の場合、ポータルで 1 回限りの手動アクションが必要になります。あるいは、仮想 ARM エンドポイントで PUT または PATCH アクションを実行するためのアクセスが必要になります (このエンドポイントを用意するには、Azure サポートにお問い合わせください)。開始する前に、従来の仮想ネットワークでポイント対サイトが既に有効になっており、ゲートウェイが作成/デプロイされていることを確認してください。ゲートウェイを作成し、ポイント対サイトを有効にするには、「[VPN Gateway を作成する][createvpngateway]」の説明に基づき、ポータルを使用する必要があります。

V1 VNET のサブスクリプションは、統合するアプリを保持する App Service プランのサブスクリプションと同じである必要があります。

#####Azure PowerShell SDK の設定#####

PowerShell ウィンドウを開き、Azure のアカウントとサブスクリプションを設定します。

	Login-AzureRmAccount

そのコマンドでは Azure 資格情報を取得するためのプロンプトが開きます。サインイン後、

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

または

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

を利用し、使用するサブスクリプションを選択します。VNET と App Service プランが入っているサブスクリプションを使用します。

#####このドキュメントで使用される変数#####

下のコマンドを簡潔にするために、特定の構成で $Configuration PowerShell 変数を設定します。

次のパラメーターを利用し、PowerShell に次のような変数を設定します。

	$Configuration = @{} 
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]" 
	$Configuration.VnetResourceGroup = "[Your vnet resource group]" 
	$Configuration.VnetName = "[Your vnet name]"
 
アプリの場所にはスペースを使用しません。たとえば、West US は westus になります。

	$Configuration.WebAppLocation = "[Your web app Location]" 

次の項目は、証明書が書き込まれる場所です。ローカル システム上の書き込み可能なパスになります。最後に .cer を追加してください。

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]" 

設定内容を確認するには、「$Configuration」と入力します。

	> $Configuration
	
	Name                           Value
	----                           -----
	GeneratedCertificatePath       C:\vnetcert.cer
	VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
	WebAppResourceGroup            vnetdemo-rg
	VnetResourceGroup              testase1-rg
	VnetName                       TestNetwork
	WebAppName                     vnetintdemoapp
	WebAppLocation                 centralus

このドキュメントの残りでは、今までの説明に基づいて変数が作成されているものと想定しています。

#####アプリに VNET を宣言する#####

次のコマンドを利用し、この特定の VNET を使用することをアプリに通知します。その通知に基づき、アプリは必要な証明書を生成します。

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

このコマンドが完了すると、$vnet に変数 Properties が含まれます。Properties 変数には、証明書の拇印と証明書のデータの両方を含めます。

#####仮想ネットワークに Web アプリ証明書をアップロードする#####

サブスクリプションと VNET の組み合わせごとに 1 回限りの手動アクションが必要になります。つまり、サブスクリプション A のアプリを VNET A に接続する場合、構成するアプリの数に関係なく、この手順を 1 回だけ行う必要があります。新しいアプリを別の VNET に追加する場合、これを再び行う必要があります。その理由は、証明書セットが Azure App Service のサブスクリプション レベルで生成され、アプリの接続先となる VNET ごとに 1 回生成されるためです。

これらの手順に従っている場合、あるいはポータルを利用し、同じ VNET と統合している場合、証明書は既に設定されています。

最初の手順は .cer ファイルを生成することです。2 番目の手順は VNET に .cer ファイルをアップロードすることです。先の手順で API 呼び出しから .cer ファイルを生成するには、次のコマンドを実行します。

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

証明書は $Configuration.GeneratedCertificatePath で指定される場所にあります。

証明書を手動でアップロードするには、[[Azure ポータル]][azureportal]、[仮想ネットワークの参照 (クラシック)]、[VPN 接続]、[ポイント対サイト]、[証明書の管理] で新しいポータルを使用します。ここから証明書をアップロードします。

#####ポイント対サイトのパッケージを取得する#####

Web アプリで VNET 接続を設定するための次の手順は、ポイント対サイトのパッケージを取得し、それを Web に与えることです。

コンピューターで「GetNetworkPackageUri.json」という名前のファイルを探し、次のテンプレートを保存します。たとえば、C:\\Azure\\Templates\\GetNetworkPackageUri.json にあります。

	{ 
		"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#", 
		"contentVersion": "1.0.0.0", 
		"parameters": { 
			"certData": { 
				"type": "string" 
			}, 
			"certThumbprint": { 
				"type": "string" 
			}, 
			"networkName": { 
				"type": "string" 
			} 
		}, 
		"variables": { 
			"legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]" 
			}, 
			"resources": [ 
			], 
		"outputs" : { 
			"PackageUri" : 
			{ 
			"value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string" 
			} 
		} 
	} 


入力パラメーターの設定:

	$parameters = @{"certData" = $vnet.Properties.certBlob ; 
	certThumbprint = $vnet.Properties.certThumbprint ; 
	"networkName" = $Configuration.VnetName } 

スクリプトの呼び出し:

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json 


これで変数 $output.Outputs.packageUri に Web アプリに与えるパッケージ URI が含まれます。

#####ポイント対サイトのパッケージをアプリにアップロードする#####

最後の手順は、このパッケージをアプリに提供することです。次のコマンドを実行します。

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation 

既存のリソースの上書きを確定するメッセージが表示されることがあります。上書きを許可します。

このコマンドが完了すると、アプリは VNET に接続されているはずです。アプリ コンソールに移動し、次を入力すると確認できます。

	SET WEBSITE_

WEBSITE\_VNETNAME という名前の環境変数の値がターゲット VNET の名前と一致している場合、すべての構成は完了です。

###V1(classic) VNET 統合情報を更新する###

情報を更新または再同期するには、最初に統合を作成したときと同じ手順を繰り返す必要があります。次のような手順です。

1. 構成情報を定義する
1. アプリに VNET を宣言する
1. ポイント対サイトのパッケージを取得する
1. ポイント対サイトのパッケージをアプリにアップロードする

###V1(classic) VNET からアプリを切断する###

切断するには、VNET 統合中に設定した構成情報が必要になります。その情報を利用し、コマンドで VNET からアプリを切断する必要があります。

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## V2(リソース マネージャー) VNETS ##

V2 またはリソース マネージャー VNET には ARM API があります。また、いくつかのことは V1 VNET の場合より簡単にできます。次のことを可能になするスクリプトがあります。

- V2 VNET を作成し、アプリとそれを統合します
- ゲートウェイを作成し、既存 V2 VNET でポイント対サイトを構成し、アプリとそれを統合します
- ゲートウェイとポイント対サイトが有効な既存の V2 VNET とアプリを統合します 
- V2 VNET からアプリを切断します

###V2 VNET App Service 統合スクリプト###

次のスクリプトをコピーし、ファイルに保存します。スクリプトをそのまま使用しない場合、自由にスクリプトから学習し、V2 VNET の設定方法を確認できます。


    function ReadHostWithDefault($message, $default)
    {
    	$result = Read-Host "$message [$default]"
    	if($result -eq "")
	    {
			$result = $default
	    }
		    return $result
    	}
    
	function PromptCustom($title, $optionValues, $optionDescriptions)
	{
	    Write-Host $title
	    Write-Host
	    $a = @()
	    for($i = 0; $i -lt $optionValues.Length; $i++){
		    Write-Host "$($i+1))" $optionDescriptions[$i]
	    }
	    Write-Host
    
	    while($true)
	    {
		    Write-Host "Choose an option: "
		    $option = Read-Host
		    $option = $option -as [int]
		    
		    if($option -ge 1 -and $option -le $optionValues.Length)
		    {
			    return $optionValues[$option-1]
		    }
	    }
    }
    
    function PromptYesNo($title, $message, $default = 0)
    {
	    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
	    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
	    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
	    $result = $host.ui.PromptForChoice($title, $message, $options, $default)
	    return $result
    }
    
    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
	    Write-Host "Creating a new VNET"
	    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
	    New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }
    
    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
	    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
	    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	    
	    Write-Host "Creating a public IP address for this VNET"
	    $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
	    $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip
	    
	    Write-Host "Adding a root certificate to this VNET"
	    $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData
	    
	    Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
	    New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }
    
    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
	    Write-Host "Adding a new Vnet"
	    Write-Host
	    $vnetName = Read-Host "Specify a name for this Virtual Network"
	    
	    $vnetGatewayName="$($vnetName)-gateway"
	    $vnetIpName="$($vnetName)-ip"
	    $vnetIpConfigName="$($vnetName)-ipconf"
	    
	    # Virtual Network settings
	    $vnetAddressSpace="10.0.0.0/8"
	    $vnetGatewayAddressSpace="10.5.0.0/16"
	    $vnetPointToSiteAddressSpace="172.16.0.0/16"
	    
	    $changeRequested = 0
	    $resourceGroupName = $webAppResourceGroup
	    
	    while($changeRequested -eq 0)
	    {
		    Write-Host
		    Write-Host "Currently, I will create a VNET with the following settings:"
		    Write-Host
		    Write-Host "Virtual Network Name: $vnetName"
		    Write-Host "Resource Group Name:  $resourceGroupName"
		    Write-Host "Gateway Name: $vnetGatewayName"
		    Write-Host "Vnet IP name: $vnetIpName"
		    Write-Host "Vnet IP config name:  $vnetIpConfigName"
		    Write-Host "Address Space:$vnetAddressSpace"
		    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
		    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
		    Write-Host
		    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
		    
		    if($changeRequested -eq 0)
		    {
			    $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
			    $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
			    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
			    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
			    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
			    $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
			    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
			    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
		    }
	    }
    
	    $ErrorActionPreference = "Stop";
	    
	    # We create the virtual network and add it here. The way this works is:
	    # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
	    # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
	    # 3) Get the VPN package from the gateway and pass it back to the App.
	    
	    $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
	    $location = $webApp.Location
	    
	    Write-Host "Creating App assocation to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	       
	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location
	    
	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
	    
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	    
	    Write-Host "Finished!"
    }
    
    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
		$ErrorActionPreference = "Stop";
		
		# At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
		Write-Host "Getting App information"
		$webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$location = $webApp.Location
		   
		$webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$currentVnet = $webAppConfig.Properties.VnetName
		if($currentVnet -ne $null -and $currentVnet -ne "")
		{
			Write-Host "Currently connected to VNET $currentVnet"
		}
		
		# Display existing vnets
		$vnets = Get-AzureRmVirtualNetwork
		$vnetNames = @()
		foreach($vnet in $vnets)
		{
			$vnetNames += $vnet.Name
	    }
	    
	    Write-Host
	    $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames
	    
	    # We need to check if this VNET is able to be joined to a App, based on following criteria
		    # If there is no gateway, we can create one.
		    # If there is a gateway:
			    # It must be of type Vpn
			    # It must be of VpnType RouteBased
			    # If it doesn't have the right certificate, we will need to add it.
			    # If it doesn't have a point-to-site range, we will need to add it.
	       
	    $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }
	       
	    if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
	    {
			$ErrorActionPreference = "Continue";
		    # There is no gateway. We need to create one.
		    Write-Host "This Virtual Network has no gateway. I will need to create one."
		    
		    $vnetName = $vnet.Name
		    $vnetGatewayName="$($vnetName)-gateway"
		    $vnetIpName="$($vnetName)-ip"
		    $vnetIpConfigName="$($vnetName)-ipconf"
		    
		    # Virtual Network settings
		    $vnetAddressSpace="10.0.0.0/8"
		    $vnetGatewayAddressSpace="10.5.0.0/16"
		    $vnetPointToSiteAddressSpace="172.16.0.0/16"
		    
		    $changeRequested = 0
		    
		    Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
		    foreach($subnet in $vnet.Subnets)
		    {
			    Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
		    }
		    
		    $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"
		    
		    while($changeRequested -eq 0)
		    {
			    Write-Host
			    Write-Host "Currently, I will create a VNET gateway with the following settings:"
			    Write-Host
			    Write-Host "Virtual Network Name: $vnetName"
			    Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
			    Write-Host "Gateway Name: $vnetGatewayName"
			    Write-Host "Vnet IP name: $vnetIpName"
			    Write-Host "Vnet IP config name:  $vnetIpConfigName"
			    Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
			    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
			    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
			    Write-Host
			    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
			    
			    if($changeRequested -eq 0)
			    {
				    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
				    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
				    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
				    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
				    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
			    }
		    }
		    
		    $ErrorActionPreference = "Stop";
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # If there is no gateway subnet, we need to create one.
		    if($gatewaySubnet -eq $null)
		    {
			    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
			    $vnet.Subnets.Add($gatewaySubnet);
			    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
		    }
		    
		    CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
		    
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
	    }
	    else
	    {
		    $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
		    $gatewayResourceGroup = $uriParts[4]
		    $gatewayName = $uriParts[8]
		       
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName
		    
		    # validate gateway types, etc.
		    if($gateway.GatewayType -ne "Vpn")
		    {
			    Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnType -ne "RouteBased")
		    {
			    Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
		    {
			    Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
			    $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
			    Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
		    }
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # We need to check if the certificate here exists in the gateway.
		    $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates
		    
		    $certFound = $false
		    foreach($certificate in $certificates)
		    {
			    if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
			    {
				    $certFound = $true
				    break
			    }
		    }
		    
		    if(-not $certFound)
		    {
			    Write-Host "Adding certificate"
			    Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
		    }
	    }
	    
	    # Now finish joining by getting the VPN package and giving it to the App
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
	    
	    Write-Host "Finished!"
    }
       
    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
	    $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    $currentVnet = $webAppConfig.Properties.VnetName
	    if($currentVnet -ne $null -and $currentVnet -ne "")
	    {
		    Write-Host "Currently connected to VNET $currentVnet"
		    
		    Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    }
	  	  else
	    {
	  	  Write-Host "Not connected to a VNET."
	    }
    }
    
    Write-Host "Please Login"
    Login-AzureRmAccount
    
    # Choose subscription. If there's only one we will choose automatically
    
    $subs = Get-AzureRmSubscription
    $subscriptionId = ""
    
    if($subs.Length -eq 0)
    {
	    Write-Error "No subscriptions bound to this account."
	    return
    }
    
    if($subs.Length -eq 1)
    {
	    $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
	    $subscriptionChoices = @()
	    $subscriptionValues = @()
	       
	    foreach($subscription in $subs){
	    $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
	    $subscriptionValues += ($subscription.SubscriptionId);
	    }
	    
	    $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }
    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    $resourceGroup = Read-Host "Please enter the Resource Group of your App"
    
    $appName = Read-Host "Please enter the Name of your App"
    
    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options
    
    if($option -eq 0)
    {
		AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
	    AddExistingVnet $subscriptionId $resourceGroup $appName
    }
	if($option -eq 2)
    {
	    RemoveVnet $subscriptionId $resourceGroup $appName
    }
    
スクリプトのコピーを保存します。このドキュメントでは V2VnetAllinOne.ps1 と呼ばれていましたが、自由に名前を付けることができます。このスクリプトには引数がありません。スクリプトを実行します。スクリプトを実行すると、最初にサインインが求められます。サインイン後、スクリプトはアカウントの詳細を取得し、選択対象となるサブスクリプションの一覧を返します。資格情報を求めるポップアップを除く最初のスクリプト実行はこのようになります。

	PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
	Please Login
	
	Environment           : AzureCloud
	Account               : ccompy@microsoft.com
	TenantId              : 722278f-fef1-499f-91ab-2323d011db47
	SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
	CurrentStorageAccount :
	
	Choose a subscription
	
	1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
	2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
	3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)
	
	Choose an option:
	3
	
	Account      : ccompy@microsoft.com
	Environment  : AzureCloud
	Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
	Tenant       : 722278f-fef1-499f-91ab-2323d011db47
	
	Please enter the Resource Group of your App: hcdemo-rg
	Please enter the Name of your App: v2vnetpowershell
	What do you want to do?
	
	1) Add a NEW Virtual Network to an App
	2) Add an EXISTING Virtual Network to an App
	3) Remove a Virtual Network from an App

このドキュメントの残りの部分では、3 つのオプションのそれぞれについて説明します。

###V2(リソース マネージャー) VNET を作成し、それと統合する###

新しい V2 VNET を作成し、それとアプリを統合するには、[1) Add a NEW Virtual Network to an App] を選択します。VNET の名前を求められます。ここでは v2pshell という名前を付けました。

スクリプトにより、作成される VNET の詳細が与えられます。必要であれば、値を変更できます。この例の実行では、次の設定で VNET を作成しました。

	Virtual Network Name:         v2pshell
	Resource Group Name:          hcdemo-rg
	Gateway Name:                 v2pshell-gateway
	Vnet IP name:                 v2pshell-ip
	Vnet IP config name:          v2pshell-ipconf
	Address Space:                10.0.0.0/8
	Gateway Address Space:        10.5.0.0/16
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):

値を変更する場合、「Y」と入力し、必要な変更を行います。VNET 設定に問題がなければ、変更を求められたときに「N」と入力するか、リターン キーを押します。そこから完了まで、スクリプトが VNET ゲートウェイの作成に入るまでに行う内容の一部が伝えられます。その手順には最大 1 時間かかることがあります。この段階では進捗状況が表示されませんが、ゲートウェイの作成完了時に通知されます。

スクリプトの完了時に **[完了]** と表示されます。この時点で、選択した名前と設定で V2 VNET が作成されます。この新しい VNET もアプリに統合されます。

###既存の V2 VNET とアプリを統合する###

既存の VNET と統合するとき、ゲートウェイまたはポイント対サイトのない V2 VNET を与える場合、スクリプトがそれを設定します。VNET にそれらが既に設定されている場合、アプリ統合に直接入ります。このプロセスを開始するには、[2) Add an EXISTING Virtual Network to an App] オプションを選択します。

このオプションは、既存の V2 VNET のサブスクリプションがアプリのサブスクリプションと同じ場合にのみ機能します。オプション 2 を選択すると、V2 VNET の一覧が提示されます。

	Select a VNET to integrate with
	
	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2
	
	Choose an option:
	5

統合する VNET を選択します。ポイント対サイトが有効なゲートウェイを既に用意している場合、スクリプトはアプリを VNET と統合します。ゲートウェイがない場合、ゲートウェイ サブネットを指定する必要があります。ゲートウェイ サブネットは VNET のアドレス空間に置く必要があります。他のサブネットに置くことはできません。VNET にゲートウェイがなく、この手順を実行する場合、次のようになります。

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

この例では、次の設定で VNET ゲートウェイを作成しました。

	Virtual Network Name:         v2pshell2
	Resource Group Name:          vnetdemo-rg
	Gateway Name:                 v2pshell2-gateway
	Vnet IP name:                 v2pshell2-ip
	Vnet IP config name:          v2pshell2-ipconf
	Address Space:                172.16.0.0/16
	Gateway Address Space:        172.16.1.0/26
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):
	Creating App assocation to VNET

設定は変更できます。変更しない場合、リターン キーを押します。ゲートウェイが作成され、アプリが VNET に関連付けられます。ゲートウェイの作成時間は 1 時間である点にご注意ください。すべてが完了すると、[完了] と表示されます。

###V2 VNET からアプリを切断する###

V2 VNET からアプリを切断してもゲートウェイは停止せず、ポイント対サイトは無効になりません。他のことに使用していることがあります。自分が与えた以外のアプリから切断されることもありません。このアクションを実行するには、[3) Remove a Virtual Network from an App] を選択します。選択後、次のような画面が表示されます。

	Currently connected to VNET v2pshell
	
	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

スクリプトに「delete (削除)」と表示されますが、VNET は削除されません。統合を削除するだけです。実行する操作を確定すると、コマンドが速やかに処理され、完了後、「True」と表示されます。

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->
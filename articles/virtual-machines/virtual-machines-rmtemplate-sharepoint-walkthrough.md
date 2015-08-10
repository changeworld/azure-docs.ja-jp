<properties
	pageTitle="3 台のサーバーから成る SharePoint ファーム リソース マネージャーのテンプレート"
	description="3 台のサーバーから成る SharePoint ファームの Azure リソース マネージャーのテンプレートの構造に関する段階的な説明です。"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# 3 台のサーバーから成る SharePoint ファーム リソース マネージャーのテンプレート

このトピックでは、3 台のサーバーから成る SharePoint ファームの azuredeploy.json テンプレート ファイルの構造について段階的に説明します。[こちら](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json)からブラウザーでこのテンプレートの内容を見ることができます。

または、azuredeploy.json ファイルのローカル コピーを調べるために、ファイルの場所としてローカル フォルダー (たとえば、C:\\Azure\\Templates\\SharePointFarm) を指定してファイルを作成することもできます。Azure PowerShell のコマンド プロンプトでフォルダー名を入力して次のコマンドを実行します。

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

テキスト エディターまたは適当なツールで azuredeploy.json テンプレートを開きます。テンプレート ファイルの構造および各セクションの目的を次に示します。

## "parameters" セクション

"parameters" セクションには、このテンプレートにデータを入力するために使用するパラメーターを指定します。最大 50 個のパラメーターを定義することができます。Azure の場所を表すパラメーターの例を次に示します。

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## "variables" セクション

"variables" セクションには、このテンプレート全体で使用できる変数を指定します。最大 100 個の変数を定義することができます。次に例をいくつか示します。

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## "resources" セクション

**"resources"** セクションには、SharePoint ファームのリソースをリソース グループにデプロイするために必要な情報を指定します。最大 250 個のリソースを定義することができますが、リソースの依存関係の深さは 5 レベルまでしか定義できません。

このセクションには、次のようにいくつかのサブセクションがあります。

### Microsoft.Storage/storageAccounts  

このセクションは、ファームのすべての VHD リソースおよびディスク リソース用に新しいストレージ アカウントを作成します。ストレージ アカウントの JSON コードを次に示します。

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

これらのセクションは、各仮想マシンにインターネット経由でアクセスできるようにするためのパブリック IP アドレスのセットを作成します。たとえば次のようになります。

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

これらのセクションは、デプロイの各階層に 1 つずつ、3 つの可用性セットを作成します。

- Active Directory ドメイン コントローラー
- SQL Server クラスター
- SharePoint サーバー

たとえば次のようになります。

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

このセクションは、仮想マシンが配置される、3 つのサブネット (デプロイの階層ごとに 1 つ) を持つクラウド専用仮想ネットワークを作成します。JSON コードを次に示します。

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

これらのセクションは、インターネットからの着信トラッフィック用の NAT とフィルタリングを提供する、仮想マシンごとのロード バランサー インスタンスを作成します。この設定により、ロード バランサーごとに、フロントエンド、バックエンド、着信 NAT のルールが構成されます。たとえば、各仮想マシンに対してはリモート デスクトップ トラフィックのルールが設定され、SharePoint サーバーに対してはインターネットからの着信 Web トラフィック (TCP ポート 80) を許可するルールが設定されます。SharePoint サーバーの例を次に示します。


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

これらのセクションは、仮想マシンごとにネットワーク インターフェイスを 1 つ作成し、ドメイン コントローラーの静的 IP アドレスを構成します。次に、ドメイン コントローラーのネットワーク インターフェイスの例を示します。

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

以下のセクションで、デプロイ内に 3 つの仮想マシンを作成および構成します。

最初のセクションでは、次の手順に従ってドメイン コントローラーを作成して構成します。

- ストレージ アカウント、可用性セット、ネットワーク インターフェイス、ロード バランサーのインスタンスを指定する
- ディスクを追加する
- PowerShell スクリプトを実行してドメイン コントローラーを構成する

JSON コードを次に示します。

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

ドメイン コントローラーの **"name":"UpdateVNetDNS"** で始まる追加のセクションでは、仮想ネットワークの DNS サーバーでドメイン コントローラーの静的 IP アドレスを使用するように構成します。

その後の **"type":"Microsoft.Compute/virtualMachines"** セクションでは、デプロイ内に SQL Server の仮想マシンを作成し、次の手順に従います。

- ストレージ アカウント、可用性セット、ロード バランサー、仮想ネットワーク、ネットワーク インターフェイスを指定する
- ディスクを追加する

追加の **"Microsoft.Compute/virtualMachines/extensions"** セクションでは、SQL Server を構成する PowerShell スクリプトを呼び出します。

その後の **"type": "Microsoft.Compute/virtualMachines"** セクションでは、デプロイ内に SharePoint の仮想マシンを作成し、ストレージ アカウント、可用性セット、ロード バランサー、仮想ネットワーク、およびネットワーク インターフェイスを指定します。追加の **"Microsoft.Compute/virtualMachines/extensions"** セクションでは、SharePoint ファームを構成する PowerShell スクリプトを呼び出します。

JSON ファイルの **"resources"** セクションのサブセクションの構成は次のとおりです。

1.	複数の仮想マシンをサポートするために必要な Azure インフラストラクチャの要素 (ストレージ アカウント、パブリック IP アドレス、可用性セット、仮想ネットワーク、ネットワーク インターフェイス、ロード バランサーのインスタンス) を作成します。
2.	ドメイン コントローラーの仮想マシンを作成し (以前作成された、Azure インフラストラクチャの共通および特定の要素を使用)、データ ディスクを追加して、PowerShell スクリプトを実行します。さらに、ドメイン コントローラーの静的 IP アドレスを使用するように仮想ネットワークを更新します。
3.	SQL Server の仮想マシンを作成し (以前ドメイン コントローラー用に作成された、Azure インフラストラクチャの共通および特定の要素を使用)、データ ディスクを追加します。さらに、SQL Server を構成する PowerShell スクリプトを実行します。
4.	SharePoint サーバーの仮想マシンを作成し (以前作成された、Azure インフラストラクチャの共通および特定の要素を使用)、SharePoint ファームを構成する PowerShell スクリプトを実行します。

独自の JSON テンプレートで Azure に多階層のインフラストラクチャを構築するには、同様の手順に従う必要があります。

1.	デプロイに必要な Azure インフラストラクチャの各種要素を作成します。これには、共通の要素 (ストレージ アカウント、仮想ネットワーク)、階層に固有の要素 (可用性セット)、仮想マシン固有の要素 (パブリック IP アドレス、可用性セット、ネットワーク インターフェイス、ロード バランサー インスタンス) があります。
2.	アプリケーションの階層 (認証、データベース、Web など) ごとにサーバーを作成および構成します。このとき、共通の要素 (ストレージ アカウント、仮想ネットワーク)、階層に固有の要素 (可用性セット)、仮想マシン固有の要素 (パブリック IP アドレス、ネットワーク インターフェイス、ロード バランサー インスタンス) を使用します。

詳細については、「[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。

## その他のリソース

[Azure リソース マネージャーにおける Azure Compute、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)

[Azure リソース マネージャーの概要](resource-group-overview.md)

[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)

[Virtual Machines のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=July15_HO5-->
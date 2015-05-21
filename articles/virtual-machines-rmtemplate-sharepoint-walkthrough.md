<properties 
	pageTitle="高可用性 SharePoint ファーム リソース マネージャーのテンプレート" 
	description="高可用性 SharePoint ファームの Azure リソース マネージャーのテンプレートの構造についての順を追った説明です。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# 高可用性 SharePoint ファーム リソース マネージャーのテンプレート

このトピックでは、高可用性 SharePoint ファームの azuredeploy.json テンプレート ファイルの構造について段階的に説明します。

## "parameters" セクション

"parameters" には、このテンプレートにデータを入力するために使用するパラメーターを指定します。最大 50 個のパラメーターを定義することができます。Azure の場所を表すパラメーターの例を次に示します。

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

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## "resources" セクション

**"resources"** セクションには、SharePoint ファームのリソースをリソース グループにデプロイするために必要な情報を指定します。最大 250 個のリソースを定義することができますが、リソースの依存関係の深さは 5 レベルまでしか定義できません。

このセクションには、次のようにいくつかのサブセクションがあります。

### Microsoft.Storage/storageAccounts  

このセクションは、ファームのすべての VHD リソースおよびディスク リソース用に新しいストレージ アカウントを作成します。ストレージ アカウントの JSON コードを次に示します。

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

これらのセクションは、各仮想マシンにインターネット経由でアクセスできるようにするためのパブリック IP アドレスのセットを作成します。たとえば次のようになります。

	{
		"apiVersion": "2014-12-01-preview",
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

これらのセクションは、デプロイメントの各階層に 1 つずつ、4 つの可用性セットを作成します。

- Active Directory ドメイン コントローラー
- SQL Server クラスター
- アプリケーション層サーバー
- Web 層サーバー

たとえば次のようになります。

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

このセクションは、仮想マシンが配置される、4 つのサブネット \(デプロイメントの階層ごとに 1 つ\) を持つクラウド専用仮想ネットワークを作成します。JSON コードを次に示します。

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

これらのセクションは、インターネットからの着信トラッフィック用の NAT とフィルタリングを提供する、仮想マシンごとのロード バランサー インスタンスを作成します。この設定により、ロード バランサーごとに、フロントエンド、バックエンド、着信 NAT のルールが構成されます。たとえば、各仮想マシンに対してはリモート デスクトップ トラフィックのルールが設定され、Web 層サーバーに対してはインターネットからの着信 Web トラフィック \(TCP ポート 80\) を許可するルールが設定されます。Web 層サーバーの例を次に示します。

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

これらのセクションは、仮想マシンごとにネットワーク インターフェイスを 1 つ作成し、ドメイン コントローラーの静的 IP アドレスを構成します。次に、プライマリ ドメイン コントローラーのネットワーク インターフェイスの例を示します。

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

これらのセクションはデプロイメント内に 9 つの仮想マシンを作成および構成します。

最初の 2 つの主要なセクションで、ドメイン コントローラーをデプロイメントに作成して構成します。各セクションの内容は次のとおりです。

- 各ドメイン コントローラーの仮想マシンに対し、ストレージ アカウント、可用性セット、ネットワーク インターフェイス、ロード バランサーのインスタンスを指定する
- 各ドメイン コントローラーの仮想マシンにディスクを追加する
- ドメイン コントローラーとして仮想マシンを構成するための PowerShell スクリプトを実行する

次に、プライマリ ドメイン コントローラーの例を示します。

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


各ドメイン コントローラーの後の **"name":"UpdateVNetDNS"** で始まる追加のセクションでは、仮想ネットワークの DNS サーバーで 2 つのドメイン コントローラーの静的 IP アドレスを使用するように構成します。

その後の 3 つの **"type":"Microsoft.Compute/virtualMachines"** セクションでは、デプロイメント内に SQL Server クラスターの仮想マシンを作成します。各セクションの内容は次のとおりです。

- 各仮想マシンに対し、ストレージ アカウント、可用性セット、ロード バランサー、仮想ネットワーク、ネットワーク インターフェイスを指定します。
- 各 SQL サーバーにディスクを 2 つ追加します。

追加の **"Microsoft.Compute/virtualMachines/extensions"** セクションでは、PowerShell スクリプトを呼び出して、SQL Server クラスターの仮想マシンと SQL サーバー クラスターを構成し、AlwaysOn 可用性グループを有効にします。

次の 4 つの **"type": "Microsoft.Compute/virtualMachines"** セクションでは、デプロイメント内に SharePoint ファームの仮想マシンを作成します。各セクションで、仮想マシンごとに、ストレージ アカウント、可用性セット、ロード バランサー、仮想ネットワーク、ネットワーク インターフェイスを指定します。

追加の **"Microsoft.Compute/virtualMachines/extensions"** セクションでは、SharePoint ファームとして SharePoint サーバーを構成する PowerShell スクリプトを呼び出します。

JSON ファイルの **"resources"** セクションのサブセクションの構成は次のとおりです。

1.	複数の仮想マシンをサポートするために必要な Azure インフラストラクチャの要素 \(ストレージ アカウント、パブリック IP アドレス、可用性セット、仮想ネットワーク、ネットワーク インターフェイス、ロード バランサーのインスタンス\) を作成します。
2.	ドメイン コントローラーの仮想マシンを作成し \(以前作成された、Azure インフラストラクチャの共通および特定の要素を使用\)、データ ディスクを追加し、PowerShell スクリプトを実行します。さらに、ドメイン コントローラーの静的 IP アドレスを使用するように仮想ネットワークを更新します。
3.	SQL Server クラスターの仮想マシンを作成し \(以前ドメイン コントローラー用に作成された、Azure インフラストラクチャの共通および特定の要素を使用\)、データ ディスクを追加します。さらに、クラスターと SQL Server AlwaysOn 可用性グループを構成する PowerShell スクリプトを実行します。
4.	SharePoint サーバーの仮想マシンを作成し \(以前作成された、Azure インフラストラクチャの共通および特定の要素を使用\)、データ ディスクを追加します。さらに、SharePoint ファームを構成する PowerShell スクリプトを実行します。

独自の JSON テンプレートで Azure に多階層のインフラストラクチャを構築するには、同様の手順に従う必要があります。

1.	デプロイメントに必要な Azure インフラストラクチャの各種要素を作成します。これには、共通の要素 \(ストレージ アカウント、仮想ネットワーク\)、階層に固有の要素 \(可用性セット\)、仮想マシン固有の要素 \(パブリック IP アドレス、可用性セット、ネットワーク インターフェイス、ロード バランサー インスタンス\) があります。
2.	アプリケーションの階層 \(認証、データベース、Web など\) ごとにサーバーを作成および構成します。このとき、共通の要素 \(ストレージ アカウント、仮想ネットワーク\)、階層に固有の要素 \(可用性セット\)、仮想マシン固有の要素 \(パブリック IP アドレス、ネットワーク インターフェイス、ロード バランサー インスタンス\) を使用します。

詳細については、「[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。


## その他のリソース

[Azure リソース マネージャーにおける Azure コンピューティング、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)

[Azure リソース マネージャーの概要](resource-group-overview.md)

[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)

[Virtual Machines のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->

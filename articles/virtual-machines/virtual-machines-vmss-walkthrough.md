<properties
	pageTitle="仮想マシン スケール セットの自動スケール | Microsoft Azure"
	description="初めての Azure 仮想マシン スケール セットの作成と管理を開始します"
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
	ms.date="11/19/2015"
	ms.author="davidmu"/>

# 仮想マシン スケール セットでのマシンの自動スケール

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md)。

仮想マシン スケール セットを使用すると、まったく同じ仮想マシンを簡単にまとめてデプロイし、管理することができます。スケール セットは、最高水準のスケーラビリティが要求されるアプリケーションのための、拡張性が高くカスタマイズしやすいコンピューティング レイヤーです。Windows プラットフォーム イメージのほか、Linux プラットフォーム イメージ、カスタム イメージ、拡張機能をサポートしています。スケール セットの詳細については、[仮想マシン スケール セット](virtual-machines-vmss-overview.md)に関するページを参照してください。

このチュートリアルでは、Windows 仮想マシンのスケール セットを作成し、複数のマシンをまとめて自動的にスケールする方法を紹介しています。そのためには、Azure リソース マネージャー テンプレートを作成し、Azure PowerShell を使ってそれをデプロイする必要があります。テンプレートの詳細については、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」をご覧ください。

このチュートリアルで作成するテンプレートは、テンプレート ギャラリーにあるテンプレートと似ています。詳細については、[Windows VM と Jumpbox を使った単純な VM スケール セットのデプロイ](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview)に関するページを参照してください。

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [virtual-machines-vmss-preview](../../includes/virtual-machines-vmss-preview-include.md)]

## 手順 1: リソース グループとストレージ アカウントの作成

1.	**Microsoft Azure にサインイン**します。Microsoft Azure PowerShell ウィンドウを開いて **Login-AzureRmAccount** を実行します。

2.	**リソース グループを作成します**。リソースはすべてリソース グループにデプロイする必要があります。このチュートリアルでは、リソース グループに **vmss-test1** という名前を付けます。[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx) を参照してください。

3.	**ストレージ アカウントを新しいリソース グループにデプロイします**。仮想マシン スケール セットを単純化するために、このチュートリアルでは、いくつかのストレージ アカウントを使用しています。[New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) を使用して、**vmssstore1** という名前のストレージ アカウントを作成します。この後の手順に備えて、Azure PowerShell ウィンドウは開いたままにしておいてください。

## 手順 2: テンプレートの作成
Azure リソース マネージャー テンプレートを使用すると、Azure リソースとそれに関連するデプロイ パラメーターを JSON 形式で記述することによって、それらのリソースをまとめてデプロイし、管理することができます。

1.	任意のテキスト エディターで C:\\VMSSTemplate.json ファイルを作成し、このテンプレートをサポートする初期 JSON 構造体を追加します。

	```
	{
		"$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
		}
		"variables": {
		}
		"resources": [
		]
	}
	```

2.	パラメーターは必須ではありませんが、指定した方がテンプレートは管理しやすくなります。テンプレートに必要な値を指定したり、値の型を記述したり、既定値 (必要な場合) を指定したりすることができるほか、パラメーターに許容される値を指定することもできます。

	これらのパラメーターは、テンプレートに追加した parameters を親要素として、その下に追加します。

	- スケール セット内のマシンにアクセスするために使用する Jumpbox 仮想マシンの名前。
	- テンプレートの保存先となるストレージ アカウントの名前。
	- スケール セットに最初に作成する仮想マシンのインスタンス数。
	- 仮想マシンの管理者アカウントの名前とパスワード。
	- スケール セット内の仮想マシンに使用するストレージ アカウントの名前のプレフィックス。


	```
	"vmName": {
		"type": "string"
	},
	"vmSSName": {
		"type": "string"
	},
	"instanceCount": {
		"type": "string"
	},
	"adminUsername": {
		"type": "string"
	},
	"adminPassword": {
		"type": "securestring"
	},
	"storageAccountName": {
		"type": "string"
	},
	"vmssStoragePrefix": {
		"type": "string"
	}
	```


3.	絶えず値が変化する場合や、複数のパラメーター値を組み合わせて値を作成する必要がある場合は、テンプレートの中で変数を使用して指定できます。

	これらの変数は、テンプレートに追加した variables を親要素として、その下に追加します。

	- ネットワーク インターフェイスで使用されている DNS 名。
	- スケール セットで使用する仮想マシンのサイズ。仮想マシン サイズの詳細については、[仮想マシンのサイズ](virtual-machines-size-specs.md)に関するページを参照してください。
	- スケール セット内の仮想マシン上で動作するオペレーティング システムを定義するためのプラットフォーム イメージ情報。イメージの選択の詳細については、[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](resource-groups-vm-searching.md)に関するページを参照してください。
	- 仮想ネットワークとサブネットに使用する IP アドレスの名前とプレフィックス。
	- 仮想ネットワーク、ロード バランサー、ネットワーク インターフェイスの名前と識別子。
	- スケール セット内のマシンに関連付けられているアカウントのストレージ アカウントの名前。
	- 仮想マシンにインストールする診断拡張機能の設定。診断拡張機能の詳細については、「[Azure リソース マネージャー テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](virtual-machines-extensions-diagnostics-windows-template.md)」を参照してください。

	```
	"dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
	"dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
	"vmSize": "Standard_A0",
	"imagePublisher": "MicrosoftWindowsServer",
	"imageOffer": "WindowsServer",
	"imageVersion": "2012-R2-Datacenter",
	"addressPrefix": "10.0.0.0/16",
	"subnetName": "Subnet",
	"subnetPrefix": "10.0.0.0/24",
	"publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
	"publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
	"loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
	"virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
	"nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
	"nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	"publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
	"publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
	"lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	"nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
	"frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	"storageAccountType": "Standard_LRS",
	"storageAccountPrefix": [ "a", "g", "m", "s", "y" ],
	"diagnosticsStorageAccountName": "[concat('a', parameters('vmssStorageSuffix'))]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="ja-JP"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
	```

4.	このチュートリアルでは、次のリソースと拡張機能をデプロイします。

 - Microsoft.Storage/storageAccounts
 - Microsoft.Network/virtualNetworks
 - Microsoft.Network/publicIPAddresses
 - Microsoft.Network/loadBalancers
 - Microsoft.Network/networkInterfaces
 - Microsoft.Compute/virtualMachines
 - Microsoft.Compute/virtualMachineScaleSets
 - Microsoft.Insights.VMDiagnosticsSettings
 - Microsoft.Insights/autoscaleSettings

	リソース マネージャーのリソースの詳細については、「[Azure リソース マネージャーにおける Compute、Network、Storage プロバイダー](virtual-machines-azurerm-versus-azuresm.md)」を参照してください。

	テンプレートに追加した resources を親要素として、その下にストレージ アカウント リソースを追加します。このテンプレートは、オペレーティング システムのディスクと診断データの保存先として、推奨される 5 つのストレージ アカウントをループを使って作成します。この一連のアカウントは、スケール セットで最大 100 台の仮想マシンをサポートします。これが現時点での最大数となります。各ストレージ アカウントには、変数で定義された文字指定子とテンプレートのパラメーターで指定したサフィックスとを組み合わせた名前が付けられます。

	```
	{
		"type": "Microsoft.Storage/storageAccounts",
		"name": "[concat(variables('storagePrefix')[copyIndex()], parameters('vmssStorageSuffix'))]",
		"apiVersion": "2015-05-01-preview",
		"copy": {
			"name": "storageLoop",
			"count": 5
		},
		"location": "[resourceGroup().location]",
		"properties": {
			"accountType": "[variables('storageAccountType')]"
		}
	},
	```

5.	仮想ネットワーク リソースを追加します。詳細については、「[ネットワーク リソース プロバイダー](../virtual-network/resource-groups-networking.md)」を参照してください。

	```
	{
		"apiVersion": "2015-06-15",
		"type": "Microsoft.Network/virtualNetworks",
		"name": "[variables('virtualNetworkName')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"addressSpace": {
				"addressPrefixes": [
					"[variables('addressPrefix')]"
				]
			},
			"subnets": [
				{
					"name": "[variables('subnetName')]",
					"properties": {
						"addressPrefix": "[variables('subnetPrefix')]"
					}
				}
			]
		}
	},
	```

6.	ロード バランサーとネットワーク インターフェイスによって使用されるパブリック IP アドレス リソースを追加します。

	```
	{
		"apiVersion": "2015-06-15",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP1')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName1')]"
			}
		}
	},
	{
		"apiVersion": "2015-06-15",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP2')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName2')]"
			}
		}
	},
	```

7.	スケール セットで使用するロード バランサー リソースを追加します。詳細については、「[Azure リソース マネージャーによるロード バランサーのサポート](../load-balancer/oad-balancer-arm.md)」を参照してください。

	```
	{
		"apiVersion": "2015-06-15",
		"name": "[variables('loadBalancerName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "loadBalancerFrontEnd",
					"properties": {
						"publicIPAddress": {
							"id": "[variables('publicIPAddressID1')]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "bepool1"
				}
			],
			"inboundNatPools": [
				{
					"name": "natpool1",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('frontEndIPConfigID')]"
						},
						"protocol": "tcp",
						"frontendPortRangeStart": 50000,
						"frontendPortRangeEnd": 50500,
						"backendPort": 3389
					}
				}
			]
		}
	},
	```

8.	Jumpbox 仮想マシンで使用するネットワーク インターフェイス リソースを追加します。


	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/networkInterfaces",
		"name": "[variables('nicName1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Dynamic",
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
						},
						"subnet": {
							"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
						}
					}
				}
			]
		}
	},
	```


9.	同じネットワーク内の仮想マシン リソースを スケール セットとして追加します。仮想マシン スケール セット内のマシンは、パブリック IP アドレスで直接アクセスすることはできません。そのため、スケール セットと同じ仮想ネットワークに Jumpbox 仮想マシンを作成し、Jumpbox 仮想マシンを使用して、その スケール セット内のマシンにリモートからアクセスすることになります。

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[parameters('vmName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
		],
		"properties": {
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[parameters('vmName')]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]"
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[variables('imagePublisher')]",
					"offer": "[variables('imageOffer')]",
					"sku": "[variables('imageVersion')]",
					"version": "latest"
				},
				"osDisk": {
					"name": "davidmuos1",
					"vhd": {
						"uri":  "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"        
				}
			},
			"networkProfile": {
				"networkInterfaces": [
					{
						"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
					}
				]
			}
		}
	},
	```

10.	仮想マシン スケール セット リソースを追加し、スケール セット内のすべての仮想マシンにインストールする診断拡張機能を指定します。このリソースに使用する設定の多くは、仮想マシン リソースと同様です。主な違いは次のとおりです。

	- **capacity** - スケール セット内で初期化する仮想マシンの数を指定します。この値は、instanceCount パラメーターの値を指定することによって設定します。

	- **upgradePolicy** – スケール セット内の仮想マシンに対する更新の適用方法を指定します。Manual を指定すると、テンプレートにおける変更が、新しい仮想マシンにのみ、再デプロイ時に適用されます。Automatic を指定した場合、スケール セット内のすべてのマシンが更新され、再起動されます。

	仮想マシン スケール セットは、dependsOn 要素に指定されたすべてのストレージ アカウントが作成されたときに初めて作成されます。

	```
	{
		"type": "Microsoft.Compute/virtualMachineScaleSets",
		"apiVersion": "2015-06-15",
		"name": "[parameters('vmSSName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Storage/storageAccounts/a', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/g', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/m', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/s', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/y', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
			"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
		],
		"sku": {
			"name": "[variables('vmSize')]",
			"tier": "Standard",
			"capacity": "[parameters('instanceCount')]"
		},
		"properties": {
			"upgradePolicy": {
				"mode": "Manual"
			},
			"virtualMachineProfile": {
				"storageProfile": {
					"osDisk": {
						"vhdContainers": [
							"[concat('https://a', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://g', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://m', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://s', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://y', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]"
						],
						"name": "vmssosdisk",
						"caching": "ReadOnly",
						"createOption": "FromImage"
					},
					"imageReference": {
						"publisher": "[variables('imagePublisher')]",
						"offer": "[variables('imageOffer')]",
						"sku": "[variables('imageVersion')]",
						"version": "latest"
					}
				},
				"osProfile": {
					"computerNamePrefix": "[parameters('vmSSName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"networkProfile": {
					"networkInterfaceConfigurations": [
						{
							"name": "[variables('nicName2')]",
							"properties": {
								"primary": "true",
								"ipConfigurations": [
									{
										"name": "ip1",
										"properties": {
											"subnet": {
												"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
											},
											"loadBalancerBackendAddressPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
												}
											],
											"loadBalancerInboundNatPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
												}
											]
										}
									}
								]
							}
						}
					]
				},
				"extensionProfile": {
					"extensions": [
						{
							"name": "Microsoft.Insights.VMDiagnosticsSettings",
							"properties": {
								"publisher": "Microsoft.Azure.Diagnostics",
								"type": "IaaSDiagnostics",
								"typeHandlerVersion": "1.5",
								"autoUpgradeMinorVersion": true,
								"settings": {
									"xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
									"storageAccount": "[variables('diagnosticsStorageAccountName')]"
								},
								"protectedSettings": {
									"storageAccountName": "[variables('diagnosticsStorageAccountName')]",
									"storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
									"storageAccountEndPoint": "https://core.windows.net"
								}
							}
						}
					]
				}
			}
		}
	},
	```

11.	スケール セットの仮想マシンのプロセッサ使用率に基づく調整方法を定義する autoscaleSettings リソースを追加します。このチュートリアルで重要となる値は次のとおりです。

 - **metricName** - これは、wadperfcounter 変数で定義したパフォーマンス カウンターと同じです。診断拡張機能は、この変数を使用して、**Processor(\_Total)\\% Processor Time** カウンターを収集します。
 - **metricResourceUri** - 仮想マシン スケール セットのリソース識別子です。
 - **timeGrain** – 収集するメトリックの粒度です。このテンプレートでは 1 分に設定しています。
 - **statistic** – 自動スケーリング処理に対応するためのメトリックの集計方法を指定します。指定できる値は Average、Min、Max です。このテンプレートでは、スケール セットに含まれる仮想マシンの平均合計 CPU 使用率を検出しています。
 - **timeWindow** – インスタンス データを収集する時間の範囲です。5 分～ 12 時間の範囲で指定する必要があります。
 - **timeAggregation** – 時間経過に沿って収集されたデータの集計方法を指定します。既定値は Average です。指定できる値は Average、Minimum、Maximum、Last、Total、Count です。
 - **operator** – メトリック データとしきい値を比較するときに用いる演算子です。指定できる値は、Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual です。
 - **threshold** – スケール処理の開始点となる値です。このテンプレートでは、スケール セット内のマシンの平均 CPU 使用率が 50% を超えたとき、スケール セットにマシンが追加されます。
 - **direction** – しきい値に達したときに実行するアクションを指定します。指定できる値は Increase または Decrease です。このテンプレートでは、定義された時間枠 (timeWindow) 内にしきい値が 50% を超えた場合、スケール セット内の仮想マシンの数が増やされます。
 - **type** – 発生させるアクションの種類です。これは ChangeCount に設定する必要があります。
 - **value** – スケール セットに追加する仮想マシンの数または スケール セットから削除する仮想マシンの数です。1 以上の値を設定する必要があります。既定値は 1 です。このテンプレートでは、しきい値に達したときに、スケール セット内の仮想マシンの数が 1 つ増やされます。
 - **cooldown** – 前回のスケーリング処理から次回のスケーリング処理までの待機時間です。1 分～ 1 週間の範囲で指定する必要があります。

	```
	{
		"type": "Microsoft.Insights/autoscaleSettings",
		"apiVersion": "2015-04-01",
		"name": "[concat(parameters('resourcePrefix'),'as1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		],
		"properties": {
			"enabled": true,
			"name": "[concat(parameters('resourcePrefix'),'as1')]",
			"profiles": [
				{
					"name": "Profile1",
					"capacity": {
						"minimum": "1",
						"maximum": "10",
						"default": "1"
					},
					"rules": [
						{
							"metricTrigger": {
								"metricName": "\\Processor(_Total)\\% Processor Time",
								"metricNamespace": "",
								"metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
								"timeGrain": "PT1M",
								"statistic": "Average",
								"timeWindow": "PT5M",
								"timeAggregation": "Average",
								"operator": "GreaterThan",
								"threshold": 50.0
							},
							"scaleAction": {
								"direction": "Increase",
								"type": "ChangeCount",
								"value": "1",
								"cooldown": "PT5M"
							}
						}
					]
				}
			],
			"targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		}
	}
	```

12.	テンプレート ファイルを保存します。    

## 手順 3: ストレージへのテンプレートのアップロード

手順 1. で作成したストレージ アカウントのアカウント名とプライマリ キーがわかっていれば、Microsoft Azure PowerShell ウィンドウからテンプレートをアップロードできます。

1.	手順 1. でデプロイしたストレージ アカウントの名前を指定する変数を Microsoft Azure PowerShell ウィンドウで設定します。

		$StorageAccountName = "vmssstore1"

2.	ストレージ アカウントのプライマリ キーを指定する変数を設定します。

		$StorageAccountKey = "<primary-account-key>"

	このキーは、Azure ポータルでストレージ アカウント リソースを表示しているときにキー アイコンをクリックして取得できます。

3.	ストレージ アカウントのコンテキスト オブジェクトを作成します。ストレージ アカウントに対する操作を検証する際にこのオブジェクトを使用します。

		$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

4.	作成したテンプレートの保存先となる新しいテンプレート コンテナーを作成します。

		$ContainerName = "templates"
		New-AzureStorageContainer -Name $ContainerName -Context $ctx  -Permission Blob

5.	その新しいコンテナーにテンプレート ファイルをアップロードします。

		$BlobName = "VMSSTemplate.json"
		$fileName = "C:" + $BlobName
		Set-AzureStorageBlobContent -File $fileName -Container $ContainerName -Blob  $BlobName -Context $ctx

## 手順 4: テンプレートのデプロイ

テンプレートが完成したら、リソースのデプロイ作業に進むことができます。その作業を開始するには、次のコマンドを使用します。

		New-AzureRmResourceGroupDeployment -Name "vmss-testdeployment1" -ResourceGroupName "vmss-test1" -TemplateUri "https://vmssstore1.blob.core.windows.net/templates/VMSSTemplate.json"

Enter キーを押すと、先ほど割り当てた変数の値を入力するよう求められます。次の値を指定します。

	vmName: vmssvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: vmadminpass1
	storageAccountName: vmssstore1
	resourcePrefix: vmsstest

すべてのリソースが正常にデプロイされるまでに約 15 分かかります。

>[AZURE.NOTE]ポータルの機能を利用してリソースをデプロイすることもできます。具体的な方法については、次のリンク先のページを参照してください: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## 手順 4: リソースの監視

仮想マシン スケール セットに関する情報の一部は、次の方法で入手することができます。

 - Azure ポータル - 現在、ポータルを使用して入手できる情報の量は限られています。
 - [Azure リソース エクスプローラー](https://resources.azure.com/) - スケール セットの現在の状態を調査するうえで最適なツールです。作成した スケール セットのインスタンス ビューは次のパスをたどって表示できます。

		subscriptions > {your subscription} > resourceGroups > vmss-test1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell - 次のコマンドを使用して一部の情報を得ることができます。

		Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmss-test1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

 - 通常の仮想マシンに接続するときと同じように Jumpbox 仮想マシンに接続し、スケール セット内の仮想マシンにリモートからアクセスして個々のプロセスを監視することができます。

>[AZURE.NOTE]スケール セットに関する情報を得ることができる REST API の一覧については、[仮想マシン スケール セット](https://msdn.microsoft.com/library/mt589023.aspx)に関するページを参照してください。

## 手順 5: リソースの削除

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。リソース グループから各リソースを個別に削除する必要はありません。リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。

	Remove-AzureRmResourceGroup -Name vmss-test1

リソース グループを維持する必要がある場合は、スケール セットのみを削除することができます。

	Remove-AzureRmResource -Name vmsstest1 -ResourceGroupName vmss-test1 -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

<!---HONumber=AcomDC_1125_2015-->
<properties
	pageTitle="リソース マネージャー テンプレートを使用した Ubuntu 上での DataStax | Microsoft Azure "
	description="Azure PowerShell または Azure CLI とリソース マネージャー テンプレートを使用し、Ubuntu VM 上に新しい DataStax クラスターを簡単にデプロイする方法について説明します"
	services="virtual-machines"
	documentationCenter=""
	authors="scoriani"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="scoriani"/>

# リソース マネージャー テンプレートを使用した Ubuntu 上での DataStax

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。


DataStax は、Apache Cassandra に基づくソリューションの開発と配布を行う、実績のある業界リーダーです。Apache Cassandra は、商用使用がサポートされている、企業向けの NoSQL 分散データベース テクノロジで、アジャイルで常時稼働、しかも任意のサイズにスケール可能であるとして、広く認められています。DataStax は、Enterprise (DSE) と Community (DSC) の 2 種類の製品を提供しています。また、インメモリ コンピューティング、エンタープライズ レベルのセキュリティ、高速かつ強力な統合分析、エンタープライズ検索などの機能も提供しています。

Azure Marketplace で既に提供されていたものに加え、[Azure PowerShell](../powershell-install-configure.md) または [Azure CLI](../xplat-cli-install.md) を通してデプロイされたリソース マネージャー テンプレートを使用して、Ubuntu VM 上に新しい DataStax クラスターを簡単にデプロイすることも可能になりました。

このテンプレートに基づいて新しくデプロイされるクラスターには、次の図に示すトポロジが実装されます。これ以外のトポロジも、この記事で示したテンプレートをカスタマイズすることで容易に実現できます。

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

パラメーターを使用して、新しい Apache Cassandra クラスターにデプロイするノードの数を定義できます。さらに、DataStax オペレーション センター サービスのインスタンスも同じ仮想ネットワーク内のスタンドアロン VM にデプロイすることができ、クラスターとすべての個別ノードの状態の監視、ノードの追加や削除、そのクラスターに関連するすべての管理タスクの実行などの機能が利用できるようになります。

デプロイが完了すると、構成済みの DNS アドレスを使用して DataStax オペレーション センターの VM インスタンスにアクセスできます。OpsCenter VM には、有効な SSH ポート 22 と、HTTPS 用のポート 8443 があります。オペレーション センターの DNS アドレスには、パラメーターとして入力される *dnsName* と*リージョン*が、`{dnsName}.{region}.cloudapp.azure.com` の形式で含まれます。"West US" リージョンの "datastax" に設定した *dnsName* パラメーターでデプロイを作成した場合、`https://datastax.westus.cloudapp.azure.com:8443` のデプロイの DataStax オペレーション センターの VM にアクセスすることができます。

> [AZURE.NOTE]デプロイで使用される証明書は自己署名証明書で、ブラウザーではそれに対する警告が生成されます。[DataStax](http://www.datastax.com/) Web サイトのプロセスに従い、この証明書を独自の SSL 証明書に置き換えることができます。

Azure リソース マネージャーとこのデプロイに使用するテンプレートに関連する詳細に進む前に、Azure PowerShell または Azure CLI の構成が正常に完了していることを確認してください。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## リソース マネージャー テンプレートで DataStax に基づく Cassandra クラスターを作成する

GitHub テンプレート リポジトリのリソース マネージャー テンプレートを使用して、DataStax に基づく Apache Cassandra クラスターを作成するには、以下の手順に従います。各手順には、Azure PowerShell と Azure CLI の両方の手順が含まれています。

### 手順 1-a: Azure PowerShell を使用してテンプレート ファイルをダウンロードする

JSON テンプレートとその他の関連ファイル用のローカル フォルダーを作成します (例: C:\\Azure\\Templates\\DataStax)。

フォルダー名をローカル フォルダーのフォルダー名に置き換えて、次のコマンドを実行します。

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### 手順 1-b: Azure CLI を使用してテンプレート ファイルをダウンロードする

たとえば次のように任意の Git クライアントを使用して、テンプレート リポジトリ全体を複製します。

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

複製の完了後に、C:\\Azure\\Templates ディレクトリの **datastax-on-ubuntu** フォルダーを探します。

### 手順 2. (省略可能) テンプレート パラメーターを理解する

DataStax に基づく Apache Cassandra クラスターのような自明ではないソリューションをデプロイする場合、必要な複数の設定を処理するために構成パラメーターのセットを指定する必要があります。テンプレートの定義でこれらのパラメーターを宣言することで、デプロイ時に外部ファイルまたはコマンド ラインを通して値を指定することができます。

azuredeploy.json ファイルの先頭にある "parameters" セクションで、DataStax クラスターを構成するためにテンプレートに必要なパラメーター セットが見つかります。このテンプレートの azuredeploy.json ファイルの parameters セクションの例を次に示します。

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the storage account where the virtual machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the operations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
		"adminUsername": {
			"type": "string",
			"metadata": {
				"Description": "Administrator user name used when provisioning virtual machines"
			}
		},
		"adminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Administrator password used when provisioning virtual machines"
			}
		},
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

各パラメーターには、データ型や許容値などの詳細が記述されています。これにより、対話モード (Azure PowerShell や Azure CLI など) でのテンプレート実行時に渡されるパラメーターだけでなく、必要なパラメーターとその記述のリストを解析することによって動的に構築できる自己発見型の UI を検証することが可能になります。

### 手順 3-a: Azure PowerShell とテンプレートを使用して DataStax クラスターをデプロイする

すべてのパラメーターの実行時の値を含む JSON ファイルを作成することによって、デプロイ用の parameters ファイルを準備します。このファイルは、単一のエンティティとしてデプロイ コマンドに渡されます。parameters ファイルが指定されていない場合、Azure PowerShell はテンプレートで指定されているすべての既定値を使用し、残りの値を入力するように要求します。

次に、azuredeploy-parameters.json ファイルのパラメーター セットの例を示します。

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Azure のデプロイ名、リソース グループ名、Azure の場所、JSON デプロイ ファイルの保存先フォルダーを指定します。次のコマンドを実行します。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

**New-AzureResourceGroupDeployment** コマンドを実行すると、JSON parameters ファイルからパラメーター値が抽出され、それに基づきテンプレートが実行されます。さまざまな環境 (テスト環境、運用環境など) によって複数のパラメーター ファイルを定義し、使用することは、テンプレートの再利用を推進し、複雑な多環境ソリューションを単純化します。

デプロイ時には新しい Azure ストレージ アカウントを作成する必要があるため、ストレージ アカウント パラメーターとして指定する名前は一意であり、かつ Azure ストレージ アカウントのすべての要件 (小文字と数字のみ) を満たしている必要がある点に留意してください。

デプロイ中およびデプロイ後には、プロビジョニング中に行われたすべての要求 (発生したすべてのエラーを含む) を確認できます。

確認するには、[Azure ポータル](https://portal.azure.com)に移動し、次の操作を行います。

- 左側のナビゲーション バーで **[参照]** をクリックし、下へスクロールして **[リソース グループ]** をクリックします。  
- 作成したリソース グループをクリックして、[リソース グループ] ブレードを表示します。  
- [リソース グループ] ブレードの **[監視]** 部分にある [イベント] 棒グラフをクリックすると、デプロイのイベントを表示できます。
- 個々のイベントをクリックすることで、テンプレートのために実行された各操作の詳細にドリル ダウンできます。

テスト後に、このリソース グループとそのすべてのリソース (ストレージ アカウント、仮想マシン、仮想ネットワーク) を削除する必要がある場合は、次のコマンドを使用します。

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### 手順 3-b: Azure CLI とテンプレートを使用して DataStax クラスターをデプロイする

Azure CLI を使用して DataStax クラスターをデプロイするには、まず、名前と場所を指定してリソース グループを作成します。

	azure group create dsc "West US"

このリソース グループ名、JSON テンプレート ファイルの場所、および parameters ファイルの場所 (詳細については、前述の Azure PowerShell に関する説明を参照) を、次のコマンドに渡します。

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

次のコマンドを使用することにより、個々のリソース デプロイのステータスをチェックできます。

	azure group deployment list dsc

## DataStax のテンプレート構造とファイル編成について

堅牢で再利用可能なリソース マネージャー テンプレートを設計するには、さらに検討を加え、DataStax のような複雑なソリューションのデプロイ時に必要な、複雑で相互に関連する一連のタスクを整理する必要があります。関連する拡張機能によるスクリプト実行に加え、リソース マネージャーのテンプレート リンク機能やリソース ループ機能を活用することで、実質的にはどのような複雑なテンプレート ベースのデプロイにも再利用可能なモジュール式の手法を実行できます。

このセクションでは、DataStax クラスターの azuredeploy.json ファイルの構造について、順を追って説明します。

### "parameters" セクション

azuredeploy.json の "parameters" セクションには、このテンプレートで使用される、変更可能なパラメーターを指定します。前述の azuredeploy-parameters.json ファイルは、テンプレート実行時に、値を azuredeploy.json の "parameters" セクションに渡すために使用されます。

### "variables" セクション

"variables" セクションには、このテンプレート全体で使用できる変数を指定します。これには、実行時に定数または計算済みの値に設定される、いくつかのフィールド (JSON データ型またはフラグメント) が格納されます。次に、この Datastax テンプレートの "variables" セクションの例を示します。

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

この例を詳しく調べると、2 つの異なる手法を確認できます。次の 1 つ目のフラグメントでは、**osSettings** 変数は入れ子になった JSON 要素で、4 組のキー値のペアを格納します。

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
次の 2 つ目のフラグメントでは、**scripts** 変数は JSON 配列で、実行時にテンプレート言語関数 (concat)、および別の変数に文字列定数を加えた値を使用して各要素が計算されます。

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### "resources" セクション

"resources" セクションではアクションの大部分が発生します。このセクションを注意深く調べると、2 つの異なるケースがあることがすぐにわかります。1 つ目は、型 `Microsoft.Resources/deployments`の定義されている要素です。基本的に、メインのデプロイ内に入れ子になったデプロイの呼び出しを意味します。**templateLink** 要素 (および関連するバージョン プロパティ) を通して、一連のパラメーターを入力として渡しながら呼び出されるリンク済みテンプレート ファイルを指定できます (次のフラグメントを参照)。

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

この 1 つ目の例から明らかなように、このシナリオの azuredeploy.json は、ある種のオーケストレーション メカニズムとして編成され、他の数多くのテンプレート ファイルを呼び出しています。各ファイルは、必要なデプロイ アクティビティの一部を担っています。

特に、このデプロイには次のリンク済みテンプレートが使用されます。

-	**shared-resource.json**: デプロイ全体で共有されるすべてのリソースの定義を格納します。たとえば、VM の OS ディスクおよび仮想ネットワークの格納に使用されるストレージ アカウントです。
-	**opscenter-resources.json**: OpsCenter VM と、すべての関連するリソース (ネットワーク インターフェイスやパブリック IP アドレスなど) をデプロイします。
-	**opscenter-install-resources.json**: OpsCenter VM の拡張機能 (Linux 用のカスタム スクリプト) をデプロイします。この拡張機能は、VM 内での OpsCenter サービスのセットアップに必要な bash スクリプト ファイル (opscenter.sh) を呼び出します。
-	**ephemeral-nodes-resources.json**: すべてのクラスター ノードの VM と、接続されているリソース (ネットワーク カード、プライベート IP など) をデプロイします。このテンプレートは、VM 拡張機能 (Linux 用のカスタム スクリプト) も同様にデプロイし、bash スクリプト (dsenode.sh) を呼び出して各ノードに Apache Cassandra の構成要素を物理的にインストールします。

この最後のテンプレートは、テンプレート開発の観点から見て最も興味深いテンプレートの 1 つです。このテンプレートの使用方法について、詳しく見てみましょう。注意が必要な重要な概念は、どのようにすれば 1 つのテンプレート ファイルによって単一の種類のリソースの複数のコピーをデプロイできるか、また、各インスタンスが必要な設定に対して一意の値を設定できるかということです。この概念は、**リソース ループ**と呼ばれています。

ephemeral-nodes-resources.json が、メインの azuredeploy.json ファイル内から呼び出される場合、*nodeCount* という名前のパラメーターがパラメーター リストの一部として提供されます。下のフラグメントで表されているように、子テンプレート内では、複数のコピーにデプロイする必要のある各リソースの **copy** 要素の内部で、*nodeCount* (クラスター内のデプロイするノードの数) が使用されます。デプロイ済みリソースの異なるインスタンスごとに一意の値を指定する必要があるすべての設定に対して、**copyindex()** 関数を使用して、特定のリソース ループ作成における現在のインデックスを示す数値を取得できます。次のフラグメントでは、DataStax クラスター ノードに対して作成される複数 VM に、この概念が適用されていることがわかります。

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

リソース作成におけるもう 1 つの重要な概念は、**dependsOn** JSON 配列からわかるように、リソース間の依存関係と優先順位を指定できる点です。このテンプレートでは、各ノードに Apache Cassandra インスタンスのバックアップとスナップショットをホストするために使用できる 1 TB のディスク ("dataDisks" を参照) も接続されます。

接続されたディスクは、dsenode.sh スクリプト ファイルの実行によってトリガーされるノード準備アクティビティの一部としてフォーマットされます。そのスクリプトの最初の行は、別のスクリプトを呼び出しています。

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh は、azure-quickstart-tempates GitHub リポジトリ内の **shared\_scripts\\ubuntu** フォルダーの一部であり、ディスクのマウント、フォーマット、ストライピングのための便利な機能を含みます。これらの機能は、リポジトリ内のすべてのテンプレートで使用できます。

注意が必要なもう 1 つの興味深いフラグメントとして、CustomScriptForLinux VM 拡張機能に関連するものがあります。これらは、各クラスター ノード (および OpsCenter インスタンス) に依存関係を持つ別の種類のリソースとしてインストールされ、仮想マシン用に記述された同じリソース ループ メカニズムを利用します。

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

このデプロイに含まれる他のファイルを熟知することで、Azure リソース マネージャー テンプレートを活用しながら、任意のテクノロジに基づく複数ノード ソリューションの複雑なデプロイ戦略を編成および調整するために必要な、すべての詳細とベスト プラクティスを理解することができます。必須ではありませんが、次の図で表されているようにテンプレート ファイルを構築する手法をお勧めします。

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

この手法では、基本的に次のことを提案しています。

-	すべてのデプロイ アクティビティに対する中心的なオーケストレーション ポイントとして、核となるテンプレート ファイルを定義し、サブテンプレートの実行を呼び出すためにテンプレート リンクを活用します。
-	他のすべてのデプロイ タスク (ストレージ アカウント、仮想ネットワーク構成など) の間で共有されるすべてのリソースをデプロイする、特定のテンプレート ファイルを作成します。これは、共通のインフラストラクチャに関して類似した要件を持つデプロイ間で頻繁に再利用できます。
-	特定のリソースに固有のスポット要件用にオプションのリソース テンプレートを含みます。
-	リソース グループの同一メンバー (クラスター内のノードなど) に対して、一意のプロパティを持つ複数のインスタンスをデプロイするため、リソース ループを活用する特定のテンプレートを作成します。
-	すべてのデプロイ後のタスク (製品のインストールや構成など) について、スクリプト デプロイの拡張機能を活用し、各テクノロジに固有のスクリプトを作成します。

詳細については、「[Azure リソース マネージャー テンプレートの言語](../resource-group-authoring-templates.md)」を参照してください。

<!---HONumber=Oct15_HO3-->
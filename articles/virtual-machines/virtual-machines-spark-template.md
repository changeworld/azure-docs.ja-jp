<properties
	pageTitle="Ubuntu リソース マネージャー テンプレートでの Spark"
	description="Azure PowerShell または Azure CLI とリソース マネージャー テンプレートを使用し、Ubuntu VM 上に新しい Spark クラスターを簡単にデプロイする方法について説明します"
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# リソース マネージャー テンプレートを使用した Ubuntu 上での Spark

Apache Spark は、大規模なデータ処理のための高速エンジンです。Spark には、循環型のデータ フローとインメモリ コンピューティングをサポートする高度な DAG 実行エンジンがあります。また、HDFS、Spark、HBase、S3 など、多様なデータ ソースにアクセスできます。

Spark は、Mesos や YARN クラスター マネージャーで実行されるだけでなく、単純なスタンドアロン型のデプロイ モードも用意されています。このチュートリアルでは、サンプルの Azure リソース マネージャー テンプレートと、[Azure PowerShell](../powershell-install-configure.md) または [Azure CLI](../xplat-cli.md) を使用して、Ubuntu VM に Spark Cluster をデプロイする手順について説明します。

このテンプレートで、Ubuntu 仮想マシンに Spark クラスターがデプロイされます。このテンプレートでは、インストールに必要なストレージ アカウント、仮想ネットワーク、可用性セット、パブリック IP アドレス、およびネットワーク インターフェイスも準備されます。Spark クラスターはサブネットの背後に作成されるので、パブリック IP で Spark クラスターにアクセスすることはできません。デプロイの一部として、オプションの “jump box” をデプロイできます。この “jump box” はサブネットにもデプロイされる Ubuntu VM ですが、接続できる開かれた SSH ポートでパブリック IP アドレスを公開*します*。“jump box” からは、サブネット内にあるすべての Spark VM に SSH で接続できます。

このテンプレートでは、「小」、「中」、「大」の Spark クラスター セットアップを指定するために、「T シャツのサイズ」の概念を使用します。テンプレートの言語がさらに動的なテンプレートのサイズ指定をサポートするようになったら、この概念は Spark クラスターのマスター ノード数、スレーブ ノード数、VM サイズなどの指定に変更される可能性があります。今のところは、azuredeploy.json で定義されている VM のサイズおよびマスターとスレーブの数は、変数 **tshirtSizeS**、**tshirtSizeM**、および **tshirtSizeL** で見ることができます。

- S: 1 マスター、1 スレーブ
- S: 1 マスター、4 スレーブ
- S: 1 マスター、6 スレーブ

このテンプレートに基づいて新しくデプロイされるクラスターには、次の図に示すトポロジが実装されます。これ以外のトポロジも、この記事で示したテンプレートをカスタマイズすることで容易に実現できます。

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

上の図のように、デプロイ トポロジは次の要素から構成されます。

-	新しく作成された仮想マシンの OS ディスクをホストする新しいストレージ アカウント。
-	サブネットを含む仮想ネットワーク。この仮想ネットワーク内には、テンプレートで作成されたすべての仮想マシンがプロビジョニングされます。
-	マスター ノードとスレーブ ノードの可用性セット。
-	新しく作成された可用性セット内のマスター ノード。
-	同じ仮想サブネットで実行されている 4 つのスレーブ ノードと、マスター ノードとしての可用性セット。
-	同じ仮想ネットワーク内にあるジャンプ ボックス VM と、クラスターのアクセスに使用できるサブネット。

Spark バージョン 3.0.0 は既定のバージョンです。Spark リポジトリにある任意のビルト済みのバイナリに変更することもできます。また、スクリプトで、ソースのビルドのコメントを解除するプロビジョニングもあります。静的 IP アドレスは、各 Spark マスター ノード 10.0.0.10 に割り当てられます。テンプレート内から IP アドレスの一覧を動的に作成できない現在の制限を回避するために、静的 IP アドレスは、各 Spark スレーブ ノードに割り当てられます。(既定では、最初のノードは、10.0.0.30 のプライベート IP アドレスに割り当てられ、2 番目のノードは 10.0.0.31 に割り当てられます。3 番目以降も、同様に割り当てられます。) デプロイ エラーを確認するには、新しい Azure ポータルを開き、**[リソース グループ]** > **[最後の展開]** > **[操作の詳細の確認]** を選択します。

Azure リソース マネージャーとこのデプロイに使用するテンプレートに関連する詳細に進む前に、Azure PowerShell または Azure CLI の構成が正常に完了していることを確認してください。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## リソース マネージャー テンプレートを使用して Spark クラスターを作成する

次の手順に従い、リソース マネージャー テンプレートで、Azure PowerShell または Azure CLI を使用して、GitHub テンプレート リポジトリから Spark クラスターを作成します。

### 手順 1-a: Azure PowerShell を使用してテンプレート ファイルをダウンロードする

JSON テンプレートとその他の関連ファイル用のローカル フォルダーを作成します (例: C:\\Azure\\Templates\\Spark)。

フォルダー名をローカル フォルダーのフォルダー名に置き換えて、次のコマンドを実行します。

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### 手順 1-b: Azure CLI を使用してテンプレート ファイルをダウンロードする

たとえば次のように任意の Git クライアントを使用して、テンプレート リポジトリ全体を複製します。

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

複製の完了後に、C:\\Azure\\Templates の **spark-on-ubuntu** フォルダーを探します。

### 手順 2. (省略可能) テンプレート パラメーターを理解する

テンプレートを使用して Spark クラスターを作成したら、構成パラメーター セットを指定して、必要な複数の設定を処理します。テンプレートの定義でこれらのパラメーターを宣言することで、外部ファイルまたはコマンドラインによってデプロイの実行中に値を指定することができます。

azuredeploy.json ファイルの先頭にある "parameters" セクションで、Spark クラスターを構成するためにテンプレートに必要なパラメーター セットが見つかります。このテンプレートの azuredeploy.json ファイルの parameters セクションの例を次に示します。

```json
"parameters": {
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
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

各パラメーターには、データ型や許容値などの詳細が記述されています。これにより、対話モード (Azure PowerShell や Azure CLI など) でのテンプレート実行時に渡されるパラメーターだけでなく、必要なパラメーターとその記述のリストを解析することによって動的に構築できる自己発見型の UI を検証することが可能になります。

### 手順 3-a: Azure PowerShell とテンプレートを使用して Spark クラスターをデプロイする

すべてのパラメーターの実行時の値を含む JSON ファイルを作成することによって、デプロイ用の parameters ファイルを準備します。このファイルは、単一のエンティティとしてデプロイメント コマンドに渡されます。parameters ファイルが指定されていない場合、Azure PowerShell はテンプレートで指定されているすべての既定値を使用し、残りの値を入力するように要求します。

次に、azuredeploy-parameters.json ファイルのパラメーター セットの例を示します。パラメーター **storageAccountName**、**adminUsername**、**adminPassword**、およびその他のパラメーターの任意のカスタマイズに有効な値を指定する必要があります。

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
  },
  "subnetName": {
    "value": "Subnet1"
  },
  "addressPrefix": {
    "value": "10.0.0.0/16"
  },
  "subnetPrefix": {
    "value": "10.0.0.0/24"
  },
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Azure のデプロイ名、リソース グループ名、Azure の場所、JSON デプロイ ファイルの保存先フォルダーを指定します。次のコマンドを実行します。

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]**$RGName** は、サブスクリプション内で一意である必要があります。

**New-AzureResourceGroupDeployment** コマンドを実行すると、JSON parameters ファイルからパラメーター値が抽出され、それに基づきテンプレートが実行されます。さまざまな環境 (テスト環境、運用環境など) によって複数のパラメーター ファイルを定義し、使用することは、テンプレートの再利用を推進し、複雑な多環境ソリューションを単純化します。

デプロイ時には新しい Azure ストレージ アカウントを作成する必要があるため、ストレージ アカウント パラメーターとして指定する名前は一意であり、かつ Azure ストレージ アカウントのすべての要件 (小文字と数字のみ) を満たしている必要がある点に留意してください。

デプロイメントの間に、次のような情報が表示されます。

```powershell
PS C:\> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

デプロイ中およびデプロイ後には、プロビジョニング中に行われたすべての要求 (発生したすべてのエラーを含む) を確認できます。

確認するには、[Azure ポータル](https://portal.azure.com)に移動し、次の操作を行います。

- 左側のナビゲーション バーで **[参照]** をクリックし、下へスクロールして **[リソース グループ]** をクリックします。
- 作成したリソース グループをクリックして、[リソース グループ] ブレードを表示します。
- [リソース グループ] ブレードの **[監視]** 部分にある **[イベント]** 棒グラフをクリックすると、デプロイのイベントが表示できます。
- 個々のイベントをクリックすることで、テンプレートのために実行された個別の操作の詳細にドリル ダウンできます。

![portal-events](media/virtual-machines-spark-template/portal-events.png)

テスト後に、このリソース グループとそのすべてのリソース (ストレージ アカウント、仮想マシン、仮想ネットワーク) を削除する必要がある場合は、次のコマンドを使用します。

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### 手順 3-b: Azure CLI とテンプレートを使用して Spark クラスターをデプロイする

Azure CLI を使用して Spark クラスターをデプロイするには、まず、名前と場所を指定してリソース グループを作成します。

	azure group create SparkResourceGroup "West US"

このリソース グループ名、JSON テンプレート ファイルの場所、および parameters ファイルの場所 (詳細については、前述の Azure PowerShell に関する説明を参照) を、次のコマンドに渡します。

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

次のコマンドを使用することにより、個々のリソース デプロイのステータスをチェックできます。

	azure group deployment list SparkResourceGroup

## Spark のテンプレート構造とファイル編成について

堅牢で再利用可能なリソース マネージャー テンプレートを設計するには、さらに検討を加え、Spark のような複雑なソリューションのデプロイ時に必要な、複雑で相互に関連する一連のタスクを整理する必要があります。関連する拡張機能によるスクリプト実行に加え、リソース マネージャーのテンプレート リンク機能やリソース ループ機能を活用することで、実質的にはどのような複雑なテンプレート ベースのデプロイにも再利用可能なモジュール式の手法を実行できます。

次の図は、このデプロイの GitHub からダウンロードされるすべてのファイル間の関係を表しています。

![spark-files](media/virtual-machines-spark-template/spark-files.png)

このセクションでは、Spark クラスターの azuredeploy.json ファイルの構造について、順を追って説明します。

テンプレート ファイルのコピーをまだダウンロードしていない場合は、ファイルの場所としてローカル フォルダーを指定し、ファイルを作成します (例: C:\\Azure\\Templates\\Spark)。フォルダー名を指定して以下のコマンドを実行します。

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

テキスト エディターまたは適当なツールで azuredeploy.json テンプレートを開きます。テンプレート ファイルの構造および各セクションの目的を次に示します。または、[こちら](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json)からブラウザーでこのテンプレートの内容を見ることもできます。。

### "parameters" セクション

azuredeploy.json の "parameters" セクションには、このテンプレートで使用される、変更可能なパラメーターを指定します。前述の azuredeploy-parameters.json ファイルは、テンプレート実行時に、値を azuredeploy.json の "parameters" セクションに渡すために使用されます。

「T シャツ サイズ」のパラメーターの例を次に示します。

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]**allowedValues** だけでなく、**defaultValue** も指定できます。

### "variables" セクション

"variables" セクションには、このテンプレート全体で使用できる変数を指定します。これには、実行時に定数または計算済みの値に設定される、いくつかのフィールド (JSON データ型またはフラグメント) が格納されます。単純なものから複雑なものまで、いくつかの例を次に示します。

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

**vmStorageAccountContainerName** は、サンプル名/値変数の例です。**vnetID** は、関数 **resourceId** と **parameters** を使用して実行時に計算される変数の例です。**numberOfMasterInstances** と **vmSize** 変数の値は、**concat**、**variables**、および **parameters** 関数を使用して実行時に計算されます。

Spark クラスターのデプロイのサイズをカスタマイズするには、azuredeploy.json テンプレート内の変数 **tshirtSizeS**、**tshirtSizeM**、および **tshirtSizeL** のプロパティを変更します。

テンプレート言語の詳細については、MSDN の「[Azure リソース マネージャーのテンプレート言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。


### "resources" セクション

"resources" セクションではアクションの大部分が発生します。このセクションを注意深く調べると、2 つの異なるケースがあることがすぐにわかります。1 つ目は、基本的にはメインのデプロイに入れ子になったデプロイメントを呼び出す `Microsoft.Resources/deployments` 型で定義された要素です。2 つ目は、一連のパラメーターを入力として呼び出されるリンクされたテンプレート ファイルを指定できるようにする **templateLink** プロパティ (および関連する **contentVersion** プロパティ) です。次のテンプレート フラグメントはこれらを示したものです。

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

この 1 つ目の例から明らかなように、このシナリオの azuredeploy.json は、ある種のオーケストレーション メカニズムとして編成され、他の数多くのテンプレート ファイルを呼び出しています。各ファイルは、必要なデプロイ アクティビティの一部を担っています。

特に、このデプロイメントには次のリンク済みテンプレートが使用されます。

-	**shared-resource.json**: デプロイ全体で共有されるすべてのリソースの定義を格納します。たとえば、VM の OS ディスクおよび仮想ネットワークの格納に使用される Storage アカウントです。
-	**jumpbox-resources-enabled.json**: “jump box” の VM、および環境への SSH に使用されるネットワーク インターフェイス、パブリック IP アドレス、入力エンドポイントなどのすべての関連リソースです。

これら 2 つのテンプレートを呼び出すと、azuredeploy.json によって、すべての Spark クラスター ノード VM と接続されているリソース (ネットワーク アダプター、プライベート IP など) がプロビジョニングされます。また、このテンプレートで VM 拡張機能 (Linux 用のカスタム スクリプト) がデプロイされ、bash スクリプト (spark-cluster-install.sh) が呼び出されて、各ノードに Spark が物理的にインストールされ、セットアップされます。

この最後のテンプレート azuredeploy.json は、テンプレート開発の観点から見て最も興味深いテンプレートの 1 つです。このテンプレートの*使用方法*について、詳しく見てみましょう。注意が必要な重要な概念は、どのようにすれば 1 つのテンプレート ファイルによって単一の種類のリソースの複数のコピーをデプロイできるか、また、各インスタンスが必要な設定に対して一意の値を設定できるかということです。この概念は、**リソース ループ**と呼ばれています。

**copy** 要素を使用するリソースは、**copy** 要素の **count** パラメーターで指定されている回数だけ自分自身を「コピー」します。デプロイ済みのリソースの異なるインスタンス間で一意の値を指定する必要があるすべての設定に対して、特定のリソース ループ作成における現在のインデックスを示す数値の取得に **copyindex()** 関数を使用できます。azuredeploy.json の次のフラグメントでは、この概念が複数のネットワーク アダプター、VM、および Spark クラスター用に作成された VM 拡張機能に適用されていることを確認できます。

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

リソース作成におけるもう 1 つの重要な概念は、**dependsOn** JSON 配列からわかるように、リソース間の依存関係と優先順位を指定できる点です。このテンプレートでは、Spark クラスター ノードが、共有リソースと最初に作成される **networkInterfaces** リソースに依存していることがわかります。

注意が必要なもう 1 つの興味深いフラグメントとして、**CustomScriptForLinux** VM 拡張機能に関連するものがあります。これらは、各クラスター ノードに依存関係を持つ別の種類のリソースとしてインストールされます。この場合、これは各 VM ノードに Spark をインストールしてセットアップするために使われます。これらを使用する azuredeploy.json テンプレートのスニペットを見てみましょう。

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

マスター ノードとスレーブ ノードのリソースの拡張機能は、プロビジョニング プロセスの一部として、**commandToExecute** プロパティに定義されている別のコマンドを実行します。

最新の仮想マシンの拡張機能の JSON スニペットを見ると、このリソースが、仮想マシン リソースとそのネットワーク インターフェイスに依存していることが分かります。これは、この VM の拡張機能をプロビジョニングし、実行する前に、これらの 2 つのリソースを展開しておく必要があることを示しています。**copyindex()**関数を使用して、各スレーブ仮想マシンに対してこの手順を繰り返すことにも注意してください。

このデプロイに含まれる他のファイルを熟知することで、Azure リソース マネージャー テンプレートを活用しながら、任意のテクノロジに基づく複数ノード ソリューションの複雑なデプロイ戦略を編成および調整するために必要な、すべての詳細とベスト プラクティスを理解することができます。必須ではありませんが、次の図で表されているようにテンプレート ファイルを構築する手法をお勧めします。

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

この手法では、基本的に次のことを提案しています。

-	すべてのデプロイ アクティビティに対する中心的なオーケストレーション ポイントとして、核となるテンプレート ファイルを定義し、サブテンプレートの実行を呼び出すためにテンプレート リンクを活用します。
-	他のすべてのデプロイ タスク (ストレージ アカウント、仮想ネットワーク構成など) の間で共有されるすべてのリソースをデプロイする、特定のテンプレート ファイルを作成します。これは、共通のインフラストラクチャに関して類似した要件を持つデプロイ間で頻繁に再利用できます。
-	特定のリソースに固有のスポット要件用にオプションのリソース テンプレートを含みます。
-	リソース グループの同一メンバー (クラスター内のノードなど) に対して、一意のプロパティを持つ複数のインスタンスをデプロイするため、リソース ループを活用する特定のテンプレートを作成します。
-	すべてのデプロイメント後のタスク (製品のインストールや構成など) について、スクリプト デプロイメントの拡張機能を活用し、各テクノロジに固有のスクリプトを作成します。

詳細については、「[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。

## 次のステップ

詳細については、[テンプレートのデプロイ](../resource-group-template-deploy.md)を参照してください。

[その他のアプリケーション フレームワークについてはこちら](virtual-machines-app-frameworks.md)を参照してください。

[Troubleshoot template deployments (テンプレート デプロイのトラブルシューティング)](resource-group-deploy-debug.md)

<!---HONumber=August15_HO6-->
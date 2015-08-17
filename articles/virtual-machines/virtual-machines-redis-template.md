<properties
	pageTitle="Redis クラスターのリソース マネージャー テンプレート"
	description="Azure PowerShell または Azure CLI とリソース マネージャー テンプレートを使用し、Ubuntu VM 上に新しい Redis クラスターを簡単にデプロイする方法について説明します"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# リソース マネージャー テンプレートを使用した Redis クラスター

Redis は、オープンソースのキー/値キャッシュおよびストアです。キー/値ストアのキーには、文字列、ハッシュ、リスト、セット、ソート済みセットなどのデータ構造を格納できます。Redis は、このようなデータ型に対する一連のアトミック操作をサポートしています。Redis バージョン 3.0 のリリースでは、最新の安定バージョンの Redis で Redis クラスターを使用できるようになりました。Redis クラスターは Redis の分散実装であり、データは複数の Redis ノード間で自動的に共有されて、ノードのサブセットにエラーが発生しても動作を続けることができます。

Microsoft Azure Redis Cache は専用の Redis Cache Service で、マイクロソフトによって管理されますが、すべての Microsoft Azure ユーザーが Azure Redis Cache を使用する必要があるわけではありません。ユーザーの好みにより、Redis Cache を独自の Azure デプロイ内のサブネットの背後に置くことも、すべての Redis 機能を完全に利用できるように Linux 仮想マシンで独自の Redis サーバーをホストすることもできます。

このチュートリアルでは、Azure リソース マネージャーのサンプル テンプレートを使用して Microsoft Azure のリソース グループ内のサブネットの内部にある Ubuntu VM 上に Redis クラスターをデプロイする手順を説明します。このテンプレートは、Redis 3.0 クラスターに加えて、Redis Sentinel での Redis 2.8 のデプロイもサポートします。このチュートリアルは Redis 3.0 クラスターの実装を対象にしていることに注意してください。

Redis クラスターはサブネットの背後に作成されるので、パブリック IP で Redis クラスターにアクセスすることはできません。デプロイメントの一部として、オプションの “jump box” をデプロイできます。この “jump box” はサブネットにもデプロイされる Ubuntu VM ですが、SSH を使用して接続できる開かれた SSH ポートでパブリック IP アドレスを公開*します*。“jump box” からは、サブネット内にあるすべての Redis VM に SSH で接続できます。

このテンプレートでは、「小」、「中」、「大」の Redis クラスター セットアップを指定するために、「T シャツのサイズ」の概念を使用します。Azure リソース マネージャー テンプレートの言語がさらに動的なテンプレートのサイズ指定をサポートするようになったら、この概念は Redis クラスターのマスター ノード数、スレーブ ノード数、VM サイズなどの指定に変更される可能性があります。今のところは、azuredeploy.json で定義されている VM のサイズおよびマスターとスレーブの数は、変数 `tshirtSizeSmall`、`tshirtSizeMedium`、および `tshirtSizeLarge` で見ることができます。

T シャツ サイズ「中」の Redis クラスター テンプレートは、次のような構成を作成します。

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Azure リソース マネージャーとこのデプロイメントに使用するテンプレートに関連する詳細に進む前に、Azure PowerShell または Azure CLI の構成が正常に完了していることを確認してください。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## リソース マネージャー テンプレートを使用した Redis クラスターのデプロイ

GitHub テンプレート リポジトリのリソース マネージャー テンプレートを使用して Redis クラスターを作成するには、以下の手順に従います。各手順には、Azure PowerShell と Azure CLI の両方の手順が含まれています。

### 手順 1-a: Azure PowerShell を使用してテンプレート ファイルをダウンロードする

JSON テンプレートとその他の関連ファイル用のローカル フォルダーを作成します (例: C:\\Azure\\Templates\\RedisCluster)。

フォルダー名をローカル フォルダーのフォルダー名に置き換えて、次のコマンドを実行します。

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### 手順 1-b: Azure CLI を使用してテンプレート ファイルをダウンロードする

たとえば次のように任意の Git クライアントを使用して、テンプレート リポジトリ全体を複製します。

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

複製の完了後に、C:\\Azure\\Templates ディレクトリで **redis-high-availability** フォルダーを探します。

### 手順 2. (省略可能) テンプレート パラメーターを理解する

テンプレートを使用して Redis クラスターを作成するときは、一連の構成パラメーターを指定する必要があります。Redis クラスターを作成するためのコマンドを実行する前に、ローカル JSON ファイルのテンプレートで指定する必要のあるパラメーターを確認するには、適切なツールまたはテキスト エディターで目的の JSON ファイルを開きます。

ファイルの先頭にある "parameters" セクションを探してください。テンプレートで Redis クラスターを構成するために必要な一連のパラメーターが指定されています。azuredeploy.json テンプレートの "parameters" セクションを次に示します。

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
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
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
		"defaultValue": "redisSubnet1",
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
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

各パラメーターには、データ型や許容値などの詳細が記述されています。これにより、対話モード (Azure PowerShell や Azure CLI など) でのテンプレート実行時に渡されるパラメーターだけでなく、必要なパラメーターとその記述のリストを解析することによって動的に構築できる自己発見型の UI を検証することが可能になります。

### 手順 3-a: Azure PowerShell とテンプレートを使用して Redis クラスターをデプロイする

すべてのパラメーターの実行時の値を含む JSON ファイルを作成することによって、デプロイメント用の parameters ファイルを準備します。このファイルは、単一のエンティティとしてデプロイメント コマンドに渡されます。parameters ファイルが指定されていない場合、Azure PowerShell はテンプレートで指定されているすべての既定値を使用し、残りの値を入力するように要求します。

azuredeploy-parameters.json ファイル内に見られる例を次に示します。パラメーター `storageAccountName`、`adminUsername`、`adminPassword` および他のパラメーターのすべてのカスタマイズに対して、有効な値を指定する必要があります。

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
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
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]パラメーター `storageAccountName` には、Microsoft Azure Storage アカウントの名前付け要件 (小文字と数字のみ) を満たす存在しない一意のストレージ アカウント名を指定する必要があります。このストレージ アカウントは、デプロイメント プロセスの一部として作成されます。

Azure のデプロイメント名、リソース グループ名、Azure の場所、JSON ファイルの保存先フォルダーを指定します。次のコマンドを実行します。

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` は、サブスクリプション内で一意である必要があります。

**New-AzureResourceGroupDeployment** コマンドを実行すると、parameters JSON ファイル (azuredeploy-parameters.json) からパラメーター値が抽出され、それに基づきテンプレートが実行されます。さまざまな環境 (テスト環境、運用環境など) によって複数のパラメーター ファイルを定義し、使用することは、テンプレートの再利用を推進し、複雑な多環境ソリューションを単純化します。

デプロイ時には新しい Azure ストレージ アカウントを作成する必要があるため、ストレージ アカウント パラメーターとして指定する名前は一意であり、かつ Azure ストレージ アカウントのすべての要件 (小文字と数字のみ) を満たしている必要がある点に留意してください。

デプロイメントの間に、次のような情報が表示されます。

	PS C:\> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

デプロイメント中およびデプロイメント後には、プロビジョニング中に行われたすべての要求 (発生したすべてのエラーを含む) を確認できます。

確認するには、[Azure ポータル](https://portal.azure.com)に移動し、次の操作を行います。

- 左側のナビゲーション バーで **[参照]** をクリックし、下へスクロールして **[リソース グループ]** をクリックします。
- 作成したリソース グループを選択して、[リソース グループ] ブレードを表示します。
- **[監視]** セクションで、[イベント] 棒グラフを選択します。デプロイメントのイベントが表示されます。
- 個々のイベントをクリックすることで、テンプレートのために実行された各操作の詳細にドリル ダウンできます。

テスト後に、このリソース グループとそのすべてのリソース (ストレージ アカウント、仮想マシン、仮想ネットワーク) を削除する必要がある場合は、次のコマンドを使用します。

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### 手順 3-b: Azure CLI とテンプレートを使用して Redis クラスターをデプロイする

Azure CLI を使用して Redis クラスターをデプロイするには、まず、名前と場所を指定してリソース グループを作成します。

```powershell
azure group create TestRG "West US"
```

このリソース グループ名、JSON テンプレート ファイルの場所、および parameters ファイルの場所 (詳細については、前述の Azure PowerShell に関する説明を参照) を、次のコマンドに渡します。

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

次のコマンドを使用することにより、個々のリソース デプロイメントのステータスをチェックできます。

```powershell
azure group deployment list TestRG
```

## Redis クラスターのテンプレート構造とファイル編成について

リソース マネージャー テンプレートの堅牢で再利用可能な設計手法を生み出すためには、さらに検討を加え、Redis クラスターのような複雑なソリューションのデプロイメント時に必要な、複雑で相互に関連する一連のタスクを整理する必要があります。リソース マネージャーのテンプレート リンク機能、リソース ループ、関連する拡張機能によるスクリプト実行を活用することで、実質的にはどのような複雑なテンプレート ベースのデプロイメントにも再利用可能なモジュール式の手法を実行できます。

次の図は、このデプロイメントの GitHub からダウンロードされるすべてのファイル間の関係を表しています。

![redis-files](media/virtual-machines-redis-template/redis-files.png)

このセクションでは、Redis クラスターの azuredeploy.json テンプレートの構造について、順を追って説明します。

テンプレート ファイルのコピーをダウンロードしていない場合は、ファイルの場所としてローカル フォルダーを指定し、ファイルを作成します (例: C:\\Azure\\Templates\\RedisCluster)。フォルダー名を指定して以下のコマンドを実行します。

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

テキスト エディターまたは適当なツールで azuredeploy.json テンプレートを開きます。テンプレート ファイルの構造および各セクションの目的を次に示します。または、[こちら](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json)からブラウザーでこのテンプレートの内容を見ることもできます。。

### "parameters" セクション

既に役割を説明した azuredeploy-parameters.json は、テンプレートの実行中に指定されたパラメーター値のセットを渡すために使用されます。azuredeploy.json の "parameters" セクションでは、このテンプレートにデータを入力するために使用されるパラメーターを指定します。

「T シャツ サイズ」のパラメーターの例を次に示します。

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]`defaultValue` および `allowedValues` を指定できることに注意してください。

### "variables" セクション

"variables" セクションには、このテンプレート全体で使用できる変数を指定します。基本的に、このセクションには、実行時に定数または計算済みの値に設定される、いくつかのフィールド (JSON データ型またはフラグメント) が格納されます。単純なものから複雑なものまで、いくつかの例を次に示します。

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

変数 `vmStorageAccountContainerName` および `vmStorageAccountDomain` は、単純な名前/値変数の例です。`vnetID` は、関数 `resourceId` および `parameters` を使用して実行時に計算される変数の例です。`machineSettings` はこれらの概念を基にして、さらに JSON オブジェクト `osImageReference` が `machineSettings` 変数に入れ子になっています。`vmScripts` に含まれる JSON 配列 `scriptsToDownload` は、`concat` および `variables` 関数を使用して実行時に計算されます。

Redis クラスターのデプロイメントのサイズをカスタマイズする場合は、azuredeploy.json テンプレート内の変数 `tshirtSizeSmall`、`tshirtSizeMedium`、`tshirtSizeLarge` のプロパティを変更できます。

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

注: 現在テンプレート言語には「算術」演算がないため、`totalMemberCountExcludingLast` および `totalMemberCount` プロパティが必要です。

テンプレート言語の詳細については、MSDN の「[Azure リソース マネージャーのテンプレート言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。

### "resources" セクション

"resources" セクションではアクションの大部分が発生します。このセクションを注意深く調べると、2 つの異なるケースがあることがすぐにわかります。1 つ目は、基本的にはメインのデプロイメントに入れ子になったデプロイメントを呼び出す `Microsoft.Resources/deployments` 型で定義された要素です。2 つ目は、一連のパラメーターを入力として呼び出されるリンクされたテンプレート ファイルを指定できるようにする `templateLink` プロパティ (および関連する `contentVersion` プロパティ) です。次のテンプレート フラグメントはこれらを示したものです。

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

この 1 つ目の例から明らかなように、このシナリオの azuredeploy.json は、ある種のオーケストレーション メカニズムとして編成され、他の数多くのテンプレート ファイルを呼び出しています。各ファイルは、必要なデプロイ アクティビティの一部を担っています。

特に、このデプロイメントには次のリンク済みテンプレートが使用されます。

- **shared-resource.json**: デプロイメント全体で共有されるすべてのリソースの定義を格納します。たとえば、VM の OS ディスクまたは仮想ネットワークの格納に使用されるストレージ アカウントや、可用性セットです。
- **jumpbox-resources.json**: “jump box” の VM、および環境への SSH に使用されるネットワーク インターフェイス、パブリック IP アドレス、入力エンドポイントなどのすべての関連リソースです。
- **node-resources.json**: すべての Redis クラスター ノードの VM と、接続されているリソース (ネットワーク アダプター、プライベート IP など) をデプロイします。このテンプレートは、VM 拡張機能 (Linux 用のカスタム スクリプト) もデプロイし、bash スクリプトを呼び出して、各ノードに Redis を物理的にインストールしてセットアップします。呼び出すスクリプトは、`commandToExecute` プロパティの `machineSettings` パラメーターでこのテンプレートに渡されます。1 つを除くすべての Redis クラスター ノードを、並列にデプロイしてスクリプト化できます。Redis クラスターのセットアップは 1 つのノードでのみ実行できるので、1 つのノードは最後まで残しておき、他のすべてのノードが Redis サーバーを実行するようになった後でデプロイする必要があります。実行するスクリプトをこのテンプレートに渡すのはこのためです。最後のノードでは、Redis サーバーのインストールだけではなく、Redis クラスターのセットアップも行うため、少し異なるスクリプトを実行する必要があります。

この最後のテンプレート node-resources.json は、テンプレート開発の観点から見て最も興味深いテンプレートの 1 つです。このテンプレートの*使用方法*について、詳しく見てみましょう。注意が必要な重要な概念は、どのようにすれば 1 つのテンプレート ファイルによって単一の種類のリソースの複数のコピーをデプロイできるか、また、各インスタンスが必要な設定に対して一意の値を設定できるかということです。この概念は、**リソース ループ**と呼ばれています。

node-resources.json は、メインの azuredeploy.json ファイルから呼び出されるとき、`copy` 要素を使用してソートのループを作成するリソースの内部から呼び出されます。`copy` 要素を使用するリソースは、`copy` 要素の `count` パラメーターで指定されている回数だけ自分自身を「コピー」します。デプロイ済みのリソースの異なるインスタンス間で一意の値を指定する必要があるすべての設定に対して、特定のリソース ループ作成における現在のインデックスを示す数値の取得に **copyindex()** 関数を使用できます。次に示す azuredeploy.json のフラグメントでは、Redis クラスター ノードに対して作成される複数 VM に、この概念が適用されていることがわかります。

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

リソース作成におけるもう 1 つの重要な概念は、`dependsOn` JSON 配列からわかるように、リソース間の依存関係と優先順位を指定できる点です。この特定のテンプレートでは、Redis クラスターのノードが最初に作成される共有リソースに依存していることがわかります。

前に説明したように、最後のノードは、Redis クラスター内の他のすべてのノードが Redis サーバーを実行するようにプロビジョニングされるまで待ってから、プロビジョニングする必要があります。この処理は、上で示したテンプレート スニペットの `memberNodesLoop` という名前の `copy` ループに依存する `lastnode-resources` という名前のリソースを用意することによって、azuredeploy.json で実現されています。`memberNodesLoop` が完了した後、`lastnode-resources` をプロビジョニングできます。

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

`lastnode-resources` リソースがわずかに異なる `machineSettings.commandToExecute` をリンクされたテンプレートに渡している方法に注意してください。これは、最後のノードの場合、Redis サーバーをインストールするだけでなく、Redis クラスターをセットアップするスクリプトを呼び出す必要があるためです (クラスターのセットアップは、すべての Redis サーバーが稼働した後で 1 回だけ実行する必要があります)。

注意が必要なもう 1 つの興味深いフラグメントは、`CustomScriptForLinux` VM 拡張機能に関連するものです。これらは、各クラスター ノードに依存関係を持つ別の種類のリソースとしてインストールされます。この場合、これは各 VM ノードに Redis をインストールしてセットアップするために使われます。これらを使用する node-resources.json テンプレートのスニペットを見てみましょう。

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

このリソースは既にデプロイされているリソース VM に依存することがわかります (`Microsoft.Compute/virtualMachines/vmMember<X>`。ここで、`<X>` はパラメーター `machineSettings.machineIndex` であり、**copyindex()** 関数を使用してこのスクリプトに渡された VM のインデックスです)。

このデプロイメントに含まれる他のファイルを熟知することで、Azure リソース マネージャー テンプレートを活用しながら、任意のテクノロジに基づく複数ノード ソリューションの複雑なデプロイメント戦略を編成および調整するために必要な、すべての詳細とベスト プラクティスを理解することができます。必須ではありませんが、次の図で表されているようにテンプレート ファイルを構築する手法をお勧めします。

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

この手法では、基本的に次のことを提案しています。

- すべてのデプロイメント アクティビティに対する中心的なオーケストレーション ポイントとして、核となるテンプレート ファイルを定義し、サブテンプレートの実行を呼び出すためにテンプレート リンクを活用します。
- 他のすべてのデプロイメント タスク (ストレージ アカウント、仮想ネットワーク構成など) の間で共有されるすべてのリソースをデプロイする、特定のテンプレート ファイルを作成します。これは、共通のインフラストラクチャに関して類似した要件を持つデプロイメント間で頻繁に再利用できます。
- 特定のリソースに固有のスポット要件用にオプションのリソース テンプレートを含みます。
- リソース グループの同一メンバー (クラスター内のノードなど) に対して、一意のプロパティを持つ複数のインスタンスをデプロイするため、リソース ループを活用する特定のテンプレートを作成します。
- すべてのデプロイメント後のタスク (製品のインストールや構成など) について、スクリプト デプロイメントの拡張機能を活用し、各テクノロジに固有のスクリプトを作成します。

詳細については、「[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。

<!---HONumber=August15_HO6-->
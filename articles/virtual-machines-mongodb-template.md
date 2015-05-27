<properties
  pageTitle="リソース マネージャー テンプレートを使用して Ubuntu 上に MongoDB クラスターを作成する"
  description="リソース マネージャー テンプレートを使用して PowerShell または Azure CLI により Ubuntu 上に MongoDB クラスターを作成します"
  services="virtual-machines"
  documentationCenter=""
  authors="karthmut"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="karthmut"/>

# リソース マネージャー テンプレートを使用して Ubuntu 上に MongoDB クラスターを作成する

MongoDB は、高いパフォーマンス、高い可用性、自動的なスケーリングを提供するオープン ソースのドキュメント データベースです。MongoDB は、組み込まれているレプリケーション機能を利用して、スタンドアロンとして、またはクラスター内にインストールできます。場合によっては、レプリケーションを使用して読み取り能力を増やすことができます。クライアントには、読み取り操作と書き込み操作を異なるサーバーに送る機能があります。また、異なるデータ センターにコピーを保持して、分散アプリケーションのデータの局所性と可用性を向上させることもできます。MongoDB でのレプリケーションは、冗長性も提供し、データの可用性を向上させます。レプリケーションによって異なるデータベース サーバー上にデータの複数のコピーを置くことにより、1 台のサーバーが失われてもデータベースを保護できます。レプリケーションにより、ハードウェアの障害やサービスの中断から回復することもできます。データのコピーを増やして、障害回復専用、レポート専用、バックアップ専用などにできます。

Azure Marketplace で既に提供されていたさまざまな種類に加え、[Azure PowerShell](powershell-install-configure.md) または [Azure CLI](xplat-cli.md) を通してデプロイされたリソース マネージャー テンプレートを使用して、Ubuntu VM 上に新しい MongoDB クラスターを簡単にデプロイすることも可能になりました。

このテンプレートに基づいて新しくデプロイされるクラスターには、次の図に示すトポロジが実装されます。これ以外のトポロジも、この記事で示したテンプレートをカスタマイズすることで容易に実現できます。

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

パラメーターによって新しい MongoDB クラスターにデプロイされるノードの数を定義でき、また別のパラメーターに基づいてパブリック IP アドレスを持つ VM インスタンス (Jumpbox) を同じ VNET 内にデプロイすることもできるので、パブリック インターネットからクラスターに接続して、そのクラスターに関連するあらゆる種類の管理タスクを実行できます。パラメーターとして使用できるもう 1 つのオプションは、レプリカ セットにアービター ノードを追加する機能です。これは通常、レプリカ セットに偶数個のメンバーがある場合に推奨されます。MongoDB のレプリケーション トポロジと詳細については、公式な [MongoDB ドキュメント](http://docs.mongodb.org/manual/core/replication-introduction/)を参照してください。

デプロイメントが完了すると、SSH ポート 22 に構成された DNS アドレスを使用して Jumpbox にアクセスできます。

Azure リソース マネージャーとこのデプロイメントに使用するテンプレートに関連する詳細に進む前に、Azure PowerShell または Azure CLI の構成が正常に完了していることを確認してください。

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## リソース マネージャー テンプレートを使用して MongoDB クラスターを作成する

Github テンプレート リポジトリのリソース マネージャー テンプレートを使用して MongoDB クラスターを作成するには、以下の手順に従います。各手順には、Azure PowerShell と Azure CLI の両方の手順が含まれています。

### 手順 1-a: PowerShell を使用してテンプレート ファイルをダウンロードする

JSON テンプレートとその他の関連ファイル用のローカル フォルダーを作成します (例: C:\\Azure\\Templates\\MongoDB)。

フォルダー名をローカル フォルダーのフォルダー名に置き換えて、次のコマンドを実行します。

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### 手順 1-b: Azure CLI を使用してテンプレート ファイルをダウンロードする

たとえば次のように任意の Git クライアントを使用して、テンプレート リポジトリ全体を複製します。

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

完了後に、C:\\Azure\\Templates ディレクトリで **mongodb-high-availability** フォルダーを探します。

### 手順 2: (省略可能) テンプレート パラメーターを理解する

MongoDB クラスターのような自明ではないソリューションをデプロイする場合、必要な複数の設定を処理するために構成パラメーターのセットを指定する必要があります。テンプレートの定義でこれらのパラメーターを宣言することで、デプロイメント時に外部ファイルまたはコマンドラインを通して値を指定することができます。

**azuredeploy.json** ファイルの先頭にある "parameters" セクションで、MongoDB クラスターを構成するためにテンプレートに必要なパラメーター セットが見つかります。このテンプレートの azuredeploy.json ファイルの parameters セクションの例を次に示します。

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
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
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

各パラメーターには、データ型や許容値などの詳細が記述されています。これにより、対話モード (PowerShell や Azure CLI など) でのテンプレート実行時に渡されるパラメーターだけでなく、必要なパラメーターとその記述のリストを解析することによって動的に構築できる自己発見型の UI を検証することが可能になります。

### 手順 3-a: PowerShell とテンプレートを使用して MongoDB クラスターをデプロイする

すべてのパラメーターの実行時の値を含む JSON ファイルを作成することによって、デプロイメント用の parameters ファイルを準備します。このファイルは、単一のエンティティとしてデプロイメント コマンドに渡されます。parameters ファイルが指定されていない場合、PowerShell はテンプレートで指定されているすべての既定値を使用し、残りの値を入力するように要求します。

次に、**azuredeploy-parameters.json** ファイルのパラメーター セットの例を示します。

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
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
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Azure のデプロイメント名、リソース グループ名、Azure の場所、JSON デプロイ ファイルの保存先フォルダーを指定します。次のコマンドを実行します。

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

**New-AzureResourceGroupDeployment** コマンドを実行すると、JSON parameters ファイルからパラメーター値が抽出され、それに基づきテンプレートが実行されます。さまざまな環境 (テスト環境、運用環境など) によって複数のパラメーター ファイルを定義し、使用することは、テンプレートの再利用を推進し、複雑な多環境ソリューションを単純化します。

デプロイ時には新しい Azure ストレージ アカウントを作成する必要があるため、ストレージ アカウント パラメーターとして指定する名前は一意であり、かつ Azure ストレージ アカウントのすべての要件 (小文字と数字のみ) を満たしている必要がある点に留意してください。

デプロイメント中およびデプロイメント後には、プロビジョニング中に行われたすべての要求 (発生したすべてのエラーを含む) を確認できます。

確認するには、[Azure ポータル](https://portal.azure.com)に移動し、次の操作を行います。

- 左側のナビゲーション バーで [参照] をクリックし、下へスクロールして [リソース グループ] をクリックします。
- 作成したリソース グループをクリックすると、[リソース グループ] ブレードが表示されます。
- [リソース グループ] ブレードの [監視] 部分にある [イベント] 棒グラフをクリックすると、デプロイメントのイベントが表示できます。
- 個々のイベントをクリックすることで、テンプレートのために実行された個別の操作の詳細にドリル ダウンできます。

テスト後に、このリソース グループとそのすべてのリソース (ストレージ アカウント、仮想マシン、仮想ネットワーク) を削除する必要がある場合は、次のコマンドを使用します。

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### 手順 3-b: Azure CLI とテンプレートを使用して MongoDB クラスターをデプロイする

Azure CLI を使用して MongoDB クラスターをデプロイするには、まず、名前と場所を指定してリソース グループを作成します。

    azure group create mdbc "West US"

このリソース グループ名、JSON テンプレート ファイルの場所、および parameters ファイルの場所 (詳細については、前述の PowerShell に関する説明を参照) を、次のコマンドに渡します。

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

次のコマンドを実行することにより、個々のリソース デプロイメントのステータスをチェックできます。

    azure group deployment list mdbc

## MongoDB のテンプレート構造とファイル編成について

堅牢で再利用可能なリソース マネージャー テンプレートを設計するには、さらに検討を加え、MongoDB のような複雑なソリューションのデプロイメント時に必要な、複雑で相互に関連する一連のタスクを整理する必要があります。関連する拡張機能によるスクリプト実行に加え、ARM の**テンプレート リンク**機能や**リソース ループ**機能を活用することで、実質的にはどのような複雑なテンプレート ベースのデプロイメントにも再利用可能なモジュール式の手法を実行できます。

次の図は、このデプロイメントの GitHub からダウンロードされるすべてのファイル間の関係を表しています。

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

このセクションでは、MongoDB クラスターの **azuredeploy.json** ファイルの構造について、順を追って説明します。

### "parameters" セクション

**azuredeploy.json** の "parameters" セクションには、このテンプレートで使用される、変更可能なパラメーターを指定します。前述の **azuredeploy-parameters.json** ファイルは、テンプレート実行時に、値を azuredeploy.json の "parameters" セクションに渡すために使用されます。

### "variables" セクション

"variables" セクションには、このテンプレート全体で使用できる変数を指定します。これには、実行時に定数または計算済みの値に設定される、いくつかのフィールド (JSON データ型またはフラグメント) が格納されます。次に、この MongoDB テンプレートの "variables" セクションの例を示します。

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

この例を詳しく調べると、2 つの異なる手法を確認できます。次の 1 つ目のフラグメントでは、"osFamilyUbuntu" 変数に 6 組のキー値ペアを含む JSON 要素が設定されます。

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

次の 2 つ目のフラグメントでは、"vmScripts" 変数に JSON 配列が割り当てられ、実行時にテンプレート言語関数 (concat)、および別の変数に文字列定数を加えた値を使用して 1 つの要素が計算されます。

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

このテンプレートの 1 つの重要な概念は、MongoDB クラスターに対して異なる「T シャツのサイズ」が定義されている方法です。これらの "tshirtSizeXXXX" 変数の 1 つを見ると、クラスターをデプロイする方法の重要な特性が説明されていることがわかります。例として M サイズを見てみましょう。

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

“Medium” MongoDB クラスターは、データをホストする 3 つの MongoDB ノードに対する VM サイズとして D2 を使用し、さらに レプリケーションのためのアービターとして使用される 4 番目の A1 VM があります。データ ノードをデプロイするために呼び出される対応するサブテンプレートは **member-resources-D2.json** であり、データ ファイル (各 250 GB) は 2 つのストレージ アカウントに格納されます。この変数は、ノードのデプロイメントと他のタスクを調整するために、“resources” セクション内で使用されます。

### "resources" セクション

**"resources"** セクションではアクションの大部分が発生します。このセクションを注意深く確認すると、すぐに 2 つの異なるケースを特定できます。1 つ目は、基本的にはメインのデプロイメントでの入れ子になったデプロイメントの呼び出しを意味する型の `Microsoft.Resources/deployments` について定義された要素です。"templateLink" 要素 (および関連するバージョン プロパティ) を通して、一連のパラメーターを入力として渡しながら、呼び出されるリンク済みテンプレート ファイルを指定できます (次のフラグメントを参照)。

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

この 1 つ目の例から明らかなように、このシナリオの **azuredeploy.json** は、ある種のオーケストレーション メカニズムとして編成され、それぞれが必要なデプロイメント アクティビティの一部を担う、他の数多くのテンプレート ファイルを呼び出しています。

特に、このデプロイメントには次のリンク済みテンプレートが使用されます。

-	**shared-resource.json**: デプロイメント全体で共有されるすべてのリソースの定義を格納します。たとえば、VM の OS ディスクおよび仮想ネットワークの格納に使用されるストレージ アカウントです。
-	**jumpbox-resources.json**: 有効にすると、Jumpbox VM に関連するすべてのリソースをデプロイします。このVM は、パブリック ネットワークから MongoDB クラスターにアクセスするために使用できるパブリック IP アドレスを持っています。
-	**arbiter-resources.json**: 有効にすると、このテンプレートは MongoDB クラスターにアービター メンバーをデプロイします。アービターはデータを含みませんが、プライマリ選択を管理するためにレプリカ セットに偶数個のノードが含まれるときに使用されます。
-	**member-resources-Dx.json**: これらのリソース テンプレートは、MongoDB ノードを効果的にデプロイします。特定のファイルは、選択されている T シャツのサイズの定義に基づきます。各ファイルは、各ノードのアタッチされているディスクの数だけが異なります。
-	**mongodb-ubuntu-install.sh**: クラスター内のすべてのノードで CustomScriptForLinux 拡張機能によって呼び出される bash スクリプト ファイルです。データ ディスクのマウントとフォーマット、およびノードへの MongoDB ビットのインストールを行います。

MongoDB クラスターをデプロイするには、特定のロジックがレプリカ セットを正しく設定できる必要があります。デプロイメント時に使用する必要がある特定のシーケンスを次に示します。

DEPLOY DATA MEMBERS (並列) => DEPLOY LAST DATA MEMBER => (省略可能) DEPLOY ARBITER

このシーケンスでは、最後のノードを除き、複数のデータ ノードのデプロイが並列に実行されます。これによりクラスターが構成されてレプリカ セットがデプロイされるので、その時点になる前に以前のすべてのノードが稼働している必要があります。最後の手順は省略可能なアービター ノードのデプロイです (アービター ノードが必要な T シャツ サイズに対してのみ)。

メイン テンプレート (azuredeploy.json) の内容をもう一度見て、このロジックの実装方法を確認します。最初はすべてのデータ メンバーです。

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
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
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

注意が必要な重要な概念は、どのようにすれば単一の種類のリソースの複数のコピーをデプロイできるか、また、各インスタンスが必要な設定に対して一意の値を設定できるかということです。この概念は、**リソース ループ**と呼ばれています。

前のフラグメントでは、パラメーター (クラスターにデプロイするノードの数) を使用して変数 (“numberOfMembers”) が設定され、それが **“copy”** 要素に渡されて子デプロイメントの数 (ループ) がトリガーされます。各デプロイメントは、クラスター内の各メンバーに対するテンプレートのインスタンス化になります。インスタンスごとに一意の値が必要なすべての設定を設定できるように、**copyindex()** 関数を使用して、特定のリソース ループ作成における現在のインデックスを示す数値を取得できます。

リソース作成におけるもう 1 つの重要な概念は、**dependsOn** JSON 配列からわかるように、リソース間の依存関係と優先順位を指定できる点です。この特定のテンプレートでは、各ノードをデプロイするには、その前に**共有リソース**のデプロイが成功している必要があります。

接続されたディスクは、**mongodb-ubuntu-install.sh** スクリプト ファイルの実行によってトリガーされるノード準備アクティビティの一部としてフォーマットされます。そのファイルの内部には、この呼び出しのインスタンスが実際にあります。

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

**vm-disk-utils-0.1.sh** は、azure-quickstart-tempates GitHub リポジトリ内の **shared_scripts\\ubuntu** フォルダーの一部であり、ディスクのマウント、フォーマット、ストライピングのための便利な機能を含みますが、この機能は、テンプレート作成の一部として同様のタスクを実行する必要があるときにいつでも再利用できます。

注意が必要なもう 1 つの興味深いフラグメントは、CustomScriptForLinux VM 拡張機能に関連するものです。これらは異なる種類のリソースとしてインストールされ、各クラスター ノード デプロイメント テンプレートに依存します。各 **member-resources-Dx.json** ファイルの最後にあるこのフラグメントを次に示します。

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

このデプロイメントに含まれる他のファイルを熟知することで、Azure リソース マネージャー テンプレートを活用しながら、任意のテクノロジに基づく複数ノード ソリューションの複雑なデプロイメント戦略を編成および調整するために必要な、すべての詳細とベスト プラクティスを理解することができます。必須ではありませんが、次の図で表されているようにテンプレート ファイルを構築する手法をお勧めします。

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

この手法では、基本的に次のことを提案しています。

-	すべてのデプロイメント アクティビティに対する中心的なオーケストレーション ポイントとして、核となるテンプレート ファイルを定義し、サブテンプレートの実行を呼び出すためにテンプレート リンクを活用します。
-	他のすべてのデプロイメント タスク (ストレージ アカウント、VNET 構成など) の間で共有されるすべてのリソースをデプロイする、特定のテンプレート ファイルを作成します。これは、共通のインフラストラクチャに関して類似した要件を持つデプロイメント間で頻繁に再利用できます。
-	特定のリソースに固有のスポット要件用にオプションのリソース テンプレートを含みます。
-	リソース グループの同一メンバー (クラスター内のノードなど) に対して、一意のプロパティを持つ複数のインスタンスをデプロイするため、リソース ループを活用する特定のテンプレートを作成します。
-	すべてのデプロイメント後のタスク (製品のインストールや構成など) について、スクリプト デプロイメントの拡張機能を活用し、各テクノロジに固有のスクリプトを作成します。

詳細については、「[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。

<!--HONumber=54-->
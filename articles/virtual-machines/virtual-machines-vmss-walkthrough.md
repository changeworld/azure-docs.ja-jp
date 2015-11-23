<properties
	pageTitle="仮想マシン スケール セットの自動スケール | Microsoft Azure"
	description="初めての Azure 仮想マシン スケール セットの作成と管理を開始します"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# 仮想マシン スケール セットでのマシンの自動スケール

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md)。


このドキュメントは、パブリック プレビュー期間中に Azure 仮想マシン スケール セットを初めて作成および管理するためのクイック ガイドです。

このガイドは、Azure リソース マネージャー (ARM) テンプレートを使用して仮想マシンや VNET などの Azure リソースをデプロイする操作について既によく知っているユーザーを対象としています。よく知らない場合は、「リソース」セクションの最初の 3 つのリンクで、リソース マネージャーの概要を知ることができます。

## VM スケール セットの概要と、それを利用する理由

VM スケール セットは、同一の VM のセットをデプロイおよび管理するために使用できる Azure コンピューティング リソースです。

すべての VM が同じ構成になっており、VM を事前にプロビジョニングする必要がない、真の自動スケールをサポートするように設計されています。そのため、ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロード、コンピューティング リソースのスケールアウトとスケールインが必要なアプリケーションなどを対象にした大規模サービスを簡単に構築できます。スケール操作は、暗黙的に障害ドメインと更新ドメインに分散されます。VM スケール セットの概要については、最近の [Azure ブログでの発表](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview)を参照してください。

## VM スケール セットの作成と操作

VM スケール セットは、個々の Azure リソース マネージャー VM と同様に、JSON テンプレートと REST 呼び出しを使用して定義およびデプロイできます。そのため、任意の標準 Azure リソース マネージャー デプロイ方法を使用できます。このドキュメントでは、いくつかの例を紹介します。

スクリプト言語やプログラミング言語での命令型コマンドのサポートなどの個々の VMSS 統合領域と完全なポータル統合は開発中であり、プレビュー期間中に段階的にリリースされます。

## Azure ポータルでの VM スケール セットのデプロイと管理

初めは、Azure ポータルで VM スケール セットを最初から自分で作成することはできません。ポータル サポートは作業中であり、この作業の最初の段階では、作成済みのスケール セットをポータルで表示することだけができます。作成するには、テンプレート/スクリプトを使用する必要があります。いずれの場合も、"ポータル" は新しい Azure ポータル ([https://portal.azure.com/](https://portal.azure.com/)) を意味しています。

ただし、テンプレートをデプロイするポータル機能を使用して、スケール セットなどのリソースをデプロイすることはできます。単純なスケール セットをデプロイする簡単な方法は、次のようなリンクを使用することです。

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

VM スケール セット用のサンプル テンプレートのセットが、Azure Quickstart teamplates GitHub リポジトリ ([https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)) にあります。タイトルに _vmss_ が付いているテンプレートを探してください。

これらのテンプレートの readme には、ポータルのデプロイ機能にリンクしている、このようなボタンが表示されます。

スケール セットをデプロイするには、ボタンをクリックし、ポータルで必要なすべてのパラメーターを指定します。注: リソースで大文字がサポートされているかどうかが不明な場合は、常に小文字のパラメーター値を使用する方が安全です。

**ポータル/リソース エクスプローラーでの VM スケール セット リソースの監視**

スケール セットがデプロイされた後、ポータルにはスケール セットの基本ビューが表示されますが、プレビュー期間の初期には詳細なプロパティが表示されなかったり、VM スケール セットで実行されている VM にドリルダウンできなかったりします。

完全なポータル統合が行われるまでは、**Azure リソース エクスプローラー** ([https://resources.azure.com](https://resources.azure.com)) を使用して VM スケール セットを表示することをお勧めします。VM スケール セットは、Microsoft.Compute の下のリソースなので、このサイトから以下のリンクを展開すると表示できます。

Subscriptions -> 自分のサブスクリプション -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> 自分の VM スケール セット

## PowerShell を使用した VM スケール セットのデプロイと管理

現在の任意の Azure PowerShell バージョンを使用して、VMSS テンプレートとクエリ リソースをデプロイできます。

**重要:** 以下に示す例は、多くのコマンドに _AzureRm_ プレフィックスが導入された、[Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/) 用です。それより前の、0.9.8 以前などのバージョンの PowerShell を使用している場合は、例のコマンドから "**Rm**" を削除してください。また、例では既に PowerShell 環境で Azure へのログイン接続が確立されていることが前提となっています (_Login-AzureRmAccount_)。

次に例を示します。

&#35; **リソース グループを作成する**

New-AzureRmResourcegroup -name myrg -location 'Southeast Asia'

&#35; **2 つの VM の VM スケール セットを作成する**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

指定しなかったパラメーターの入力を求めるメッセージが表示されます (この例では場所、ユーザー名、パスワードなど)。

&#35; **VM スケール セットの詳細を取得する**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; または、より詳細を | ConvertTo-Json -Depth 10 を通じてパイプする

&#35; または、さらに詳細な情報のために、コマンドに –debug を追加する

&#35; スケールアウトまたはスケールインする

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **スケール セットを削除する**

&#35; 簡単な方法: リソース グループ全体とその内容すべてを削除する

Remove-AzureRmResourceGroup -Name myrg

&#35; 正確な方法: 特定のリソースを削除する

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### スケール セット用の命令型 PowerShell コマンド

Azure PowerShell の今後のビルドには、テンプレートを使用せずに VM スケール セットを作成および管理するための命令型コマンドのセットが含まれます。このビルドは、[https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases) のプレビューで利用できます。命令型 API の使用方法の例については、以下を参照してください。

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Azure CLI を使用した VM スケール セットのデプロイと管理

現在の任意の Azure CLI バージョンを使用して、VMSS テンプレートとクエリ リソースをデプロイできます。

CLI のインストールは、Docker コンテナーから行うのが最も簡単です。インストールについては、[https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/) を参照してください。

CLI の使用方法については、[https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/) を参照してください。

### VM スケール セット CLI の例

&#35; **リソース グループを作成する**

azure group create myrg "Southeast Asia"

&#35; **VM スケール セットを作成する**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; これで、パラメーターの入力が動的に求められます。引数として指定することもできます。

&#35; **スケール セットの詳細を取得する**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; または、詳細を取得するには

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

今後の Azure CLI ビルドには、VM スケール セット内の VM でテンプレートおよびプラットフォームを操作せずに、直接 VM スケール セットを実行デプロイおよび管理できる命令型コマンドが含まれます。このビルドは、[https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/) で追跡できます。

## VM スケール セットのテンプレート

このセクションでは、VM スケール セットを作成するための単純な Azure テンプレートを紹介し、単一インスタンス仮想マシンを作成するために使用するテンプレートと比較します。また、VM スケール セット テンプレートの簡単なビデオ解説が、[https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) にあります。

### テンプレートのしくみ

まず、Ubuntu 仮想マシンの VM スケール セットを作成するための単純なテンプレートから始めて、その後、各コンポーネントを詳しく見ていきましょう。この例では、VM スケール セットの基盤となる、VNET、ストレージ アカウントなどのリソースも作成します。サンプルは、[https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json) にあります。この "hello world" サンプルでは、VNET の外部から VM スケール セット内の VM に接続するための直接的な方法がないことに注意してください。これは、各 VM に内部 IP アドレスが割り当てられているためです。ロード バランサーまたは jump box を使用して外部から接続する方法については、[Azure のクイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates)のシナリオのセクションと例を参照してください。

### テンプレート ヘッダー

スキーマ、およびコンテンツのバージョンを指定します。

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### パラメーター

他の Azure リソース マネージャー (ARM) テンプレートと同様に、このセクションではデプロイ時に指定されるパラメーターを定義します。このサンプルでは、VM スケール セットの名前、開始時の VM インスタンスの数、ストレージ アカウントの作成時に使用する一意の文字列などです (大文字と小文字がどのように扱われるかを知っている場合を除き、ストレージ アカウントのようなオブジェクトの値を入力するときは、常に小文字を使用してください)。

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### 変数

変数を定義する標準 ARM テンプレート コンポーネントは、テンプレートの後の方で参照します。このサンプルでは、変数を使用して、使用する OS、いくつかのネットワーク構成の詳細、ストレージ設定、および場所を定義します。場所については、_location()_ 関数を使用して、デプロイ先のリソース グループから取得します。

ストレージ アカウント プレフィックス用に一意の文字列の配列が定義されていることに注意してください。詳細については、「ストレージ」のセクションを参照してください。

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### リソース

このセクションでは、各リソースの種類が定義されます。

````
   "resources": [
````


**[ストレージ] をクリックします。** VM スケール セットを作成する場合は、ストレージ アカウントの配列を指定します。VM インスタンスのための OS ディスクは、各アカウント間で均等に分散されます。今後、VM スケール セットは、管理ディスク方式を使用するようになります。この方式では、ストレージ アカウントを管理しなくても、スケール セット定義の一部としてストレージ プロパティを記述するだけで済みます。現時点では、必要な数のストレージ アカウントを事前に作成する必要があります。ストレージ アカウントは、5 つ作成することをお勧めします。それで、1 つのスケール セット内の最大で 100 個 (現在の最大数) の VM を十分にサポートできます。

ストレージ アカウントのセットが作成される場合、ストレージ アカウントはストレージ スタンプ内の複数のパーティションに分散され、分散スキームはストレージ アカウント名の最初の文字に依存します。このため、最適なパフォーマンスを得るために、作成する各ストレージ アカウントの名前の最初の文字は、異なるアルファベットにすることをお勧めします。手動で名前を付けることも、このサンプルのように uniqueString() 関数を使用して疑似乱数分散を指定することもできます。

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**仮想ネットワーク。** VNET を作成します。複数のスケール セットと、同じ VNET 内に 1 つの VM を設定できます。

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

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
````

### virtualMachineScaleSets リソース

多くの点で、_virtualMachineScaleSets_ リソースは _virtualMachines_ リソースに似ています。どちらも Microsoft.Compute リソース プロバイダーによって提供され、同じレベルです。主な違いは、何個の VM を作成するかを指定する _capacity_ 値を設定することと、ここでの定義が VM スケール セット内のすべての VM に影響することです。

前に作成したストレージ アカウントと VNET を _dependsOn_ セクションが参照し、capacity が _instanceCount_ パラメーターを参照するしくみに注意してください。

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**プロパティ**

VM スケール セットには、upgradePolicy 設定があります。将来のバージョンでは、スライスされた更新 (たとえば、一度に VM の 20% の構成を変更するなど) がサポートされますが、現時点では upgradePolicy を手動または自動に設定できます。手動の場合、テンプレートを変更して再デプロイすると、変更は新しい VM が作成されるか、拡張が更新された (たとえば、_capacity_ が増やされた) ときにだけ有効になります。自動の場合、すべての VM が "一掃" するように更新され、すべてが再起動されます。一般に、手動の方が安全な方法です。VM を個別に削除し、必要に応じて再デプロイして、段階的に更新できます。

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Properties->virtualMachineProfile**

ここでは、前に作成したストレージ アカウントを VM のコンテナーとして参照します。実際のコンテナーを事前に作成する必要はありません。必要なのはアカウントだけです。

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Properties->osProfile**

VM スケール セットが個々の VM と違う点の 1 つは、コンピューター名がプレフィックスであることです。VM スケール セット内の VM インスタンスは、myvm\_0、myvm\_1 というような名前で作成されます。

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Properties->networkProfile**

VMSS のネットワーク プロファイルを定義するときは、NIC 構成と IP 構成の両方に名前があることに注意してください。NIC 構成には "_プライマリ_" 設定があり、サブネット ID は前に VNET の一部として作成したサブネット リソースを参照します。

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Properties->extensionProfile**

上の単純な例では、拡張機能プロファイルは必要ありませんでした。このテンプレートで動作し、CustomScript 拡張機能を使用して Apache および PHP をデプロイする拡張機能プロファイルは、[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) にあります。この例では、ネットワーク IP プロパティもロード バランサーを参照することに注意してください。ロード バランサーについては、「VM スケール セットのシナリオ」のセクションで詳細に説明します。

## VM スケール セットのシナリオ

このセクションでは、いくつかの一般的な VM スケール セットのシナリオおよびサンプル テンプレートについて説明します。現時点ではテンプレートが必要ですが、いくつかのシナリオは、今後ポータルに統合されます。また、一部の高レベルの Azure サービス (Batch、Service Fabric、Azure Container Service など) も、これらのシナリオを使用します。

## VM スケール セット インスタンスへの RDP/SSH

VM スケール セットは VNET の中に作成され、その中の個々の VM にはパブリック IP アドレスが割り当てられていません。これは、望ましいことです。通常は、コンピューティング グリッド内のすべてのステートレス リソースに個別の IP アドレスを割り当てるための費用や管理オーバーヘッドを避けたいためです。また、これらの VM には VNET 内の他のリソース (ロード バランサー、スタンドアロン仮想マシンなど、パブリック IP アドレスを持つリソース) から簡単に接続できます。

ここでは、VMSS VM に接続するための 2 つの簡単な方法を説明します。

### NAT 規則を使用した VM への接続

パブリック IP アドレスを作成し、ロード バランサーに割り当て、IP アドレス上のポートを VM スケール セット内の VM 上のポートにマッピングする受信 NAT 規則を定義することができます。例:

Public IP->Port 50000 -> vmss\_0->Port 22 Public IP->Port 50001 -> vmss\_1->Port 22 Public IP->Port 50002-> vmss\_2->Port 22

1 つのパブリック IP を使用しているスケール セット内のすべての VM への SSH 接続を NAT 規則で有効にする VM スケール セットの作成例は、[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat) にあります。

同じことを RDP と Windows で行う例は、[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) にあります。

### "jump box" を使用した VM への接続

VM スケール セットとスタンドアロン VM を同じ VNET 内に作成する場合、スタンドアロン VM と VMSS VM は、VNET/サブネットによって定義された内部 IP アドレスを使用して、互いに接続することができます。

パブリック IP アドレスを作成し、スタンドアロン VM に割り当てる場合は、スタンドアロン VM に RDP または SSH 接続して、そのコンピューターから VMSS インスタンスに接続できます。これでお気付きかもしれませんが、単純な VM スケール セットは、本質的に、既定の構成のパブリック IP アドレスを持つ単純なスタンドアロン VM よりも安全です。

この方法の例として、次のテンプレートは、VM のスケール セット ベースのクラスターを管理するスタンドアロン マスター VM で構成される単純な Mesos クラスターを作成します: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## VM スケール セット インスタンスへのラウンド ロビン負荷分散

"ラウンド ロビン" 方式を使用して VM のコンピューティング クラスターに作業を割り当てる場合は、それに応じた負荷分散規則で Azure ロード バランサーを構成できます。また、指定されたプロトコル、間隔、および要求パスでポートを ping して、アプリケーションが実行されていることを確認するためのプローブを定義することもできます。

以下に、IIS Web サーバーを実行している VM の VM スケール セットを作成し、各 VM が受け取る負荷を分散するロード バランサーを使用する例を示します。この例では、各 VM の特定の URL を ping するために、HTTP プロトコルも使用します: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json)。Microsoft.Network/loadBalancers というリソースの種類と、virtualMachineScaleSet の networkProfile および extensionProfile を見てください。

## VM スケール セット インスタンスと Azure 自動スケール

CPU/メモリ/ディスクの使用量やその他のイベントに応じて VM スケール セットのインスタンス数 (_capacity_) を変更する場合は、Microsoft.Insights リソース プロバイダーの豊富な自動スケール設定のセットを利用できます。利用可能な設定については、Insights REST ドキュメント ([https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx)) で確認できます。

Insights Autoscale は、VM スケール セットに直接統合されています。セットアップするには、Microsoft.Insights/autoscaleSettings というリソースの種類を定義します。これには、スケール セットを参照する _targetResourceUri_ と _metricResourceUri_ があります。スケール セットを定義するときに、Windows には Azure 診断エージェント (WAD)、Linux には Linux 診断拡張機能 (LDE) をインストールする _extensionProfile_ を含めます。以下に、1 分の時間単位で 5 分のサンプリング期間にわたって平均 CPU 使用率が 50% を超える場合に、事前に定義された制限に達するまで VM を追加する Linux の例を示します。

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale)

今後、VM スケール セットでの自動スケールも、Azure ポータルに直接統合されます。

## PaaS クラスター マネージャーでのコンピューティング クラスターとしての VM スケール セットのデプロイ

VM スケール セットは、次世代の worker ロールとして説明されることがあります。有効な説明ですが、スケール セットの機能を PaaS v1 worker ロール機能と混同させる危険性があります。

ある意味で、VM スケール セットは、真の "worker ロール" または worker リソースを提供します。これにより、プラットフォーム/ランタムに依存せず、カスタマイズ可能で、Azure リソース マネージャー IaaS に統合された、汎用コンピューティング リソースを利用できます。

PaaS v1 worker ロールは、プラットフォーム/ランタイムのサポートの面では制限がありますが (Windows プラットフォーム イメージのみ)、VIP スワップ、構成可能なアップグレード設定、ランタイム/アプリ デプロイ固有の設定などのサービスも提供しています。これらのサービスは、VM スケール セットでは_まだ_利用できないか、Service Fabric などの他の高レベル PaaS サービスによって提供されます。このような点を踏まえておけば、VM スケール セットは PaaS をサポートするインフラストラクチャであると見なすことができます。つまり、Service Fabric のような PaaS ソリューションや Mesos のようなクラスター マネージャーを、スケーラブルなコンピューティング層として VM スケール セット上に構築できます。

以下に、スタンドアロン VM と同じ VNET 内に VM スケール セットをデプロイする Mesos クラスターの例を示します。スタンドアロン VM は、Mesos マスターであり、VM スケール セットはスレーブ ノードのセットを表します: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)。将来のバージョンの [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) では、VM スケール セットに基づいて、このシナリオの、より複雑で強化されたバージョンがデプロイされます。

## VM スケール セットのスケールアウトとスケールイン

VM スケール セット内の仮想マシンの数を増減するには、単に _capacity_ プロパティを変更し、テンプレートを再デプロイします。このように単純なので、Azure 自動スケールでサポートされていないカスタム スケール イベントを定義する場合に、簡単に独自のカスタム スケール層を記述できます。

容量を変更するためにテンプレートを再デプロイする場合は、SKU と更新された容量だけが含まれている、非常に小さいテンプレートを定義できます。その例は、[https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json) にあります。

## VM スケール セットのパフォーマンスとスケールのガイダンス

- パブリック プレビュー期間中は、複数の VM スケール セット内に 500 個を超える VM を同時に作成しないでください。この制限は、今後緩和されます。
- ストレージ アカウントあたり 40 個以下の VM を計画します。
- 各ストレージ アカウント名の最初の文字は、できるだけ重複しないようにします。[Azure のクイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/)のサンプル VMSS テンプレートには、その方法の例が紹介されています。
- カスタム VM を使用している場合は、1 つのストレージ アカウントで、VM スケール セットあたり 40 個以下の VM を計画します。VM スケール セットのデプロイを開始する前に、イメージをストレージ アカウントに事前コピーする必要があります。詳細については、FAQ を参照してください。
- VNET あたり 2048 個以下の VM を計画します。この制限は、今後緩和されます。
- 作成できる VM の数は、どのリージョンでも、コンピューティング コア クォータによって制限されます。コンピューティング クォータの制限を緩和するには、現時点でクラウド サービスや IaaS v1 で使用するためのコアの上限が高い場合でも、カスタマー サポートへの連絡が必要である場合があります。クォータを照会するには、Azure CLI コマンドの場合は _azure vm list-usage_、PowerShell コマンドの場合は _Get-AzureRmVMUsage_ (1.0 以前のバージョンの PowerShell を使用している場合は _Get-AzureVMUsage_) を実行します。


## VM スケール セットの FAQ

**Q.VM スケール セットには何個の VM を設定できますか?**

A.複数のストレージ アカウントに分散できるプラットフォーム イメージを使用する場合は、100 個です。カスタム イメージを使用する場合は、最大で 40 個です。プレビュー期間中は、カスタム イメージが 1 つのストレージ アカウントに制限されているためです。

**VM スケール セットには、他にどのようなリソース制限がありますか?**

A.既存の Azure サービスの制限が適用されます: [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

プレビュー期間中は、リージョンごとに複数のスケール セットで 500 個を超える VM を作成できないという制限もあります。

**Q.VM スケール セット内でデータ ディスクはサポートされていますか?**

A.最初のリリースではされていません。データを格納するためのオプションは、次のとおりです。

- Azure ファイル (SMB 共有ドライブ)

- OS ドライブ

- 一時ドライブ (ローカルで、Azure Storage にサポートされない)

- 外部データ サービス (リモート データベース、Azure テーブル、Azure BLOB など)

**Q.VMSS は、どの Azure リージョンでサポートされていますか?**

A.Azure リソース マネージャーがサポートされているすべてのリージョンで、VM スケール セットがサポートされています。

**Q.カスタム イメージを使用して VMSS を作成するにはどうすればよいですか?**

A.vhdContainers プロパティを空白のままにします。たとえば、次のようにします。

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**Q.VMSS 容量を 20 から 15 に減らすと、どの VM が削除されますか?**

A.最後の 5 個の VM (インデックスが大きいもの) が削除されます。

**Q.その後、容量を 15 から 18 に増やす場合はどうなるでしょうか?**

18 に増やすと、15、16、17 のインデックスを持つ VM が作成されます。どちらの場合も、VM は FD と UD 間に分散されます。

**Q.VM スケール セットで複数の拡張機能を使用する場合、実行順序を強制できますか?**

A.直接的にではありませんが、customScript 拡張機能の場合は、スクリプトで他の拡張機能が完了するまで待機できます (たとえば、拡張機能ログを監視することによって。[https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh) を参照)。

**Q.VM スケール セットは、Azure 可用性セットと連携できますか?**

A.はい。スケール セットは、3 つの FD と 5 つの UD を持つ、暗黙的な可用性セットです。virtualMachineProfile の下のものは、何も構成する必要はありません。今後のリリースでは、スケール セットが複数のテナントにまたがる可能性がありますが、現時点では 1 つの可用性セットです。

## 次のステップ

VM スケール セットのプレビュー期間中、ドキュメントは更新され、ポータルや自動スケールなどの統合機能が使用できるようになります。

お客様からのフィードバックは、お客様が必要とする機能を提供できるサービスを構築するうえで、非常に重要です。良かった点、良くなかった点、改善が必要な点などをお知らせください。フィードバックは、[vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com) までお寄せください。

## リソース

| **トピック** | **リンク** |
| --- | --- |
| Azure リソース マネージャーの概要 | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Azure リソース マネージャーのテンプレート | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Azure リソース マネージャーのテンプレートの関数 | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| サンプル テンプレート (github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| VM スケール セット REST API ガイド | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| VM スケール セットのビデオ | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| 自動スケールの設定 (MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Azure でのリソース グループのデプロイのトラブルシューティング | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->
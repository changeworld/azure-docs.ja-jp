<properties
   pageTitle="Resource Manager テンプレートのチュートリアル | Microsoft Azure"
   description="基本的な Azure IaaS アーキテクチャをプロビジョニングする Resource Manager テンプレートのステップ バイ ステップ チュートリアルです。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/14/2016"
   ms.author="navale;tomfitz"/>
   
# Resource Manager テンプレートのチュートリアル

このトピックでは、Resource Manager テンプレートを作成する手順について説明します。デプロイする Azure サービスについては精通しているものの、そのインフラストラクチャをテンプレートで表現する方法については精通していない読者を想定しています。[クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates)の[ロード バランサーとロード バランサー規則テンプレートが構成された 2 つの VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) に基づいてテンプレートを作成しますが、このテクニックは、作成する必要のあるあらゆるテンプレートに利用できます。

一般的なアーキテクチャを見てみましょう。

* 同じストレージ アカウントを使用する 2 つのバーチャル マシンは、同じ可用性セットおよび仮想ネットワークの同じサブネット上にあります。
* 各仮想マシンの単一の NIC と VM IP アドレス。
* ポート 80 の負荷分散規則が構成されたロード バランサー

![アーキテクチャ](./media/resource-group-overview/arm_arch.png)

このアーキテクチャを Azure にデプロイし、Resource Manager テンプレートを使用してこのアーキテクチャを別の機会に簡単に再度デプロイできるようにしたいものの、そのテンプレートの作成方法がわかりません。このトピックは、テンプレートに記述する内容を理解するのに役立ちます。

テンプレートを作成する際には、任意のエディターを使用できます。Visual Studio にはテンプレートの作成を簡略化するツールが用意されていますが、Visual Studio でこのチュートリアルを完了する必要はありません。Visual Studio を使用して Web アプリと SQL Database のデプロイを作成するチュートリアルについては、「[Visual Studio での Azure リソース グループの作成とデプロイ](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」をご覧ください。

## Resource Manager テンプレートの作成

このテンプレートは、デプロイするすべてのリソースを定義する JSON ファイルです。デプロイ中に指定されるパラメーター、他の値と式から構築される変数、デプロイからの出力を定義することもできます。

まず、ごく簡単なテンプレートから始めましょう。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }

このファイルは **azuredeploy.json** として保存します。

まず **resources** セクションについて集中的に確認し、**parameters** と **variables** についてはこのトピックの後の方で確認します。

## ストレージ アカウント
仮想マシンで使用するストレージ アカウントを定義します。次のように、**resources** リソース セクションで、ストレージ アカウントを定義するオブジェクトを追加します。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

これらのプロパティと値がどのようなものなのか、疑問に思われるかもしれません。**type**、**name**、**apiVersion**、**location** の各プロパティは、すべての種類のリソースで利用できる標準的な要素です。一般的な要素については、「[リソース](../resource-group-authoring-templates/#resources)」をご覧ください。デプロイ中に渡すパラメーター値に **name** を設定しています。**location** は、リソース グループによって使用される場所として設定しています。**type** と **apiVersion** を決定する方法については、以下のセクションで説明します。

**properties** セクションには、特定のリソースの種類に固有のプロパティがすべて含まれます。このセクションで指定する値は、その種類のリソースを作成するための REST API の PUT 操作と正確に一致します。ストレージ アカウントを作成するときは、**accountType** を指定する必要があります。[ストレージ アカウントを作成するための REST API](https://msdn.microsoft.com/library/azure/mt163564.aspx) を見ると、REST 操作の properties セクションにも **accountType** プロパティが含まれていることがわかります。また、許可されている値も記載されています。この例では、アカウントの種類は **Standard\_LRS** に設定されていますが、その他の値を指定することも、ユーザーがアカウントの種類をパラメーターとして渡せるようにすることもできます。

## 可用性セット
ストレージ アカウントの定義の後に、バーチャル マシンの可用性セットを追加します。この場合はその他のプロパティは必要ないため、その定義はかなりシンプルです。更新ドメインの数と障害ドメインの数の値を定義する場合は、[可用性セットを作成するための REST API](https://msdn.microsoft.com/library/azure/mt163607.aspx) を確認し、完全な properties セクションをご覧ください。

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

ご覧のように、**name** は変数の値に設定されています。このテンプレートでは、何か所かで可用性セットの名前が必要です。その値を 1 回だけ定義し、複数の場所で使用すれば、テンプレートをより簡単に管理できます。

**type** に対して指定する値には、リソース プロバイダーとリソースの種類の両方が含まれます。可用性セットの場合、リソース プロバイダーは **Microsoft.Compute**、リソースの種類は **availabilitySets** です。使用可能なリソース プロバイダーの一覧を取得するには、次の PowerShell コマンドを実行してください。

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

Azure CLI を使用している場合は、次のコマンドを使用できます。

    azure provider list

このトピックでは、ストレージ アカウント、仮想マシン、仮想ネットワークと共に作成しているため、以下を操作します。

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

特定のプロバイダーのリソースの種類を表示するには、次の PowerShell コマンドを実行します。

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes

Azure CLI を使用している場合は、次のコマンドを実行すると、使用可能な種類が JSON 形式で返され、ファイルに保存されます。

    azure provider show Microsoft.Compute --json > c:\temp.json

種類の 1 つとして、**availabilitySets** が **Microsoft.Compute** に示されます。種類の完全な名前は **Microsoft.Compute/availabilitySets** です。テンプレート内のどのリソースについても、リソースの種類の名前を確認できます。

## パブリック IP
パブリック IP アドレスを定義します。設定するプロパティについては、[パブリック IP アドレスの REST API](https://msdn.microsoft.com/library/azure/mt163590.aspx) をご覧ください。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

割り当て方式は **Dynamic** に設定されていますが、必要な値に設定することも、パラメーター値を受け入れるように設定することもできます。ここでは、テンプレートのユーザーがドメイン名ラベルの値を渡せるようにしてあります。

次に、**apiVersion** の特定方法を確認しましょう。指定する値は、単純に、リソースの作成時に使用する REST API のバージョンに一致します。そのため、そのリソースの種類については、REST API のドキュメントで確認できます。特定の種類については、次の PowerShell コマンドを実行して確認することもできます。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions

返される値は次のとおりです。

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Azure CLI で API バージョンを確認するには、前に示した同じ **azure provider show** コマンドを実行します。

新しいテンプレートの作成時には、最新の API バージョンを採用してください。

## Virtual Network とサブネット
1 つのサブネットを持つ仮想ネットワークを作成します。設定するすべてのプロパティについては、[仮想ネットワークの REST API](https://msdn.microsoft.com/library/azure/mt163661.aspx) をご覧ください。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## Load Balancer
次に、外部に接続するロード バランサーを作成します。このロード バランサーはパブリック IP アドレスを使用するため、**dependsOn** セクションでパブリック IP アドレスへの依存関係を宣言する必要があります。これは、パブリック IP アドレスのデプロイが終わるまでは、ロード バランサーはデプロイされないことを意味します。この依存関係を定義しないと、Resource Manager によってリソースが並列的にデプロイされ、まだ存在しないパブリック IP アドレスにロード バランサーが設定されることになるため、エラーが発生します。

このリソースの定義では、バックエンド アドレス プール、VM への RDP に対するいくつかの受信 NAT 規則、ポート 80 での TCP プローブに関する負荷分散規則も作成します。すべてのプロパティについては、[ロード バランサーの REST API](https://msdn.microsoft.com/library/azure/mt163574.aspx) を確認してください。

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## ネットワーク インターフェイス
2 つのネットワーク インターフェイス (VM ごとに 1 つずつ) を作成します。同じエントリをネットワーク インターフェイスごとに追加する必要はありません。[copyIndex() 関数](resource-group-create-multiple.md) を使ってコピー ループ (nicLoop と呼ばれます) を反復処理し、`numberOfInstances` 変数で定義された数だけネットワーク インターフェイスを作成できます。ネットワーク インターフェイスを作成するには、仮想ネットワークとロード バランサーが作成されている必要があります。仮想ネットワークの作成時に定義されたサブネットとロード バランサー ID を使用して、ロード バランサー アドレス プールと受信 NAT 規則を構成します。すべてのプロパティについては、[ネットワーク インターフェイスの REST API](https://msdn.microsoft.com/library/azure/mt163668.aspx) をご覧ください。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## 仮想マシン
[ネットワーク インターフェイス](#network-interface)の作成時と同じように、copyIndex() 関数を使って 2 つの仮想マシンを作成します。VM を作成するには、ストレージ アカウント、ネットワーク インターフェイス、可用性セットが必要です。この VM は、`storageProfile` プロパティで定義されているとおりに、マーケットプレース イメージから作成されます。また、イメージの発行者、プラン、SKU、バージョンを定義するために、`imageReference` が使用されます。最後に、VM の診断を有効にするために、診断プロファイルを構成します。

マーケットプレース イメージの該当するプロパティを探すには、[VM の検索](./virtual-machines/resource-groups-vm-searching.md)に関する記事を参考にしてください。サード パーティ ベンダーによって発行されたイメージの場合は、`plan` という別のプロパティを指定する必要があります。クイックスタート ギャラリーの[このテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic)で例を確認できます。


```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

テンプレートの各種リソースの定義が完了しました。

## パラメーター

parameters セクションでは、テンプレートのデプロイ時に指定できる値を定義します。デプロイ中に変更する必要があると思われる値のパラメーターのみを定義してください。デプロイ中に指定されない場合に使用されるパラメーターの既定値を指定できます。**imageSKU** パラメーターには、ご覧の許可されている値を定義できます。

```
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## 変数

variables セクションでは、テンプレート内の複数の場所で使用される値のほか、他の式または変数から構築される値を定義できます。変数は、テンプレートの構文を簡略化するために、頻繁に使用されます。

```
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```



## 次のステップ

これでテンプレートの作成が終わり、デプロイできる状態になりました。

- テンプレートの構造の詳細については、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」をご覧ください。
- テンプレートをデプロイする方法の詳細については、「[Azure Resource Manager のテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)」をご覧ください。

<!---HONumber=AcomDC_0323_2016-->
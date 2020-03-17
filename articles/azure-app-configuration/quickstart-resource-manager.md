---
title: Azure App Configuration を使用した VM の自動デプロイのクイックスタート
description: このクイックスタートでは、Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用して Azure App Configuration ストアをデプロイする方法を示します。 次に、ストア内の値を使用して VM をデプロイします。
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: c45f6855c33dff2790ced306fd7f049b98dd1387
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126383"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>クイック スタート:App Configuration と Resource Manager テンプレートを使用した VM の自動デプロイ

Azure PowerShell モジュールは、PowerShell コマンドレットまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 このクイックスタートでは、Azure PowerShell と Azure Resource Manager テンプレートを使用して Azure App Configuration ストアをデプロイする方法を示します。 次に、ストア内のキー値を使用して VM をデプロイする方法を学習します。

前提条件テンプレートを使用して App Configuration ストアを作成し、Azure portal または Azure CLI を使用してキー値をストアに追加します。 プライマリ テンプレートは、既存の構成ストアの既存のキーと値の構成を参照します。 取得された値は、この例の VM のように、テンプレートによって作成されたリソースのプロパティを設定するために使用されます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

* このクイック スタートには、Azure PowerShell モジュールが必要です。 ローカル マシンにインストールされているバージョンを調べるには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-Az-ps)に関するページを参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、ポップアップ ブラウザーに Azure 資格情報を入力します。

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

サブスクリプションが複数ある場合は、次のコマンドレットを実行して、このクイックスタートに使用するサブスクリプションを選択します。 `<your subscription name>` は、必ず実際のサブスクリプションの名前に置き換えてください。

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Azure App Configuration ストアをデプロイする

キー値を VM に適用する前に、既存の Azure App Configuration ストアが必要です。 このセクションでは、Azure Resource Manager テンプレートを使用して Azure App Configuration ストアをデプロイする方法について詳しく説明します。 既にアプリ構成ストアがある場合は、この記事の次のセクションに移動できます。 

1. 次の json コードをコピーし、*prereq.azuredeploy.json* という名前の新しいファイルに貼り付けます。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. 次の json コードをコピーし、*prereq.azuredeploy.parameters.json* という名前の新しいファイルに貼り付けます。 **GET-UNIQUE** は、実際の構成ストアの一意の名前に置き換えてください。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. PowerShell ウィンドウで次のコマンドを実行して、Azure App Configuration ストアをデプロイします。 リソース グループ名、テンプレート ファイル パス、およびテンプレート パラメーター ファイル パスを必ず置き換えてください。

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>VM 構成のキー値を追加する

App Configuration ストアは Azure Resource Manager テンプレートを使用して作成できますが、キー値を追加するには Azure portal または Azure CLI を使用する必要があります。 このクイックスタートでは、Azure portal を使用してキー値を追加します。

1. デプロイが完了したら、[Azure portal](https://portal.azure.com) で新しく作成した App Configuration ストアに移動します。

1. **[設定]**  >  **[アクセス キー]** の順に選択します。 接続文字列の読み取り専用の主キーをメモします。 この接続文字列は、後で、作成した App Configuration ストアと通信するようにアプリケーションを構成する際に使用します。

1. [構成**エクスプローラー]**  >  **[作成]** の順に選択して、次のキーと値のペアを追加します。

   |Key|Value|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   **[ラベル]** に「*template*」と入力します。 **[コンテンツ タイプ]** は空のままにします。

## <a name="deploy-vm-using-stored-key-values"></a>格納されているキー値を使用して VM をデプロイする

キー値がストアに追加されたので、Azure Resource Manager テンプレートを使用して VM をデプロイする準備ができました。 このテンプレートでは、作成した **windowsOsVersion** キーと **diskSizeGB** キーを参照します。

1. 次の json コードをコピーし、*azuredeploy.json* という名前の新しいファイルに貼り付けます。または、[Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json)からファイルをダウンロードします。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
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
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. 次の json コードをコピーし、*azuredeploy.parameters.json* という名前の新しいファイルに貼り付けます。または、[Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json)からファイルをダウンロードします。

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   テンプレートのパラメーター値を次の値に置き換えます。

   |パラメーター|Value|
   |-|-|
   |adminPassword|VM の管理者パスワード。|
   |appConfigStoreName|対象の Azure App Configuration ストアの名前。|
   |appConfigStoreResourceGroup|対象の App Configuration ストアが含まれているリソース グループ。|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|VM の管理者のユーザー名。|
   |storageAccountName|VM に関連付けられているストレージ アカウントの一意の名前。|
   |domainNameLabel|一意のドメイン名。|

1. PowerShell ウィンドウで次のコマンドを実行して、Azure App Configuration ストアをデプロイします。 リソース グループ名、テンプレート ファイル パス、およびテンプレート パラメーター ファイル パスを必ず置き換えてください。

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

お疲れさまでした。 Azure App Configuration に格納されている構成を使用して VM をデプロイできました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、App Configuration ストア、VM、および関連するすべてのリソースを削除します。 App Configuration ストアまたは VM を今後使用する予定がある場合は、削除をスキップできます。 このジョブを今後使用しない場合には、次のコマンドレットを実行して、このクイック スタートで作成したすべてのリソースを削除してください。

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Resource Manager テンプレートと Azure App Configuration のキー値を使用して VM をデプロイしました。

Azure App Configuration を使用して他のアプリケーションを作成する方法については、次の記事に進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: Azure App Configuration を使用して ASP.NET Core アプリを作成する](quickstart-aspnet-core-app.md)

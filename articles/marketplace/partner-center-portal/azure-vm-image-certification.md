---
title: Azure 仮想マシン イメージの検証 - Azure Marketplace
description: 商業マーケットプレースで仮想マシン オファーをテストして送信する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: fd8f41f88b6184eee15477c460dc9d2e521d25e6
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144189"
---
# <a name="azure-virtual-machine-image-validation"></a>Azure 仮想マシン イメージの検証

この記事では、商業マーケットプレースで仮想マシン (VM) イメージをテストして送信し、Azure Marketplace の最新の公開要件を満たしていることを確認する方法について説明します。

VM オファーを送信する前に、次の手順を完了します。

- 一般化されたイメージを使用して Azure VM をデプロイします。
- 検証を実行します。

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>一般化されたイメージを使用して Azure VM をデプロイする

このセクションでは、新しい Azure VM リソースを作成するために、一般化された仮想ハード ディスク (VHD) イメージをデプロイする方法について説明します。 このプロセスでは、提供されている Azure Resource Manager テンプレートと Azure PowerShell スクリプトを使用します。

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを準備する

このセクションでは、ユーザー指定の仮想マシン (VM) イメージを作成してデプロイする方法について説明します。 これを行うには、Azure にデプロイされた仮想ハード ディスクからオペレーティング システムとデータ ディスクの VHD イメージを提供します。 次の手順では、一般化された VHD を使用して VM をデプロイします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. 一般化されたオペレーティング システム VHD とデータ ディスク VHD を Azure ストレージ アカウントにアップロードします。
3. ホーム ページで、 **[リソースの作成]** を選択し、「テンプレートのデプロイ」を検索して、 **[作成]** を選択します。
4. **[Build your own template in the editor]\(エディターで独自のテンプレートを作成する\)** を選択します。

    :::image type="content" source="media/vm/template-deployment.png" alt-text="テンプレートの選択を示します。":::

5. 次の JSON テンプレートをエディターに貼り付け、 **[保存]** を選択します。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. 表示されているカスタム デプロイのプロパティ ページのパラメーター値を指定します。

| ResourceGroupName | 既存の Azure リソース グループ名。 通常は、ご利用のキー コンテナーと同じ RG を使用します。 |
| --- | --- |
| TemplateFile | VHDtoImage.json ファイルの完全なパス名。 |
| userStorageAccountName | ストレージ アカウントの名前。 |
| dnsNameForPublicIP | パブリック IP の DNS 名。小文字にする必要があります。 |
| subscriptionId | Azure サブスクリプションの識別子。 |
| 場所 | リソース グループの標準的な Azure 地理的場所。 |
| vmName | 仮想マシンの名前。 |
| vhdUrl | 仮想ハード ディスクの Web アドレス。 |
| vmSize | 仮想マシン インスタンスのサイズ。 |
| publicIPAddressName | パブリック IP アドレスの名前。 |
| virtualNetworkName | 仮想ネットワークの名前。 |
| nicName | 仮想ネットワーク用のネットワーク インターフェイス カードの名前。 |
| adminUserName | 管理者アカウントのユーザー名。 |
| adminPassword | 管理者パスワード。 |
|

7. これらの値を指定した後、 **[購入]** を選択します。

Azure でデプロイが開始されます。 これにより、指定のストレージ アカウント パスに、指定されたアンマネージド VHD を含む新しい VM が作成されます。 Azure portal で進捗状況を追跡するには、ポータルの左側にある **[Virtual Machines]** を選択します。 VM が作成されると、状態は [開始中] から [実行中] に変わります。

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>第 2 世代 VM のデプロイの場合は、次のテンプレートを使用します。

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>PowerShell を使用して Azure VM をデプロイする

次のスクリプトをコピーして編集し、変数 `$storageaccount` と `$vhdUrl` の値を指定します。 それを実行して、汎用化された既存の VHD から Azure VM リソースを作成します。

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>検証を実行する

デプロイされたイメージに対して検証を実行する方法は 2 つあります。

### <a name="use-certification-test-tool-for-azure-certified"></a>Azure 認定用の認定テスト ツールを使用する

#### <a name="download-and-run-the-certification-test-tool"></a>認定テスト ツールをダウンロードして実行する

Azure 認定用の認定テスト ツールはローカルの Windows マシン上で動作しますが、そのテスト対象は Azure ベースの Windows と Linux VM です。 これにより、ユーザーの VM イメージが Microsoft Azure で使用できることと、VHD を準備するにあたってのガイダンスと要件が満たされていることが認定されます。

1. 次のリンクから、[Azure 認定用の認定テスト ツール](https://www.microsoft.com/download/details.aspx?id=44299)の最新版をダウンロードしてインストールします。
2. 認定ツールを開き、 **[Start New Test]\(新規テストの開始\)** を選択します。
3. [テスト情報] 画面で、実行する**テストの名前**を入力します。
4. VM のプラットフォームとして **[Windows Server]** または **[Linux]** のどちらかを選択します。 ここでのプラットフォームの選択は、以降のオプションに影響します。
5. 該当するデータベース サービスを VM で使用している場合、 **[Test for Azure SQL Database]\(Azure SQL Database のテスト\)** チェック ボックスをオンにします。

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>VM イメージに認定ツールを接続する

1. [SSH Authentication]\(SSH 認証\) モードを選択します:パスワード認証またはキー ファイル認証。
2. パスワード ベースの認証を使用する場合は、 **[VM の DNS 名]** 、 **[ユーザー名]** 、 **[パスワード]** の値を入力します。 既定の SSH ポート番号を変更することもできます。

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="VM のテスト情報の選択を示します。":::

3. キー ファイル ベースの認証を使用する場合は、VM の DNS 名、ユーザー名、秘密キーの場所の値を入力します。 パスフレーズを含めたり、既定の SSH ポート番号を変更したりすることもできます。
4. 完全修飾 VM DNS 名 (MyVMName.Cloudapp.net など) を入力します。
5. **[ユーザー名]** と **[パスワード]** を入力します。

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="VM ユーザー名とパスワードの選択を示します。":::

6. **[次へ]** を選択します。

#### <a name="run-a-certification-test"></a>認定テストを実行する

認定ツールで VM イメージのパラメーター値を指定したら、[接続テスト] を選択して VM への有効な接続を作成します。 接続の確認後、 **[次へ]** をクリックしてテストを開始します。 テストが完了すると、結果がテーブルに表示されます。 [Status]\(状態\) 列に、各テストについて Pass(合格)/Fail(不合格)/Warning(警告) が表示されます。 いずれかのテストが失敗した場合、イメージは認定 "されません"。 その場合は、要件と失敗のメッセージを確認して、提案された変更を行い、テストを再実行します。

自動テストが完了したら、 [Questionnaire]\(アンケート\) 画面の 2 つのタブ ( [General Assessment]\(全般的な評価\) と [Kernel Customization]\(カーネルのカスタマイズ\) ) で VM イメージに関する追加情報を入力し、 [Next]\(次へ\) を選択します。

最後の画面では、Linux VM イメージの SSH アクセスに関する情報や、例外を探している場合の不合格になった評価の説明などの詳細情報を指定できます。

最後に [Generate Report]\(レポートの生成\) を選択して、実行されたテスト ケースのテスト結果とログ ファイルを、アンケートに対する自分の回答と合わせてダウンロードします。 VHD と同じコンテナーに結果を保存します。

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>PowerShell を使用して自己テスト API を消費する方法

### <a name="on-linux-os"></a>Linux OS の場合

PowerShell で API を呼び出します。

1. Invoke-WebRequest コマンドを使用して API を呼び出します。
2. 次のコード例とスクリーン キャプチャに示すように、メソッドは Post、コンテンツの種類は JSON です。
3. 本文パラメーターを JSON 形式で指定します。

次の例は、API への PowerShell 呼び出しを示しています。

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>PowerShell での API の呼び出しの例を次に示します。

[![PowerShell で API を呼び出している画面の例。](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>前の例を使用し、JSON を取得して解析し、以下の詳細を取得することができます。

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>次のサンプル画面 (`$res.Content` を示します) は、JSON 形式でのテスト結果の詳細を示しています。

[![テスト結果の詳細を含む PowerShell で API を呼び出している画面の例。](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>オンラインの JSON ビューアー ([Code Beautify](https://codebeautify.org/jsonviewer) や [JSON Viewer](https://jsonformatter.org/json-viewer) など) に表示された JSON テスト結果の例を次に示します。

![オンラインの JSON ビューアーでのさらに多くのテスト結果。](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows OS の場合

PowerShell で API を呼び出します。

1. Invoke-WebRequest コマンドを使用して API を呼び出します。
2. 次のコード例とサンプル画面に示すように、メソッドは Post であり、コンテンツの種類は JSON です。
3. 本文パラメーターを JSON 形式で作成します。

次のコード サンプルは、API への PowerShell 呼び出しを示しています。

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

次のサンプル画面は、PowerShell で API を呼び出す例を示しています。

**SSH キーを使用**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="SSH キーを使用した PowerShell での API の呼び出し。":::

**パスワードを使用**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="パスワードを使用した PowerShell での API の呼び出し。":::

<br>前の例を使用し、JSON を取得して解析し、以下の詳細を取得することができます。

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>次の画面 (`$res.Content` を示します) は、JSON 形式でのテスト結果の詳細を示しています。

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="JSON 形式でのテスト結果の詳細。":::

<br>オンラインの JSON ビューアー ([Code Beautify](https://codebeautify.org/jsonviewer) や [JSON Viewer](https://jsonformatter.org/json-viewer) など) に表示されたテスト結果の例を次に示します。

![オンラインの JSON ビューアーでのテスト結果。](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>CURL を使用して Linux OS で自己テスト API を消費する方法

CURL で API を呼び出します。

1. API を呼び出すには curl コマンドを使用します。
2. 次のコード スニペットに示すように、メソッドは Post、コンテンツの種類は JSON です。

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>CURL を使用して API を呼び出す例を次に示します。

![CURL を使用して API を呼び出す例。](media/vm/use-curl-call-api.png)

<br>CURL の呼び出しからの JSON の結果を次に示します。

![CURL の呼び出しからの JSON の結果。](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>次のステップ

- 「[SAS URI に関する一般的な問題と解決策](common-sas-uri-issues.md)」を参照してください。

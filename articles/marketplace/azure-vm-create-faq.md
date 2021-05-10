---
title: Azure Marketplace の VM についての一般的な質問
description: Azure Marketplace で仮想マシンを作成するときによく発生する一般的な質問。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 2975d1f1558bc7f9e4a12c18882e43a163b97982
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593426"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Azure Marketplace の VM についての一般的な質問

これらのよく寄せられる質問 (FAQ) では、Azure Marketplace に仮想マシン (VM) オファーを作成するときに発生する可能性のある一般的な問題について説明します。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>VM で使用するよう仮想プライベート ネットワーク (VPN) を構成するにはどうすればよいですか?

Azure Resource Manager デプロイ モデルを使用している場合、3 つのオプションがあります。

- [Azure portal を使用してルートベースの VPN ゲートウェイを作成する](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Azure PowerShell を使用してルートベースの VPN ゲートウェイを作成する](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [CLI を使用してルートベースの VPN ゲートウェイを作成する](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure ベースの VM で Microsoft サーバー ソフトウェアを実行するための Microsoft サポート ポリシーはどのようなものですか?

詳細については 「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」を参照してください。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM で、スタートアップ タスクのカスタム スクリプト拡張機能を管理するにはどうすればよいですか?

Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用したカスタム スクリプト拡張機能の使用方法と、Windows システムでのトラブルシューティング手順の詳細については、「[Windows でのカスタムのスクリプト拡張機能](../virtual-machines/extensions/custom-script-windows.md)」を参照してください。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>32 ビットのアプリケーションやサービスは Azure Marketplace でサポートされていますか?

いいえ。 サポートされるオペレーティング システムと Azure VM の標準サービスはすべて 64 ビットです。 ほとんどの 64 ビット オペレーティング システムは、下位互換性のために 32 ビット バージョンのアプリケーションをサポートしていますが、VM ソリューションの一部として 32 ビット アプリケーションを使用することはサポートされておらず、非常にお勧めできません。 アプリケーションを 64 ビットのプロジェクトとして再作成してください。

詳細と例については、次の記事をご覧ください。

- [32 ビット アプリケーションの実行](/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 仮想マシンでの 32 ビット オペレーティング システムのサポート](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>エラー:VHD は、既にリソースとしてイメージ リポジトリに登録されています

VHD からイメージを作成しようとするたびに、Azure PowerShell で "VHD は、既にリソースとしてイメージ リポジトリに登録されています" というエラーが発生します。 これまでイメージを作成したことはなく、Azure でこの名前のイメージは見つかっていません。 解決するにはどうすればよいですか?

この問題は通常、ロックがある VHD から VM を作成した場合に発生します。 この VHD から割り当てられた VM がないことを確認し、操作を再試行してください。 それでもこの問題が解決しない場合は、サポート チケットを開いてください。 [パートナー センターのサポート](support.md)に関する記事を参照してください。

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>汎用化した VHD から VM を作成する方法

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを準備する

このセクションでは、ユーザー指定の仮想マシン (VM) イメージを作成してデプロイする方法について説明します。 これを行うには、Azure にデプロイされた仮想ハード ディスクからオペレーティング システムとデータ ディスクの VHD イメージを提供します。 次の手順では、一般化された VHD を使用して VM をデプロイします。

1. Azure portal にサインインします。
2. 一般化されたオペレーティング システム VHD とデータ ディスク VHD を Azure ストレージ アカウントにアップロードします。
3. ホーム ページで、 [リソースの作成] を選択し、「テンプレートのデプロイ」を検索して、 [作成] を選択します。
4. [Build your own template in the editor]\(エディターで独自のテンプレートを作成する\) を選択します。

   :::image type="content" source="media/vm/template-deployment.png" alt-text="テンプレートの選択肢を表示する":::

5. 次の JSON テンプレートをエディターに貼り付け、 [保存] を選択します。
 ```json
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
           }
       ]
   }
   ```


6. 表示されているカスタム デプロイのプロパティ ページのパラメーター値を指定します。

 **表 1**

| **ResourceGroupName** | **既存の Azure リソース グループ名。通常は、キー コンテナーと同じ RG を使用します。** |
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

7. これらの値を指定した後、 [購入] を選択します。

Azure でデプロイが開始されます。 これにより、指定のストレージ アカウント パスに、指定されたアンマネージド VHD を含む新しい VM が作成されます。 Azure portal で進捗状況を追跡するには、ポータルの左側にある [Virtual Machines] を選択します。 VM が作成されると、状態は [開始中] から [実行中] に変わります。

第 2 世代 VM のデプロイの場合は、次のテンプレートを使用します。
 ```json
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
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>PowerShell を使用して Azure VM をデプロイする

次のスクリプトをコピーして編集し、変数 `$storageaccount` と `$vhdUrl` の値を指定します。 それを実行して、汎用化された既存の VHD から Azure VM リソースを作成します。

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```

## <a name="how-do-i-test-a-hidden-preview-image"></a>非表示のプレビュー画像をテストするにはどうすればよいですか?

クイックスタート テンプレートを使用して、非表示のプレビュー画像をデプロイできます。
プレビュー イメージをデプロイするには、次の手順を実行します。 
1. [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) または [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) のそれぞれのクイックスタート テンプレートに移動し、[Azure へのデプロイ] を選択します。 これで Azure portal に移動します。
2. Azure portal で、[テンプレートの編集] を選択します。
3. JSON テンプレートで、imageReference を検索し、画像の publisherid、offerid、skuid、および version を更新します。 プレビュー画像をテストするには、offerid に "-PREVIEW" を追加します。
 ![image](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. [保存] をクリックします。
5. 残りの詳細を入力します。 確認と作成


## <a name="next-steps"></a>次の手順

- [VM 認定のトラブルシューティング](azure-vm-create-certification-faq.md)

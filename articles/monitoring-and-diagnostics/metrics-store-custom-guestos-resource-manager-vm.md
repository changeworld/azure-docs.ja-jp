---
title: Windows 仮想マシンについて Resource Manager テンプレートを使用してゲスト OS メトリックを Azure Monitor メトリック ストアに送信する
description: Windows 仮想マシンについて Resource Manager テンプレートを使用してゲスト OS メトリックを Azure Monitor メトリック ストアに送信する
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4ed911766a14dd35ea662326a5d50df11cf81698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984076"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Windows 仮想マシンについて Resource Manager テンプレートを使用してゲスト OS メトリックを Azure Monitor メトリック ストアに送信する

Azure Monitor [Windows Azure 診断拡張機能](azure-diagnostics.md) (WAD) を使用すると、仮想マシン、クラウド サービス、または Service Fabric クラスターの一部として、ゲスト オペレーティング システム (ゲスト OS) からメトリックとログを収集できます。  拡張機能により、以前のリンク先の記事に記載されている多くの場所にテレメトリを送信することができます。  

この記事では、Windows 仮想マシン用のゲスト OS のパフォーマンス メトリックを Azure Monitor データ ストアに送信するプロセスについて説明します。 WAD バージョン 1.11 以降、標準プラットフォーム メトリックが既に収集されている Azure Monitor メトリック ストアに、メトリックを直接書き込むことができます。 この場所にこれらを格納することで、プラットフォーム メトリックに対して使用できるのと同じアクションにアクセスできます。  アクションには、ほぼリアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなどの機能が含まれています。  これまで WAD 拡張機能は Azure Storage に書き込まれましたが、Azure Monitor のデータ ストアには書き込まれませんでした。   

Resource Manager テンプレートを初めて利用する場合は、[テンプレートのデプロイ](../azure-resource-manager/resource-group-overview.md)とその構造および構文についてご確認ください。  

## <a name="pre-requisites"></a>前提条件

- サブスクリプションを [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) に登録する必要があります 

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) がインストールされている必要があります。[Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) を使用することもできます 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor をデータ シンクとして設定する 
Azure 診断拡張機能では、"データ シンク" と呼ばれる機能を使って、メトリックとログがさまざまな場所にルーティングされます。  次の手順では、Resource Manager テンプレートと PowerShell を使用して、新しい "Azure Monitor" データ シンクを使って VM をデプロイする方法を説明します。 

## <a name="author-resource-manager-template"></a>Resource Manager テンプレートを作成する 
この例では、公開されているサンプル テンプレートを使用できます。 開始用テンプレートは https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows にあります 

- **Azuredeploy.json** は、仮想マシンのデプロイ用に事前構成された Resource Manager テンプレートです。 

- **Azuredeploy.parameters.json** は、VM 用に設定するユーザー名やパスワードなどの情報が格納されているパラメーター ファイルです。 デプロイ中、このファイルで設定されているパラメーターが、Resource Manager テンプレートによって使用されます。 

両方のファイルをダウンロードし、ローカルに保存します。 

###  <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.json ファイルを更新する
*azuredeploy.parameters.json* ファイルを開きます 

1. VM の *adminUsername* と *adminPassword* について値を入力します。 これらのパラメーターは VM へのリモート アクセスに使用されます。 お使いの VM がハイジャックされないように、このテンプレートのパラメーターは使用しないでください。 ボットによってスキャンされるのは、インターネット上にあるパブリック Github リポジトリのユーザー名やパスワードです。 多くの場合、このようなボットは、これらの既定値を使用して VM をテストします。  

1. VM について一意の dnsname を作成します。  

### <a name="modify-azuredeployjson"></a>azuredeploy.json を変更する

*azuredeploy.json* ファイルを開きます 

ストレージ アカウント ID を、テンプレートの **variables** セクションで、**storageAccountName** エントリの後ろに追加します。  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

このマネージド サービス ID (MSI) 拡張機能を、"resources" セクションの上部でテンプレートに追加します。  この拡張機能により、Azure Monitor が、出力されているメトリックを確実に受け取るようになります。  

```json
//Find this code 
"resources": [
// Add this code directly below
    { 
        "type": "Microsoft.Compute/virtualMachines/extensions", 
        "name": "WADExtensionSetup", 
        "apiVersion": "2015-05-01-preview", 
        "location": "[resourceGroup().location]", 
        "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ], 
        "properties": { 
            "publisher": "Microsoft.ManagedIdentity", 
            "type": "ManagedIdentityExtensionForWindows", 
            "typeHandlerVersion": "1.0", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "port": 50342 
            } 
        } 
    }, 
```

"identity" 構成を VM リソースに追加して、システム ID が MSI 拡張機能に確実に割り当てられるようにします。 この手順により、VM が自身に関するゲスト メトリックを Azure Monitor に確実に出力できます 

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{ 
    "apiVersion": "2017-03-30", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[variables('vmName')]", 
    "location": "[resourceGroup().location]", 
    // add these 3 lines below
    "identity": {  
    "type": "SystemAssigned" 
    }, 
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {   
    ...
```

次の構成を追加して、Windows 仮想マシンで診断拡張機能を有効にします。  シンプルな Resource Manager ベースの仮想マシンについては、拡張機能の構成を、仮想マシンのリソース配列に追加できます。 このセクションで後ほど出てくる "sinks": "AzMonSink" 行と、対応する "SinksConfig" によって、拡張機能でメトリックを Azure Monitor に直接出力できます。 必要に応じて、パフォーマンス カウンターを自由に追加/削除できます。  


```json
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
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]" 
    } 
} 
}, 
//Start of section to add 
"resources": [        
{ 
            "type": "extensions", 
            "name": "Microsoft.Insights.VMDiagnosticsSettings", 
            "apiVersion": "2015-05-01-preview", 
            "location": "[resourceGroup().location]", 
            "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" 
            ], 
            "properties": { 
            "publisher": "Microsoft.Azure.Diagnostics", 
            "type": "IaaSDiagnostics", 
            "typeHandlerVersion": "1.4", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "WadCfg": { 
                "DiagnosticMonitorConfiguration": { 
    "overallQuotaInMB": 4096, 
    "DiagnosticInfrastructureLogs": { 
                    "scheduledTransferLogLevelFilter": "Error" 
        }, 
                    "Directories": { 
                    "scheduledTransferPeriod": "PT1M", 
    "IISLogs": { 
                        "containerName": "wad-iis-logfiles" 
                    }, 
                    "FailedRequestLogs": { 
                        "containerName": "wad-failedrequestlogs" 
                    } 
                    }, 
                    "PerformanceCounters": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "sinks": "AzMonSink", 
                    "PerformanceCounterConfiguration": [ 
                        { 
                        "counterSpecifier": "\\Memory\\Available Bytes", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\Committed Bytes", 
                        "sampleRate": "PT15S" 
                        } 
                    ] 
                    }, 
                    "WindowsEventLog": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "DataSource": [ 
                        { 
                        "name": "Application!*" 
                        } 
                    ] 
                    }, 
                    "Logs": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "scheduledTransferLogLevelFilter": "Error" 
                    } 
                }, 
                "SinksConfig": { 
                    "Sink": [ 
                    { 
                        "name" : "AzMonSink", 
                        "AzureMonitor" : {} 
                    } 
                    ] 
                } 
                }, 
                "StorageAccount": "[variables('storageAccountName')]" 
            }, 
            "protectedSettings": { 
                "storageAccountName": "[variables('storageAccountName')]", 
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]", 
                "storageAccountEndPoint": "https://core.windows.net/" 
            } 
            } 
        } 
        ] 
//End of section to add 
```


ファイルを保存して閉じます 
 

## <a name="deploy-the-resource-manager-template"></a>Resource Manager テンプレートをデプロイする 

> [!NOTE]
> Azure 診断拡張機能バージョン 1.5 以上を実行する必要があります。また、Resource Manager テンプレートで "autoUpgradeMinorVersion": プロパティが "true" に設定されていなければなりません。  その後、Azure によって VM の開始時に適切な拡張機能が読み込まれます。 ご自身のテンプレートにこれらの設定がない場合は、テンプレートを変更して再デプロイします。 


Resource Manager テンプレートをデプロイするために、Azure PowerShell を利用します。  

1. PowerShell を起動する 
1. `Login-AzureRmAccount` を使用して、Azure にログインします
1. `Get-AzureRmSubscription` を使用して、サブスクリプションのご自身の一覧を取得します
1. 仮想マシンを作成/更新するサブスクリプションを設定します 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. デプロイされている VM の新しいリソース グループを作成し、次のコマンドを実行します 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > 必ず、[カスタム メトリックに対して有効になっている Azure リージョンを使用してください](metrics-custom-overview.md)。 
 
1. 以下のコマンドを実行して、VM をデプロイします  
   > [!NOTE] 
   > 既存の VM を更新するには、*-Mode Incremental* を以下のコマンドの末尾に追加するだけです。 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. デプロイが成功したら、Azure portal で VM が見つかるはずです。この VM によって、メトリックが Azure Monitor に出力されます。 

   > [!NOTE] 
   > 選択した vmSkuSize に関連するエラーが発生することがあります。 この場合は、azuredeploy.json ファイルに戻り、vmSkuSize パラメーターの既定値を更新します。 ここでは "Standard_DS1_v2" を試してみることをお勧めします。 

## <a name="chart-your-metrics"></a>メトリックをグラフ化する 

1. Azure portal にサインインします 

1. 左側のメニューで **[モニター]** をクリックします 

1. [モニター] ページで **[メトリック]** をクリックします。 

   ![メトリックス ページ](./media/metrics-store-custom-rest-api/metrics.png) 

1. 集計の期間を **[過去 30 分間]** に変更します。  

1. リソースのドロップダウンで、作成した VM を選択します。 テンプレートの名前を変更しなかった場合は、*SimpleWinVM2* のはずです。  

1. 名前空間のドロップダウンで、**[azure.vm.windows.guest]** を選択します 

1. メトリックのドロップダウンで、**[Memory\%Committed Bytes in Use]** を選択します。  
 

## <a name="next-steps"></a>次の手順
- [カスタム メトリック](metrics-custom-overview.md)の詳細を確認します。
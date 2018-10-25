---
title: Windows 仮想マシン スケール セットについて Resource Manager テンプレートを使用してゲスト OS メトリックを Azure Monitor メトリック ストアに送信する
description: Windows 仮想マシン スケール セットについて Resource Manager テンプレートを使用してゲスト OS メトリックを Azure Monitor メトリック ストアに送信する
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 7b600bd699ce7f9e4a6c7cba1a41b6bdece16bf0
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343727"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Windows 仮想マシン スケール セットについて Resource Manager テンプレートを使用してゲスト OS メトリックを Azure Monitor メトリック ストアに送信する

Azure Monitor [Windows Azure 診断拡張機能](azure-diagnostics.md) (WAD) を使用すると、仮想マシン、クラウド サービス、または Service Fabric クラスターの一部として、ゲスト オペレーティング システム (ゲスト OS) からメトリックとログを収集できます。  拡張機能により、以前にリンクされた記事に記載されている多くの場所にテレメトリを送信できます。  

この記事では、Windows 仮想マシン スケール セット用のゲスト OS のパフォーマンス メトリックを Azure Monitor データ ストアに送信するプロセスについて説明します。 WAD バージョン 1.11 以降、標準プラットフォーム メトリックが既に収集されている Azure Monitor メトリック ストアに、メトリックを直接書き込むことができます。 この場所にこれらを格納することで、プラットフォーム メトリックに対して使用できるのと同じアクションにアクセスできます。  アクションには、ほぼリアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなどの機能があります。  これまで、WAD 拡張機能は Azure Storage に書き込みましたが、Azure Monitor データ ストアには書き込みませんでした。  

Resource Manager テンプレートを初めて利用する場合は、[テンプレートのデプロイ](../azure-resource-manager/resource-group-overview.md)とその構造および構文についてご確認ください。  

## <a name="pre-requisites"></a>前提条件

- サブスクリプションを [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) に登録する必要があります 

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) がインストールされている必要があります。[Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) を使用することもできます 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor をデータ シンクとして設定する 
Azure 診断拡張機能では、"データ シンク" と呼ばれる機能を使って、メトリックとログがさまざまな場所にルーティングされます。  次の手順では、Resource Manager テンプレートと PowerShell を使用して、新しい "Azure Monitor" データ シンクを使って VM をデプロイする方法を説明します。 

## <a name="author-resource-manager-template"></a>Resource Manager テンプレートを作成する 
この例では、公開されているサンプル テンプレートを使用できます。 開始用テンプレートは https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale にあります  

- **Azuredeploy.json** は、仮想マシン スケール セットのデプロイ用に事前構成された Resource Manager テンプレートです

- **Azuredeploy.parameters.json** は、VM 用に設定するユーザー名やパスワードなどの情報が格納されているパラメーター ファイルです。 デプロイ中、このファイルで設定されているパラメーターが、Resource Manager テンプレートによって使用されます。 

両方のファイルをダウンロードし、ローカルに保存します。 

###  <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.json ファイルを更新する
*azuredeploy.parameters.json* ファイルを開きます 

- デプロイする **vmSKU** を指定します (Standard_D2_v3 を推奨) 
- 仮想マシン スケール セットに使用する **windowsOSVersion** を指定します (2016-Datacenter を推奨) 
- **vmssName** プロパティを使用して、デプロイする仮想マシン スケール セット リソースに名前を付けます  (*VMSS-WAD-TEST* など)。    
- **instanceCount** プロパティを使用して、仮想マシン スケール セットで実行する VM の数を指定します
- 仮想マシン スケール セットの **adminUsername** と **adminPassword** について値を入力します。 これらのパラメーターはスケール セット内の VM へのリモート アクセスに使用されます。 これらのパラメーターは VM へのリモート アクセスに使用されます。 お使いの VM がハイジャックされないように、このテンプレートのパラメーターは使用しないでください。 ボットによってスキャンされるのは、インターネット上にあるパブリック Github リポジトリのユーザー名やパスワードです。 多くの場合、このようなボットは、これらの既定値を使用して VM をテストします。 


###  <a name="modify-azuredeployjson"></a>azuredeploy.json を変更する
*azuredeploy.json* ファイルを開きます 

Resource Manager テンプレートでストレージ アカウント情報を保持するための変数を追加します。 診断拡張機能のインストールの一環として、引き続きストレージ アカウントを指定する必要があります。 診断構成ファイルで指定されたログやパフォーマンス カウンターは、Azure Monitor メトリック ストアに送信されるだけでなく、指定したストレージ アカウントに書き込まれます。 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
resources セクションで仮想マシン スケール セットの定義を見つけて、"identity" セクションを構成に追加します。 これにより、Azure によってシステム ID が割り当てられます。 この手順により、スケール セットの VM が自身に関するゲスト メトリックを Azure Monitor に確実に出力できます。  

```json
  { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
 ```

仮想マシン スケール セット リソースで、**virtualMachineProfile** セクションを見つけます。 拡張機能を管理するための **extensionsProfile** と呼ばれる新しいプロファイルを追加します。  


**extensionProfile** で、**VMSS-WAD-extension セクション**によって示されているように、新しい拡張機能をテンプレートに追加します。  このセクションは、出力されたメトリックが Azure Monitor で確実に受け入れられるようにする、Azure リソース拡張機能用のマネージド ID です。 **name** フィールドには任意の名前を含めることができます。 

MSI 拡張機能の次のコードはまた、診断拡張機能および構成を拡張機能リソースとして仮想マシン スケール セット リソースに追加します。 必要に応じて、パフォーマンス カウンターを自由に追加/削除できます。 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identites for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


ストレージ アカウントに対して dependsOn を追加し、正しい順序で作成されるようにします。 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

ストレージ アカウントを作成します (テンプレートでまだ作成されていない場合)。  
```json
"resources": [
  // add this code    
  {
     "type": "Microsoft.Storage/storageAccounts",
     "name": "[variables('storageAccountName')]",
     "apiVersion": "2015-05-01-preview",
     "location": "[resourceGroup().location]",
     "properties": {
       "accountType": "Standard_LRS"
      }
  },
  // end added code
  { 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

両方のファイルを保存して閉じます。 

## <a name="deploy-the-resource-manager-template"></a>Resource Manager テンプレートをデプロイする 

> [!NOTE]
> Azure 診断拡張機能バージョン 1.5 以上を実行する必要があります。また、Resource Manager テンプレートで "autoUpgradeMinorVersion": プロパティが *true* に設定されていなければなりません。  その後、Azure によって VM の開始時に適切な拡張機能が読み込まれます。 ご自身のテンプレートにこれらの設定がない場合は、テンプレートを変更して再デプロイします。 


Resource Manager テンプレートをデプロイするために、Azure PowerShell を利用します。  

1. PowerShell を起動する 
1. `Login-AzureRmAccount` を使用して Azure にサインインします
1. `Get-AzureRmSubscription` を使用して、サブスクリプションのご自身の一覧を取得します
1. 仮想マシンを作成/更新するサブスクリプションを設定します 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. デプロイされている VM の新しいリソース グループを作成し、次のコマンドを実行します 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > 必ず、カスタム メトリックに対して有効になっている Azure リージョンを使用してください。 以下を参照してください。 
 
1. 以下のコマンドを実行して、VM をデプロイします  
   > [!NOTE] 
   > 既存のスケール セットを更新するには、*-Mode Incremental* を以下のコマンドの末尾に追加するだけです。 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. デプロイが成功したら、Azure portal で仮想マシン スケール セットが見つかるはずです。これによって、メトリックが Azure Monitor に出力されます。 

   > [!NOTE] 
   > 選択した vmSkuSize に関連するエラーが発生することがあります。 この場合は、azuredeploy.json ファイルに戻り、vmSkuSize パラメーターの既定値を更新します。 ここでは "Standard_DS1_v2" を試してみることをお勧めします。 


## <a name="chart-your-metrics"></a>メトリックをグラフ化する 

1. Azure ポータルにサインインします。 

1. 左側のメニューで、**[モニター]** をクリックします 

1. [モニター] ページで、**[メトリック]** をクリックします。 

   ![メトリック ページ](./media/metrics-store-custom-rest-api/metrics.png) 

1. 集計の期間を **[過去 30 分間]** に変更します。  

1. 次のリソース ドロップ ダウン リストで、作成した仮想マシン スケール セットを選択します。  

1. 名前空間のドロップダウンで、**[azure.vm.windows.guest]** を選択します 

1. メトリックのドロップダウンで、**[Memory\%Committed Bytes in Use]** を選択します。  

その後、このメトリックのディメンションを使用して、スケール セットの特定の VM に対してこのメトリックをグラフ化するか、スケール セットの各 VM をプロットできます。 



## <a name="next-steps"></a>次の手順
- [カスタム メトリック](metrics-custom-overview.md)の詳細を確認します。


---
title: Azure PowerShell を使用して Log Analytics ワークスペースを作成する | Microsoft Docs
description: Azure PowerShell で Log Analytics ワークスペースを作成して、クラウドおよびオンプレミス環境から管理ソリューションを有効にし、データを収集できるようにする方法を学習します。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: ba5dadcdec6337c23cb0510298401296282c9047
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191004"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Azure PowerShell を使用して Log Analytics ワークスペースを作成する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure PowerShell モジュールを使用して Log Analytics ワークスペースを Azure にデプロイする方法を説明します。このワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた独自の環境です。  次のソースからデータを収集する場合は、この記事で説明する手順を実行する必要があります。

* サブスクリプション内の Azure リソース  
* System Center Operations Manager によって監視されているオンプレミスのコンピューター  
* System Center Configuration Manager のデバイス コレクション  
* Azure ストレージからの診断またはログ データ  
 
環境内の Azure VM、Windows VM、Linux VM などの他のソースについては、次のトピックを参照してください。

* [Azure Virtual Machines に関するデータの収集](../../azure-monitor/learn/quick-collect-azurevm.md)
* [ハイブリッド Linux コンピューターからのデータの収集](../../azure-monitor/learn/quick-collect-linux-computer.md)
* [ハイブリッド Windows コンピューターからのデータの収集](quick-collect-windows-computer.md)

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-workspace"></a>ワークスペースの作成
[New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) を使用してワークスペースを作成します。 次の例では、ローカル コンピューターから Resource Manager テンプレートを使用して、*eastus* の場所にあるリソース グループ *Lab* に *TestWorkspace* という名前のワークスペースを作成します。 JSON テンプレートは、ワークスペースの名前の入力だけをユーザーに求め、環境の標準構成として使用される可能性のある他のパラメーターには既定値を指定するように構成されています。 

以下のパラメーターには、既定値が設定されます。

* location - 既定値は米国東部
* sku - 既定値は、2018 年 4 月の価格モデルでリリースされた新しい 1 GB あたりの価格レベル

>[!WARNING]
>新しい 2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは **PerGB2018** のみです。 
>

### <a name="create-and-deploy-template"></a>テンプレートの作成とデプロイ

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 要件に合わせてテンプレートを編集します。  どのプロパティと値がサポートされているかを調べるには、[Microsoft.OperationalInsights/workspaces テンプレート](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces)のリファレンスを参照してください。 
3. このファイルを **deploylaworkspacetemplate.json** としてローカル フォルダーに保存します。   
4. これでこのテンプレートをデプロイする準備が整いました。 テンプレートがあるフォルダーから以下のコマンドを使用します。

    ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

デプロイが完了するまでに数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

![デプロイ完了時の結果の例](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>次の手順
使用できるワークスペースが用意されたので、管理テレメトリの収集の構成、ログ検索の実行によるデータの分析、管理ソリューションの追加による追加データと分析的な考察の提供を行うことができます。  

* Microsoft Azure 診断または Azure ストレージを使用して Azure リソースからデータを収集できるようにするには、「[Log Analytics で Azure サービスのログとメトリックを使用できるように収集する](../../azure-monitor/platform/collect-azure-metrics-logs.md)」を参照してください。  
* Operations Manager 管理グループに報告するエージェントからデータを収集して Log Analytics ワークスペースに格納するには、[データ ソースとして System Center Operations Manager を追加](../../azure-monitor/platform/om-agents.md)します。  
* 階層内のコレクションのメンバーであるコンピュータをインポートするには、[構成マネージャー](../../azure-monitor/platform/collect-sccm.md)に接続します。  
* 使用可能な[管理ソリューション](../../azure-monitor/insights/solutions.md)と、ソリューションをワークスペースに対して追加または削除する方法を確認します。

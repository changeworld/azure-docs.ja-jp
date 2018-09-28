---
title: Azure Kubernetes Service クラスターの監視を停止する方法 | Microsoft Docs
description: この記事では、コンテナー用の Azure Monitor で Azure AKS クラスターの監視を中断する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: magoedte
ms.openlocfilehash: 2b989fbebe237e4e3746ef2f237193587173dfe4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963408"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-azure-monitor-for-containers"></a>コンテナー用の Azure Monitor で Azure Kubernetes Service (AKS) の監視を停止する方法

AKS クラスターの監視を有効にした後、監視が必要なくなった場合は、提供されている Azure Resource Manager テンプレートと PowerShell コマンドレット **New-AzureRmResourceGroupDeployment** または Azure CLI を使用して*オプト アウト* できます。 1 つの JSON テンプレートでは、*オプト アウト*する構成が指定されます。もう 1 つのテンプレートには、AKS クラスター リソース ID およびリソース グループ (クラスターがデプロイされる) を指定するために構成するパラメーター値が含まれています。 

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="create-template"></a>テンプレートの作成

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. このファイルを **OptOutTemplate.json** という名前でローカル フォルダーに保存します。
3. 以下の JSON 構文をファイルに貼り付けます。

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. **aksResourceId** と **aksResourceLocation** の値を、選択したクラスターの **[プロパティ]** ページに表示されている AKS クラスターの値を使用して編集します。

    ![コンテナーのプロパティ ページ](./media/monitoring-container-health/container-properties-page.png)

    **[プロパティ]** ページの **[ワークスペース リソース ID]** もコピーします。 この値は、後で Log Analytics ワークスペースを削除する場合に必要になります。 このプロセスでは、Log Analytics ワークスペースの削除は行いません。 

5. このファイルを **OptOutParam.json** という名前でローカル フォルダーに保存します。
6. これでこのテンプレートをデプロイする準備が整いました。 

## <a name="remove-the-solution-using-azure-cli"></a>Azure CLI を使用してソリューションを削除する
Linux 上で Azure CLI を使用して次のコマンドを実行してソリューションを削除し、AKS クラスターの構成をクリーンアップします。

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

```azurecli
ProvisioningState       : Succeeded
```

## <a name="remove-the-solution-using-powershell"></a>PowerShell を使用してソリューションを削除する

テンプレートが保存されているフォルダーで次の PowerShell コマンドを実行してソリューションを削除し、AKS クラスターから構成をクリーンアップします。    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

```powershell
ProvisioningState       : Succeeded
```

クラスターの監視をサポートするためだけにワークスペースが作成され、不要になった場合、手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、「[Azure Portal で Azure Log Analytics ワークスペースを削除する](../log-analytics/log-analytics-manage-del-workspace.md)」を参照してください。 上記の手順 4 でコピーした、**ワークスペース リソース ID** は、この後の手順で必要になります。 


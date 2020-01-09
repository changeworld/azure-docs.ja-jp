---
title: お使いの Azure Red Hat OpenShift クラスターの監視を停止する方法 | Microsoft Docs
description: この記事では、コンテナー用の Azure Monitor でお使いの Azure Red Hat OpenShift クラスターの監視を停止する方法について説明します。
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: e726d2d8254598869f1c6305421c674c870e3d31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404287"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>コンテナー用の Azure Monitor でお使いの Azure Red Hat OpenShift クラスターの監視を停止する方法

一度有効にしたお使いの Azure Red Hat OpenShift クラスターの監視を、監視する必要がなくなった場合に停止することができます。 この記事では、提供されている Azure Resource Manager テンプレートで、これを行う方法を示します。  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

リソース グループ内のソリューション リソースの一貫した反復的な削除をサポートするため、2 つの Azure Resource Manager テンプレートが提供されています。 1 つは監視を停止する構成を指定する JSON テンプレートで、もう 1 つは、クラスターがデプロイされている OpenShift クラスターのリソース ID と Azure リージョンを指定するために構成するパラメーター値が含まれるものです。 

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.65 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

### <a name="create-template"></a>テンプレートの作成

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
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
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. **aroResourceId** と **aroResourceLocation** の値を、選択したクラスターの **[プロパティ]** ページに表示されている OpenShift クラスターの値を使用して編集します。

    ![コンテナーのプロパティ ページ](media/container-insights-optout-openshift/cluster-properties-page.png)

5. このファイルを **OptOutParam.json** という名前でローカル フォルダーに保存します。

6. これでこのテンプレートをデプロイする準備が整いました。 

### <a name="remove-the-solution-using-azure-cli"></a>Azure CLI を使用してソリューションを削除する

Linux 上で Azure CLI を使用して次のコマンドを実行してソリューションを削除し、お使いのクラスターの構成をクリーンアップします。

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>PowerShell を使用してソリューションを削除する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

テンプレートが含まれているフォルダーで次の PowerShell コマンドを実行してソリューションを削除し、お使いのクラスターから構成をクリーンアップします。    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>次のステップ

クラスターの監視をサポートするためだけにワークスペースが作成され、不要になった場合、手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、[Azure Log Analytics ワークスペースの削除](../../log-analytics/log-analytics-manage-del-workspace.md)に関するページを参照してください。 

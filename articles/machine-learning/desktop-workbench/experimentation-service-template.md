---
title: Azure Resource Manager テンプレートを使用して Azure Machine Learning 実験を作成する | Microsoft Docs
description: この記事では、Azure Resource Manager テンプレートを使って Azure Machine Learning 実験アカウントを作成する例を示します。
services: machine-learning
author: hning86
ms.author: haining
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 7938eaa0e06c9a33034a7388d02845d60967774e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42146634"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Azure Machine Learning 実験サービスを構成する

## <a name="overview"></a>概要
Azure Machine Learning 実験サービスのアカウント、ワークスペース、プロジェクトは、Azure リソースです。 そのため、Resource Manager テンプレートを使ってデプロイできます。 Resource Manager テンプレートとは、ソリューションに対してデプロイが必要なリソースを定義した JSON ファイルのことをいいます。 Azure ソリューションのデプロイと管理に関する概念について理解を深めるには、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」を参照してください。

## <a name="deploy-a-template"></a>テンプレートのデプロイ
Azure コマンド ライン インターフェイスまたは Azure Portal を使ってテンプレートをデプロイするのに必要な手順は 2 つだけです。

### <a name="deploy-a-template-from-the-command-line"></a>コマンドラインからテンプレートをデプロイする
コマンド ライン インターフェイスを使うと、1 つのコマンドで既存のリソース グループにテンプレートをデプロイできます。
テンプレートの作成については次をご覧ください。

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Azure Portal からテンプレートをデプロイする
テンプレートの作成とデプロイには、Azure Portal を使うこともできます。 次のようにします。

1. [Azure Portal](https://portal.azure.com)に移動します。
2. **[すべてのサービス]** を選び、「テンプレート」を検索します。
3. **[テンプレート]** を選びます。
4. **[+ 追加]** をクリックして、テンプレートの情報をコピーします。 
5. ステップ 4 で作成したテンプレートを選び、**[デプロイ]** をクリックします。


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Azure Portal で既存の Azure リソースからテンプレートを作成する
利用できる Azure Machine Learning 実験アカウントが既にある場合は、[Azure Portal](https://portal.azure.com) でそのリソースからテンプレートを生成できます。 

1. [Azure Portal](https://portal.azure.com) で Azure 実験アカウントに移動します。
2. **[設定]** で **[Automation スクリプト]** をクリックします。
3. テンプレートをダウンロードします。 

または、テンプレート ファイルを手動で編集することもできます。 テンプレート ファイルとパラメーター ファイルの例については以下をご覧ください。 

### <a name="template-file-example"></a>テンプレート ファイルの例
次の内容で "template-file.json" という名前のファイルを作成します。 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>parameters 
次の内容でファイルを作成し、<parameters.json> として保存します。 

変更可能な値が 3 つあります。 
* accountName: 実験アカウントの名前。
* location: サポートされている Azure リージョンのいずれか。
* storageAccountSku: Azure ML は Standard ストレージのみをサポートし、Premium はサポートしません。 ストレージについて詳しくは、「[Microsoft Azure Storage の概要](https://docs.microsoft.com/azure/storage/common/storage-introduction)」をご覧ください。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>次の手順
* [Azure Machine Learning を作成およびインストールする](../service/quickstart-installation.md)

---
title: Azure Resource Manager テンプレートを使用して Automation アカウントを作成する | Microsoft Docs
description: この記事では、Azure Resource Manager テンプレートを使用して、Azure Automation アカウントを作成する方法について説明します。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2020
ms.openlocfilehash: 1418b26a2a498c43ff61f42b2761c59cbca5d0f4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837146"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Automation アカウントを作成する

[Azure Resource Manager テンプレート](../azure-resource-manager/templates/template-syntax.md)を使用して、リソース グループに Azure Automation アカウントを作成することができます。 この記事では、以下のことを行うサンプル テンプレートについて説明します。

* Azure Monitor Log Analytics ワークスペースの作成を自動化します。
* Azure Automation アカウントの作成を自動化します。
* Automation アカウントを Log Analytics ワークスペースにリンクします。

このテンプレートでは、Azure または Azure 以外の仮想マシンの有効化は自動化されません。 

>[!NOTE]
>Azure Resource Manager テンプレートを使用する場合、Automation の実行アカウントの作成はサポートされていません。 ポータルからまたは PowerShell を使用して実行アカウントを手動で作成するには、[実行アカウントの管理](manage-runas-account.md)に関するページを参照してください。

これらの手順を完了したら、Runbook ジョブの状態とジョブ ストリームを、リンクされた Log Analytics ワークスペースに送信するために、Automation アカウントの[診断設定を構成する](automation-manage-send-joblogs-log-analytics.md)必要があります。 

## <a name="api-versions"></a>API のバージョン

次の表は、この例で使用されているリソースの API バージョンの一覧です。

| リソース | リソースの種類 | API バージョン |
|:---|:---|:---|
| ワークスペース | workspaces | 2017-03-15-preview |
| Automation アカウント | automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>テンプレートを使用する前に

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell Az モジュールが必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。 PowerShell では、デプロイに [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) が使用されます

Azure CLI をローカルにインストールして使用する場合、この記事ではバージョン 2.1.0 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 Azure CLI では、このデプロイに [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) が使用されます。 

JSON テンプレートは、次のことを求めるように構成されています。

* ワークスペースの名前。
* ワークスペースを作成するリージョン。
* Automation アカウントの名前。
* アカウントを作成するリージョン。

テンプレートの次のパラメーターには、Log Analytics ワークスペースの既定値が設定されます。

* *sku* の既定値は、2018 年 4 月の価格モデルでリリースされた 1 GB あたりの価格レベルです。
* *dataRetention* の既定値は 30 日です。
* *capacityReservationLevel* の既定値は 100 GB です。

>[!WARNING]
>2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは *PerGB2018* のみです。
>

JSON テンプレートでは、環境で標準構成として使用される可能性のある他のパラメーターの既定値を指定します。 このテンプレートは、組織内の共有アクセス用に Azure ストレージ アカウントに格納できます。 テンプレートの操作について詳しくは、[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)に関するページを参照してください。

Azure Automation と Azure Monitor を初めて使用する場合は、次の構成の詳細を理解しておくことが重要です。 これは、新しい Automation アカウントにリンクされた Log Analytics ワークスペースを作成、構成、および使用するときに、エラーを回避するのに役立ちます。 

* [追加の詳細](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)を確認し、アクセス制御モード、価格レベル、保有期間、容量予約レベルなどのワークスペースの構成オプションについて十分に理解します。

* [ワークスペース マッピング](how-to/region-mappings.md)を確認し、サポートされているリージョンを、インラインまたはパラメーター ファイルで指定します。 サブスクリプション内で Log Analytics ワークスペースと Automation アカウントをリンクすることは、特定のリージョンでのみサポートされています。

* Azure Monitor ログを初めて使用し、ワークスペースをまだデプロイしていない場合は、[ワークスペースの設計ガイダンス](../azure-monitor/platform/design-logs-deployment.md)を確認してください。 これは、アクセスの制御について学習し、組織に推奨される設計の実装戦略を理解するのに役立ちます。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
        "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
       ]
      }
     ]
    }
    ```

2. 要件に合わせてテンプレートを編集します。 パラメーターをインライン値として渡す代わりに、[Resource Manager パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を作成することを検討してください。

3. このファイルを deployAzAutomationAccttemplate.json という名前でローカル フォルダーに保存します。

4. これでこのテンプレートをデプロイする準備が整いました。 PowerShell または Azure CLI を使用できます。 ワークスペースと Automation アカウント名の入力を求められたら、すべての Azure サブスクリプションでグローバルに一意の名前を指定します。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    デプロイが完了するまでに数分かかる場合があります。 完了すると、結果を含む次のようなメッセージが表示されます。

    ![デプロイ完了時の結果の例](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>次のステップ

Runbook ジョブの状態とジョブ ストリームを、リンクされた Log Analytics ワークスペースに転送するには、「[Azure Monitor ログに Azure Automation のジョブ データを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。 これにより、Azure PowerShell コマンドを使用して Automation アカウントの診断設定が構成され、分析のためにログをワークスペースに送信するための統合が完了します。 

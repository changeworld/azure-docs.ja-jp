---
title: Azure Resource Manager テンプレートを使用して Update Management をオンボードする | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、Azure Automation Update Management ソリューションをオンボードすることができます。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: e69f3d7350d0da9f364983eae0935532b576bd76
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411472"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Update Management ソリューションをオンボードする

[Azure Resource Manager テンプレート](../azure-resource-manager/templates/template-syntax.md)を使用して、リソース グループ内の Azure Automation Update Management ソリューションを有効にすることができます。 この記事では、以下のことを自動化するサンプル テンプレートについて説明します。

* Azure Monitor Log Analytics ワークスペースの作成。
* Azure Automation アカウントの作成。
* まだリンクされていない場合は、Automation アカウントを Log Analytics ワークスペースにリンクする。
* Azure Automation Update Management ソリューションをオンボードする

このテンプレートでは、1 台または複数台の Azure や Azure 以外の VM のオンボードは自動化されません。

サブスクリプション内のサポートされているリージョンに Log Analytics ワークスペースと Automation アカウントを既にデプロイしており、それらがリンクされておらず、ワークスペースにまだ Update Management ソリューションがデプロイされていない場合、このテンプレートを使用することで、リンクが正常に作成され、Update Management ソリューションがデプロイされます。 

## <a name="api-versions"></a>API のバージョン

次の表は、この例で使用されているリソースの API バージョンの一覧です。

| リソース | リソースの種類 | API バージョン |
|:---|:---|:---|
| ワークスペース | workspaces | 2017-03-15-preview |
| Automation アカウント | automation | 2015-10-31 | 
| 解決策 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>テンプレートを使用する前に

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell Az モジュールが必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。 Azure PowerShell では、デプロイに [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) が使用されます。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.1.0 以降を実行していることが要件となります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 Azure CLI では、このデプロイに [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) が使用されます。 

JSON テンプレートは、次のことを求めるように構成されています。

* ワークスペースの名前
* ワークスペースを作成するリージョン
* Automation アカウントの名前
* アカウントを作成するリージョン

JSON テンプレートでは、環境で標準構成として使用される可能性のある他のパラメーターの既定値を指定します。 このテンプレートは、組織内の共有アクセス用に Azure ストレージ アカウントに格納できます。 テンプレートの操作について詳しくは、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)」を参照してください。

テンプレートの次のパラメーターには、Log Analytics ワークスペースの既定値が設定されます。

* sku - 既定値は、2018 年 4 月の価格モデルでリリースされた新しい 1 GB あたりの価格レベル
* data retention - 既定値は 30 日
* capacity reservation - 既定値は 100 GB

>[!WARNING]
>新しい 2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは **PerGB2018** のみです。
>

>[!NOTE]
>このテンプレートを使用する前に、[追加の詳細](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)を確認し、アクセス制御モード、価格レベル、保有期間、容量予約レベルなどのワークスペースの構成オプションについて完全に理解してください。 Azure Monitor ログを初めてご利用になり、ワークスペースをまだデプロイしていない場合は、[ワークスペースの設計](../azure-monitor/platform/design-logs-deployment.md)に関するガイダンスを確認してアクセス制御について学習し、組織に推奨される設計の実装戦略について理解しておく必要があります。

## <a name="deploy-template"></a>テンプレートのデプロイ

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
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
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
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
    ```

2. 要件に合わせてテンプレートを編集します。 パラメーターをインライン値として渡す代わりに、[Resource Manager パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を作成することを検討してください。

3. このファイルを deployUMSolutiontemplate.json としてローカル フォルダーに保存します。

4. これでこのテンプレートをデプロイする準備が整いました。 PowerShell または Azure CLI を使用できます。 ワークスペースと Automation アカウント名の入力を求められたら、すべての Azure サブスクリプションでグローバルに一意の名前を指定します。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    デプロイが完了するまでに数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ![デプロイ完了時の結果の例](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>次のステップ

Update Management ソリューションをデプロイしたので、VM を管理できるようにし、更新プログラムの評価を確認し、更新プログラムをデプロイしてコンプライアンスを実現することができます。

- 1 台または複数台の Azure マシンに対してはお使いの [Azure Automation アカウント](automation-onboard-solutions-from-automation-account.md)から、Azure 以外のマシンに対しては手動で。

- 単一の Azure VM に対しては、Azure portal の [仮想マシン] ページから。 このシナリオは、[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) VM 用と [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM 用があります。

- [複数の Azure VM](manage-update-multi.md) に対しては、Azure portal の **[仮想マシン]** ページから選択。 
---
title: 'チュートリアル: ARM テンプレートを使用して Azure 静的 Web アプリを発行する'
description: 静的 Web アプリ用の ARM テンプレートを作成してデプロイする
services: static-web-apps
author: petender
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 07/13/2021
ms.author: petender
ms.openlocfilehash: 06f415492157a52b3a258fa2191e1a88e211bccd
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608840"
---
# <a name="tutorial-publish-azure-static-web-apps-using-an-arm-template"></a>チュートリアル: ARM テンプレートを使用して Azure 静的 Web アプリを発行する

この記事では、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md) (ARM テンプレート) を使用して [Azure Static Web Apps](./overview.md) をデプロイする方法を示します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - Azure Static Web Apps 用の ARM テンプレートを作成する
> - Azure Static Web Apps インスタンスを作成するために ARM テンプレートをデプロイする

## <a name="prerequisites"></a>前提条件

- **アクティブな Azure アカウント:** お持ちでない場合は、[無料でアカウントを作成](https://azure.microsoft.com/free/)できます。
- **GitHub アカウント:** お持ちでない場合は、[無料で GitHub アカウントを作成](https://github.com)できます。
- **ARM テンプレート用のエディター:** テンプレートの確認および編集には、JSON エディターが必要です。 [Azure Resource Manager ツール拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)のある Visual Studio Code は、ARM テンプレートの編集に適しています。 Visual Studio Code をインストールして構成する方法については、「[クイックスタート: Visual Studio Code を使用して ARM テンプレートを作成する](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

- **Azure CLI または Azure PowerShell**: ARM テンプレートをデプロイするには、コマンド ライン ツールが必要です。 インストール手順については、以下を参照してください。
  - [Windows OS での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)
  - [Linux OS での Azure CLI のインストール](/cli/azure/install-azure-cli-linux)
  - [macOS での Azure CLI のインストール](/cli/azure/install-azure-cli-macos)
  - [Azure PowerShell をインストールするには](/powershell/azure/install-az-ps)

## <a name="create-a-github-personal-access-token"></a>GitHub 個人用アクセス トークンを作成する

`repositoryToken` は ARM テンプレートのパラメーターの 1 つで、これにより ARM デプロイ プロセスが、静的サイトのソース コードを保持している GitHub リポジトリとやりとりできるようになります。 

1. GitHub アカウント プロファイル (右上隅) で、 **[Settings]\(設定\)** を選択します。

1. **[Developer Settings]\(開発者設定\)** を選択します。

1. **[Personal Access Tokens]\(個人用アクセス トークン\)** を選択します。

1. **[Generate New Token]\(新しいトークンの生成\)** を選択します。

1. _[Note]\(メモ\)_ フィールドでこのトークンの名前を指定します (例: *myfirstswadeployment*)。

1. 次の "*スコープ*" を指定します: **repo、workflow、write:packages**。

1. **[Generate token]\(トークンの生成\)** を選択します。

1. 後で使用するために、トークンの値をコピーしてテキスト エディターに貼り付けます。

> [!IMPORTANT]
> このトークンは必ずコピーし、安全な場所に格納してください。 このトークンを [Azure Key Vault](../azure-resource-manager/templates/template-tutorial-use-key-vault.md) に格納し、自分の ARM テンプレートからアクセスすることを検討してください。

## <a name="create-a-github-repo"></a>GitHub リポジトリを作成する

この記事では、簡単に作業を開始できるように、GitHub テンプレート リポジトリを使用します。 このテンプレートには、Azure Static Web Apps を使用してデプロイするために使用されるスターター アプリが含まれます。

1. 次の場所に移動して、新しいリポジトリを作成します。
    1. [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate)

1. リポジトリに **myfirstswadeployment** という名前を付けます。

    > [!NOTE]
    > Azure Static Web Apps で Web アプリを作成するには、少なくとも 1 つの HTML ファイルが必要です。 このステップで作成するリポジトリには、1 つの _index.html_ ファイルが含まれます。

1. **[Create repository from template]\(テンプレートからリポジトリを作成する\)** を選択します。

    :::image type="content" source="./media/getting-started/create-template.png" alt-text="テンプレートからリポジトリを作成する":::

## <a name="create-the-arm-template"></a>ARM テンプレートを作成する

前提条件を満たしたら、次の手順として、ARM デプロイ テンプレート ファイルを定義します。

1. ARM テンプレートを保持するための新しいフォルダーを作成します。

1. 新しいファイルを作成し、**azuredeploy.json** という名前を付けます。

1. 次の ARM テンプレート スニペットを _azuredeploy.json_ に貼り付けます。

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "type": "string"
                },
                "location": {
                    "type": "string"
                },
                "sku": {
                    "type": "string"
                },
                "skucode": {
                    "type": "string"
                },
                "repositoryUrl": {
                    "type": "string"
                },
                "branch": {
                    "type": "string"
                },
                "repositoryToken": {
                    "type": "securestring"
                },
                "appLocation": {
                    "type": "string"
                },
                "apiLocation": {
                    "type": "string"
                },
                "appArtifactLocation": {
                    "type": "string"
                },
                "resourceTags": {
                    "type": "object"
                },
                "appSettings": {
                    "type": "object"
                }
            },
            "resources": [
                {
                    "apiVersion": "2021-01-15",
                    "name": "[parameters('name')]",
                    "type": "Microsoft.Web/staticSites",
                    "location": "[parameters('location')]",
                    "tags": "[parameters('resourceTags')]",
                    "properties": {
                        "repositoryUrl": "[parameters('repositoryUrl')]",
                        "branch": "[parameters('branch')]",
                        "repositoryToken": "[parameters('repositoryToken')]",
                        "buildProperties": {
                            "appLocation": "[parameters('appLocation')]",
                            "apiLocation": "[parameters('apiLocation')]",
                            "appArtifactLocation": "[parameters('appArtifactLocation')]"
                        }
                    },
                    "sku": {
                        "Tier": "[parameters('sku')]",
                        "Name": "[parameters('skuCode')]"
                    },
                    "resources":[
                        {
                            "apiVersion": "2021-01-15",
                            "name": "appsettings",
                            "type": "config",
                            "location": "[parameters('location')]",
                            "properties": "[parameters('appSettings')]",
                            "dependsOn": [
                                "[resourceId('Microsoft.Web/staticSites', parameters('name'))]"
                            ]
                        }
                    ]
                }
            ]
        }

    ```

1. 新しいファイルを作成し、**azuredeploy.parameters.json** という名前を付けます。

1. 次の ARM テンプレート スニペットを _azuredeploy.parameters.json_ に貼り付けます。

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "value": "myfirstswadeployment"
                },
                "location": { 
                    "value": "Central US"
                },   
                "sku": {
                    "value": "Free"
                },
                "skucode": {
                    "value": "Free"
                },
                "repositoryUrl": {
                    "value": "https://github.com/<YOUR-GITHUB-USER-NAME>/<YOUR-GITHUB-REPOSITORY-NAME>"
                },
                "branch": {
                    "value": "main"
                },
                "repositoryToken": {
                    "value": "<YOUR-GITHUB-PAT>" 
                },
                "appLocation": {
                    "value": "/"
                },
                "apiLocation": {
                    "value": ""
                },
                "appArtifactLocation": {
                    "value": "src"
                },
                "resourceTags": {
                    "value": {
                        "Environment": "Development",
                        "Project": "Testing SWA with ARM",
                        "ApplicationName": "myfirstswadeployment"
                    }
                },
                "appSettings": {
                    "value": {
                        "MY_APP_SETTING1": "value 1",
                        "MY_APP_SETTING2": "value 2"
                    }
                }
            }
        }
    ```

1. 次のパラメーターを更新します。

    | パラメーター | 必要な値 |
    | --- | --- |
    | `repositoryUrl` | 自分の静的 Web アプリ用 GitHub リポジトリの URL を指定します。 |
    | `repositoryToken` | GitHub の PAT トークンを指定します。 |

1. 次の手順でデプロイを実行する前に、更新を保存します。

## <a name="running-the-deployment"></a>デプロイを実行する

テンプレートをデプロイするには、Azure CLI または Azure PowerShell が必要です。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

テンプレートをデプロイするには、Azure CLI と Azure PowerShell のいずれかにサインインします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

---

複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。 `<SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>` を自分のサブスクリプション情報で置き換えます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

---

## <a name="create-a-resource-group"></a>リソース グループを作成する

テンプレートをデプロイするときは、関連リソースを格納するリソース グループを指定します。 デプロイ コマンドを実行する前に、Azure CLI または Azure PowerShell を使用してリソース グループを作成します。

> [!NOTE]
> この記事の CLI の例は、Bash シェルを対象として記述されています。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName="myfirstswadeployRG"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = "myfirstswadeployRG"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

---

## <a name="deploy-template"></a>テンプレートのデプロイ

いずれかのデプロイ オプションを使用してテンプレートをデプロイします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file <PATH-TO-AZUREDEPLOY.JSON> \
  --parameters <PATH-TO-AZUREDEPLOY.PARAMETERS.JSON> \
  --verbose
```

Azure CLI を使用してテンプレートをデプロイする方法の詳細については、「[ARM テンプレートと Azure CLI でリソースをデプロイする](../azure-resource-manager/templates/deploy-cli.md)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$templateparameterFile = Read-Host -Prompt "Enter the template parameter file path and file name"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -TemplateParameterFile $templateparameterfile `
  -verbose
```

Azure PowerShell を使用してテンプレートをデプロイする方法の詳細については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

---

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [静的 Web アプリを構成する](./configuration.md)
---
title: テンプレート スペックのポータル フォームを作成する
description: Azure portal フォームに表示されるフォームを作成する方法について説明します。 フォームを使用してテンプレート スペックをデプロイする
ms.topic: tutorial
ms.date: 11/02/2021
ms.openlocfilehash: 3175264b8d3a5b9fd1698c8ccd1522ddb1f54b63
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477855"
---
# <a name="tutorial-create-azure-portal-forms-for-a-template-spec"></a>チュートリアル: テンプレート スペックの Azure portal フォームを作成する

ユーザーが[テンプレート スペック](template-specs.md)をデプロイできるよう支援するために、Azure portal に表示されるフォームを作成できます。 フォームを使用すると、ユーザーはテンプレート スペックに渡される値をパラメーターとして指定できます。

テンプレート スペックを作成する場合、フォームと Azure Resource Manager テンプレート (ARM テンプレート) を一緒にパッケージ化します。 ポータルを使用してテンプレート スペックをデプロイすると、フォームが自動的に起動します。

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="テンプレート スペックの値を指定するためのフォームのスクリーンショット":::

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

Azure PowerShell の場合は、[バージョン 6.0.0 以降](/powershell/azure/install-az-ps)を使用します。 Azure CLI の場合は、[バージョン 2.24.0 以降](/cli/azure/install-azure-cli)を使用します。

## <a name="create-template"></a>テンプレートの作成

フォームで使用できるさまざまなポータル要素を表示するために、いくつかのパラメーターを含む ARM テンプレートを使用します。 次のテンプレートでは、キー コンテナーが作成され、ユーザーのキー コンテナーへのアクセス許可が構成され、シークレットが追加されます。

このファイルをコピーし、ローカルに保存します。 このチュートリアルでは、これに **keyvault.json** という名前を付けたことを前提としていますが、任意の名前を付けることができます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Specifies the Azure location where the key vault should be created."
      }
    },
    "enabledForDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Virtual Machines are permitted to retrieve certificates stored as secrets from the key vault."
      }
    },
    "enabledForDiskEncryption": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Disk Encryption is permitted to retrieve secrets from the vault and unwrap keys."
      }
    },
    "enabledForTemplateDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Resource Manager is permitted to retrieve secrets from the key vault."
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue": "[subscription().tenantId]",
      "metadata": {
        "description": "Specifies the Azure Active Directory tenant ID that should be used for authenticating requests to the key vault. Get it by using Get-AzSubscription cmdlet."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the secret that you want to create."
      }
    },
    "secretValue": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the value of the secret that you want to create."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "enabledForDeployment": "[parameters('enabledForDeployment')]",
        "enabledForDiskEncryption": "[parameters('enabledForDiskEncryption')]",
        "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "objectId": "[parameters('objectId')]",
            "tenantId": "[parameters('tenantId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/secrets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "properties": {
        "value": "[parameters('secretValue')]"
      }
    }
  ]
}
```

## <a name="create-default-form"></a>既定のフォームを作成する

Azure portal には、フォームを作成およびプレビューするためのサンドボックスが備わっています。 このサンドボックスで、既存の ARM テンプレートからフォームを生成できます。 この既定のフォームを使用して、テンプレート スペックのフォームの作成を開始します。

1. [フォーム ビューのサンドボックス](https://aka.ms/form/sandbox)を開きます。

1. **[パッケージの種類]** を **CustomTemplate** に設定します。

   :::image type="content" source="./media/template-specs-create-portal-forms/package-type.png" alt-text="[パッケージの種類] の CustomTemplate への設定を示すスクリーンショット":::

1. 既存のテンプレートを開くためのアイコンを選択します。

   :::image type="content" source="./media/template-specs-create-portal-forms/open-template.png" alt-text="ファイルを開くためのアイコンのスクリーンショット":::

1. ローカルに保存したキー コンテナー テンプレートに移動します。 それを選択し、 **[開く]** を選択します。
1. 現在の変更を上書きするかどうかを確認するメッセージが表示されたら、 **[はい]** を選択します。
1. 自動生成されたフォームがコード ウィンドウに表示されます。 変更を行わずに動作を確認するには、 **[プレビュー]** を選択します。

   :::image type="content" source="./media/template-specs-create-portal-forms/preview-form.png" alt-text="[プレビュー] の選択を示すスクリーンショット":::

1. サンドボックスにフォームが表示されます。 サブスクリプション、リソース グループ、リージョンを選択するためのフィールドがあります。 また、テンプレートのすべてのパラメーターのフィールドもあります。

   ほとんどのフィールドはテキスト ボックスですが、一部のフィールドはパラメーターの型に固有のものです。 テンプレートにパラメーターの許可値が含まれる場合、自動生成されるフォームではドロップダウン要素が使用されます。 このドロップダウン要素には、許可値が事前に設定されます。

   > [!WARNING]
   > **[作成]** を選択すると、実際のデプロイが開始されるため、これは選択しないでください。 このチュートリアルの後半に、テンプレート スペックをデプロイする機会があります。

## <a name="customize-form"></a>フォームをカスタマイズする

既定のフォームは、フォームを理解するための適切な出発点ですが、通常はカスタマイズする必要があります。 サンドボックス内または Visual Studio Code 内でこれを編集できます。 プレビュー オプションは、サンドボックスでのみ使用できます。

1. 正しいスキーマを設定しましょう。 スキーマ テキストを以下に置き換えます。

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-2" highlight="2" :::

1. フォームに、その使用方法を表す **タイトル** を付けます。

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-6" highlight="6" :::

1. 既定のフォームでは、テンプレートのすべてのフィールドが、**Basics** という 1 つのステップに結合されています。 ユーザーが、自分で指定する値を理解できるようにするために、フォームを複数のステップに分割します。 各ステップには、デプロイするソリューションの論理部分に関連するフィールドを含めます。

   **Basics** というラベルのステップを見つけます。 このステップは保持し、その下にステップを追加します。 新しいステップでは、キー コンテナーの構成、ユーザー アクセス許可の設定、シークレットの指定に重点を置きます。 Basics ステップの後にコンマを追加する必要があります。

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/steps.json" highlight="15-32" :::

   > [!IMPORTANT]
   > フォームのプロパティでは、大文字と小文字が区別されます。 例に示されている大文字と小文字を使用してください。

1. **[Preview]\(プレビュー\)** を選択します。 ステップが表示されますが、そのほとんどに要素はありません。

   :::image type="content" source="./media/template-specs-create-portal-forms/view-steps.png" alt-text="フォームのステップのスクリーンショット":::

1. 次に、要素を適切なステップに移動します。 **Secret Name** および **Secret Value** というラベルの要素から開始します。 これらの要素を **Basics** ステップから削除し、**Secret** ステップに追加します。

   ```json
   {
     "name": "secret",
     "label": "Secret",
     "elements": [
       {
         "name": "secretName",
         "type": "Microsoft.Common.TextBox",
         "label": "Secret Name",
         "defaultValue": "",
         "toolTip": "Specifies the name of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "visible": true
       },
       {
         "name": "secretValue",
         "type": "Microsoft.Common.PasswordBox",
         "label": {
           "password": "Secret Value",
           "confirmPassword": "Confirm password"
         },
         "toolTip": "Specifies the value of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "options": {
           "hideConfirmation": true
         },
         "visible": true
       }
     ]
   }
   ```

1. 要素を移動するとき、`outputs` セクションを修正する必要があります。 現時点で、outputs セクションでは、これらの要素が Basics ステップ内にまだあるものとして参照されます。 `secret` ステップ内の要素を参照するように構文を修正します。

   ```json
   "outputs": {
     "parameters": {
       ...
       "secretName": "[steps('secret').secretName]",
       "secretValue": "[steps('secret').secretValue]"
     }
   ```

1. 引き続き、要素を適切なステップに移動します。 それぞれを確認するのではなく、更新されたフォーム全体を確認します。

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" :::

このファイルを **keyvaultform.json** という名前でローカルに保存します。

## <a name="create-template-spec"></a>テンプレート スペックを作成する

テンプレート スペックを作成するとき、両方のファイルを指定します。

PowerShell の場合、[New-AzTemplateSpec](/powershell/module/az.resources/new-aztemplatespec) を使用し、`-UIFormDefinitionFile` パラメーターでフォームを指定します。

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 1 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Azure CLI の場合、[az ts create](/cli/azure/ts#az_ts_create) を使用し、`--ui-form-definition` パラメーターでフォームを指定します。

```azurecli
az ts create \
  --name keyvaultspec \
  --version 1 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

## <a name="deploy-through-portal"></a>ポータルを使用してデプロイする

フォームをテストするには、ポータルに移動し、テンプレート スペックに移動します。 **[デプロイ]** を選択します。

:::image type="content" source="./media/template-specs-create-portal-forms/deploy-template-spec.png" alt-text="[デプロイ] オプションがあるテンプレート スペックの概要のスクリーンショット":::

作成したフォームが表示されます。 ステップを実行し、フィールドの値を指定します。

**Basics** ステップで、**Region** のフィールドが表示されます。 このフィールドは、リソース グループの場所のために使用されます。 **Key Vault** ステップで、**Location** のフィールドが表示されます。 このフィールドは、キー コンテナーの場所のために使用されます。

**Permissions** ステップで、オブジェクト ID に対して自分のユーザー ID を指定できます。 キーとシークレットのアクセス許可に対して既定値 (`["list"]`) を使用します。 次のセクションで、このオプションを改善します。

値の指定が終了したら、 **[作成]** を選択してテンプレート スペックをデプロイします。

## <a name="improve-the-form"></a>フォームを改善する

前のセクションでは、ステップを追加し、要素を移動しましたが、既定の動作は変更しませんでした。 このセクションでは、テンプレート スペックのユーザー エクスペリエンスを向上させる変更を行います。

これまで 2 つのアクセス許可フィールドはテキスト ボックスでした。 ここでは、ドロップダウンを使用します。 タイプを `Microsoft.Common.DropDown` に設定します。

以下を更新します。`keysPermissions`:

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-171" highlight="3" :::

および `secretsPermissions`:

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-255" highlight="3" :::

これらのフィールドでは、配列をテンプレートに渡す必要があります。 通常のドロップダウンは、1 つの値しか選択できないため機能しません。 複数の値を選択し、それらを配列として渡すために、`multiselect` フィールドを追加し、`true` に設定します。

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-173" highlight="5" :::

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-257" highlight="5" :::

最後に、ドロップダウンの許可値と既定値を指定する必要があります。

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-304" highlight="6-8,12-81,90-92,96-133" :::

テンプレート スペックの新しいバージョンを作成します。

PowerShell の場合:

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 2 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Azure CLI の場合:

```azurecli
az ts create \
  --name keyvaultspec \
  --version 2 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

改善されたポータル フォームを使用してテンプレート スペックを再デプロイします。

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="テンプレート スペックの値を指定するためのフォームのスクリーンショット":::

これで、アクセス許可のフィールドは、複数の値が許可されるドロップダウンになりました。

## <a name="next-steps"></a>次のステップ

テンプレート スペックをリンクされたテンプレートとしてデプロイする方法の詳細については、「[チュートリアル:テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。

---
title: Key Vault のシークレットと Bicep
description: Bicep デプロイ時にパラメーターとしてキー コンテナーからシークレットを渡す方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e940a812b4e010e9499a9a85cfd400b679d43781
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2021
ms.locfileid: "112376308"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-bicep-deployment"></a>Bicep デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

お使いの Bicep ファイルやパラメーター ファイルにセキュリティで保護された値 (パスワードなど) を直接入れる代わりに、デプロイ時に、[Azure Key Vault](../../key-vault/general/overview.md) から値を取得できます。 [モジュール](./modules.md)が `secure:true` 修飾子を持つ `string` パラメーターを必要とする場合は、[getSecret 関数](bicep-functions-resource.md#getsecret)を使用してキー コンテナーのシークレットを取得できます。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。

> [!IMPORTANT]
> この記事では、機密の値をテンプレート パラメーターとして渡す方法に焦点を当てます。 シークレットがパラメーターとして渡されるとき、キー コンテナーは別のサブスクリプションに存在するか、デプロイ先のリソース グループに存在することがあります。 
>
> この記事では、仮想マシンのプロパティを、キー コンテナー内の証明書の URL に設定する方法については説明しません。 そのシナリオのクイックスタート テンプレートについては、[Azure Key Vault から証明書を仮想マシンにインストールする](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows)方法に関する記事を参照してください。

## <a name="deploy-key-vaults-and-secrets"></a>キー コンテナーとシークレットをデプロイする

Bicep のデプロイ時にキー コンテナーにアクセスするには、キー コンテナーの `enabledForTemplateDeployment` を `true` に設定します。

既にキー コンテナーがある場合は、テンプレートのデプロイが許可されていることを確認してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

新しいキー コンテナーを作成してシークレットを追加するには、次のように使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

キー コンテナーの所有者には、シークレットを作成するためのアクセスが自動的に付与されています。 シークレットを操作しているユーザーが、そのキー コンテナーの所有者でない場合、次を使用してアクセス許可を付与します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

キー コンテナーの作成とシークレットの追加の詳細については、次を参照してください。

- [CLI を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-cli.md)
- [PowerShell を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-powershell.md)
- [ポータルを使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-portal.md)
- [.NET を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-net.md)
- [Node.js を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>シークレットへのアクセスを許可する

Bicep ファイルをデプロイするユーザーには、そのリソース グループとキー コンテナーのスコープで `Microsoft.KeyVault/vaults/deploy/action` のアクセス許可がある必要があります。 このアクセスは、[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールが許可します。 キー コンテナーの作成者である場合は、その所有者であり、そのアクセス許可を持っています。

次の手順は、最小限のアクセス許可を持つロールを作成する方法と、ユーザーを割り当てる方法を示します。

1. カスタム ロール定義の JSON ファイルを作成します。

    ```json
    {
      "Name": "Key Vault Bicep deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a Bicep file with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```

    "00000000-0000-0000-0000-000000000000" はサブスクリプション ID に置き換えます。

2. JSON ファイルを使用して新しいロールを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    このサンプルでは、リソース グループ レベルでカスタム ロールを割り当てます。

[Managed Applications](../managed-applications/overview.md) の Bicep ファイルでキー コンテナーを使用する場合は、**アプライアンス リソース プロバイダー** サービス プリンシパルにアクセス許可を付与する必要があります。 詳細については、「[Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)」(Azure Managed Applications のデプロイ時に Key Vault シークレットにアクセスする) を参照してください。

## <a name="use-getsecret-function"></a>getSecret 関数を使用する

[getSecret 関数](./bicep-functions-resource.md#getsecret)を使用してキー コンテナーのシークレットを取得し、その値をモジュールの `string` パラメーターに渡すことができます。 `getSecret` 関数は `Microsoft.KeyVault/vaults` リソースでのみ呼び出すことができ、`@secure()` デコレーターのパラメーターと共にのみ使用できます。

次の Bicep ファイルによって、Azure SQL サーバーが作成されます。 `adminPassword` パラメーターには `@secure()` デコレーターがあります。

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

前の Bicep ファイルをモジュールとして使用してみましょう。ファイル名は、メインの Bicep ファイルと同じディレクトリにある *sql.bicep* です。

次の Bicep ファイルは、モジュールとして sql.bicep を使用します。  Bicep ファイルは既存のキー コンテナーを参照し、`getSecret` 関数を呼び出してキー コンテナーのシークレットを取得し、その値をパラメーターとしてモジュールに渡します。

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="reference-secrets-in-parameter-file"></a>パラメーター ファイルのシークレットを参照する

モジュールを使用しない場合は、パラメーター ファイルで直接キー コンテナーを参照できます。 次の図は、パラメーター ファイルがシークレットを参照し、その値を Bicep ファイルに渡すしくみを示しています。

![Resource Manager のキー コンテナー統合の図](./media/key-vault-parameter/statickeyvault.png)

次の Bicep ファイルでは、管理者パスワードを含む SQL サーバーがデプロイされます。 パスワード パラメーターは、セキュリティで保護された文字列に設定されます。 しかし、Bicep には値がどこから来ているかが指定されていません。

```bicep
param adminLogin string

@secure()
param adminPassword string

param sqlServerName string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

---

ここで、前の Bicep ファイルのためにパラメーター ファイルを作成します。 このパラメーター ファイルで、Bicep ファイル内のパラメーターの名前に一致するパラメーターを指定します。 パラメーター値のために、キー コンテナーのシークレットを参照します。 シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。

次のパラメーター ファイルには、キー コンテナーのシークレットが既に存在している必要があり、そのリソース ID に対して静的な値を指定します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "value": "exampleadmin"
    },
    "adminPassword": {
      "reference": {
        "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
        },
        "secretName": "ExamplePassword"
      }
    },
    "sqlServerName": {
      "value": "<your-server-name>"
    }
  }
}
```

現在のバージョン以外のバージョンのシークレットを使用する必要がある場合は、`secretVersion` プロパティを含めます。

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

テンプレートをデプロイし、パラメーター ファイルを渡します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-file <Bicep-file> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile <Bicep-file> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="next-steps"></a>次のステップ

- キー コンテナーの一般的な情報については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」をご覧ください。
- キー シークレットの詳細な参照例については、GitHub にある[キー コンテナーの例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。
- キー コンテナーからセキュリティで保護された値を渡すことに関する Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。

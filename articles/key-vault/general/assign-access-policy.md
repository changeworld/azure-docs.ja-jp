---
title: Azure Key Vault アクセス ポリシーを割り当てる (CLI)
description: Azure CLI を使用して、セキュリティ プリンシパルまたはアプリケーション ID に Key Vault アクセス ポリシーを割り当てる方法について。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b194bec5f03d5985e282b7c8d220e268de1a763c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837754"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault アクセス ポリシーを割り当てる

Key Vault アクセス ポリシーは、特定のセキュリティ プリンシパル (ユーザー、アプリケーション、またはユーザー グループ) が、Key Vault の[シークレット](../secrets/index.yml)、[キー](../keys/index.yml)、および[証明書](../certificates/index.yml)に対して、さまざまな操作を実行できるかどうかを決定します。 アクセス ポリシーは、[Azure portal](assign-access-policy-portal.md)、Azure CLI、または [Azure PowerShell](assign-access-policy-powershell.md) を使用して割り当てることができます。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="assign-an-access-policy"></a>アクセス ポリシーを割り当てる

1.  [Azure portal](https://portal.azure.com) で、Key Vault リソースに移動します。 

1.  **[設定]** で **[アクセス ポリシー]** を選択し、 **[アクセス ポリシーの追加]** を選択します。

    ![[アクセス ポリシー] を選択し、[ロール割り当ての追加] を選択しています](../media/authentication/assign-policy-portal-01.png)

1.  **[証明書のアクセス許可]** 、 **[キーのアクセス許可]** 、および **[シークレットのアクセス許可]** でアクセス許可を選択します。 一般的なアクセス許可の組み合わせが含まれるテンプレートを選択することもできます。

    ![アクセス ポリシーのアクセス許可を指定しています](../media/authentication/assign-policy-portal-02.png)

1. **[プリンシパルの選択]** で **[選択なし]** リンクを選択すると、 **[プリンシパル]** 選択ウィンドウが開きます。 検索フィールドにユーザー、アプリ、またはサービス プリンシパルの名前を入力し、適切な結果を選択したら、 **[選択]** を選択します。

    ![アクセス ポリシーのセキュリティ プリンシパルを選択](../media/authentication/assign-policy-portal-03.png)

    アプリにマネージド ID を使用している場合は、アプリ自体の名前を検索して選択します。 (セキュリティ プリンシパルの詳細については、[「Key Vault 認証」](authentication.md) を参照してください。
 
1.  **[アクセス ポリシーの追加]** ウィンドウに戻り、 **[追加]** を選択して、アクセス ポリシーを保存します。

    ![割り当てられたセキュリティ プリンシパルを使用してアクセス ポリシーを追加](../media/authentication/assign-policy-portal-04.png)

1. **[アクセス ポリシー]** ページに戻り、アクセス ポリシーが **[現在のアクセス ポリシー]** に表示されていることを確認したら、 **[保存]** を選択します。 アクセス ポリシーは、保存するまで適用されません。

    ![アクセス ポリシーの変更の保存](../media/authentication/assign-policy-portal-05.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Active Directory での Azure CLI を使用したグループの作成の詳細については、[az ad group create](/cli/azure/ad/group#az_ad_group_create) および [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add) に関する記事を参照してください。

## <a name="configure-the-azure-cli-and-sign-in"></a>Azure CLI を構成してサインインする

1. Azure CLI コマンドをローカルで実行するには、[Azure CLI](/cli/azure/install-azure-cli) をインストールします。
 
    コマンドをクラウドで直接実行するには、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。

1. ローカル CLI のみ: `az login` を使用して Azure にサインインします。

    ```bash
    az login
    ```

    `az login` コマンドを実行すると、必要に応じて、資格情報を収集するためのブラウザー ウィンドウが開きます。

## <a name="acquire-the-object-id"></a>オブジェクト ID を取得する

アクセス ポリシーの割り当て先となるアプリケーション、グループ、またはユーザーのオブジェクト ID を決定します。

- アプリケーションとその他のサービス プリンシパル: [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) コマンドを使用して、サービス プリンシパルを取得します。 コマンドの出力を調べて、アクセス ポリシーの割り当て先となるセキュリティ プリンシパルのオブジェクト ID を決定します。

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- グループ: [az ad group list](/cli/azure/ad/group#az_ad_group_list) コマンドを使用して、`--display-name` パラメーターを使用し、結果をフィルター処理します。

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- ユーザー: [az ad user show](/cli/azure/ad/user#az_ad_user_show) コマンドを使用して、ユーザーの電子メール アドレスを `--id` パラメーターに渡します。

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>アクセス ポリシーを割り当てる
    
[az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) コマンドを使用して、必要なアクセス許可を割り当てます。

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

`<object-id>` を、お使いのセキュリティ プリンシパルのオブジェクト ID に置き換えます。

これらの特定の種類にアクセス許可を割り当てるときに含める必要があるのは、`--secret-permissions`、`--key-permissions`、`--certificate-permissions` のみです。 `<secret-permissions>`、`<key-permissions>`、`<certificate-permissions>` に使用できる値は、[az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) ドキュメントに記載されています。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell を使用して Azure Active Directory にグループを作成する方法の詳細については、「[New-AzureADGroup](/powershell/module/azuread/new-azureadgroup)」および「[Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)」を参照してください。

## <a name="configure-powershell-and-sign-in"></a>PowerShell の構成とサインイン

1. コマンドをローカルで実行するには、[Azure PowerShell](/powershell/azure/) をインストールします (まだインストールしていない場合)。

    コマンドを直接クラウドで実行するには、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。

1. ローカル PowerShell のみ:

    1. [Azure Active Directory PowerShell モジュール](https://www.powershellgallery.com/packages/AzureAD)をインストールします。

    1. Azure にサインインします。

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>オブジェクト ID を取得する

アクセス ポリシーの割り当て先となるアプリケーション、グループ、またはユーザーのオブジェクト ID を決定します。

- アプリケーションとその他のサービスプリンシパル: [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) コマンドレットを `-SearchString` パラメーターを指定して使用し、結果を目的のサービス プリンシパルの名前にフィルター処理します。

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- グループ: [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) コマンドレットを `-SearchString` パラメーターを指定して使用し、結果を目的のグループの名前にフィルター処理します。

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    出力では、オブジェクト ID が `Id` として一覧表示されます。

- ユーザー: [Get-AzADUser](/powershell/module/az.resources/get-azaduser) コマンドレットを使用して、そのユーザーのメール アドレスを `-UserPrincipalName` パラメーターに渡します。

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    出力では、オブジェクト ID が `Id` として一覧表示されます。

## <a name="assign-the-access-policy"></a>アクセス ポリシーを割り当てる

[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) コマンドレットを使用して、アクセス ポリシーを割り当てます。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

これらの特定の種類にアクセス許可を割り当てるときに含める必要があるのは、`-PermissionsToSecrets`、`-PermissionsToKeys`、`-PermissionsToCertificates` のみです。 `<secret-permissions>`、`<key-permissions>`、`<certificate-permissions>` に使用できる値は、[Set-AzKeyVaultAccessPolicy に関するドキュメントのパラメーター](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters)に関する説明に記載されています。

---

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault セキュリティ](security-features.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)

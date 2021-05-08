---
title: Azure Key Vault アクセス ポリシーを割り当てる
description: Azure portal、Azure CLI、または Azure PowerShell を使用して、セキュリティ プリンシパルまたはアプリケーション ID に Key Vault アクセス ポリシーを割り当てる方法について説明します。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751420"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Azure PowerShell を使用して Key Vault アクセス ポリシーを割り当てる

Key Vault アクセス ポリシーは、特定のセキュリティ プリンシパル (ユーザー、アプリケーション、またはユーザー グループ) が、Key Vault の[シークレット](../secrets/index.yml)、[キー](../keys/index.yml)、および[証明書](../certificates/index.yml)に対して、さまざまな操作を実行できるかどうかを決定します。 アクセス ポリシーは、[Azure portal](assign-access-policy-portal.md)、[Azure CLI](assign-access-policy-cli.md)、または Azure PowerShell (この記事) を使用して割り当てることができます。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

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

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault セキュリティ: ID 管理とアクセス管理](security-overview.md#identity-management)
- [キー コンテナーをセキュリティで保護する](security-overview.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
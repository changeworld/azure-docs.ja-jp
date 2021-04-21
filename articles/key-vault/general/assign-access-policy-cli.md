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
ms.openlocfilehash: 349d7453962a736c9f15bb7d31d5a44098f463a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791951"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault アクセス ポリシーを割り当てる

Key Vault アクセス ポリシーは、特定のセキュリティ プリンシパル (ユーザー、アプリケーション、またはユーザー グループ) が、Key Vault の[シークレット](../secrets/index.yml)、[キー](../keys/index.yml)、および[証明書](../certificates/index.yml)に対して、さまざまな操作を実行できるかどうかを決定します。 アクセス ポリシーは、Azure portal、[Azure CLI (この記事)](assign-access-policy-portal.md)、または [Azure PowerShell](assign-access-policy-powershell.md) を使用して割り当てることができます。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

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

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault セキュリティ: ID 管理とアクセス管理](security-overview.md#identity-management)
- [キー コンテナーをセキュリティで保護する](security-overview.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
---
title: Azure Key Vault アクセス ポリシーを割り当てる (CLI)
description: Azure CLI を使用して、サービス プリンシパルまたはアプリケーション ID に Key Vault アクセス ポリシーを割り当てる方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 59ba81944ecdf4f2b6322f4298e61df33f5b1da8
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289176"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault アクセス ポリシーを割り当てる

Key Vault アクセス ポリシーは、特定のサービス プリンシパル (アプリケーションまたはユーザー グループ) が、Key Vault の[シークレット](../secrets/index.yml)、[キー](../keys/index.yml)、および[証明書](../certificates/index.yml)に対して、さまざまな操作を実行できるかどうかを決定します。 アクセス ポリシーは、Azure portal、[Azure CLI (この記事)](assign-access-policy-portal.md)、または [Azure PowerShell](assign-access-policy-powershell.md) を使用して割り当てることができます。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure Active Directory での Azure CLI を使用したグループの作成の詳細については、[az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) および [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add) に関する記事を参照してください。

## <a name="configure-the-azure-cli-and-sign-in"></a>Azure CLI を構成してサインインする

1. Azure CLI コマンドをローカルで実行するには、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールします。
 
    コマンドをクラウドで直接実行するには、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。

1. ローカル CLI のみ: `az login` を使用して Azure にサインインします。

    ```bash
    az login
    ```

    `az login` コマンドを実行すると、必要に応じて、資格情報を収集するためのブラウザー ウィンドウが開きます。

## <a name="acquire-the-object-id"></a>オブジェクト ID を取得する

アクセス ポリシーの割り当て先となるアプリケーション、グループ、またはユーザーのオブジェクト ID を決定します。

- アプリケーションとその他のサービス プリンシパル: [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) コマンドを使用して、サービス プリンシパルを取得します。 コマンドの出力を調べて、アクセス ポリシーの割り当て先となるセキュリティ プリンシパルのオブジェクト ID を決定します。

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- グループ: [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) コマンドを使用して、`--display-name` パラメーターを使用し、結果をフィルター処理します。

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- ユーザー: [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) コマンドを使用して、ユーザーの電子メール アドレスを `--id` パラメーターに渡します。

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>アクセス ポリシーを割り当てる
    
[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを使用して、必要なアクセス許可を割り当てます。

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

`<object-id>` をサービス プリンシパルのオブジェクト ID に置き換えます。

これらの特定の種類にアクセス許可を割り当てるときに含める必要があるのは、`--secret-permissions`、`--key-permissions`、`--certificate-permissions` のみです。 `<secret-permissions>`、`<key-permissions>`、`<certificate-permissions>` に使用できる値は、[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) ドキュメントに記載されています。

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault セキュリティ: ID 管理とアクセス管理](overview-security.md#identity-and-access-management)
- [キー コンテナーをセキュリティで保護する](secure-your-key-vault.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
- [Azure Key Vault のベスト プラクティス](best-practices.md)
---
title: Azure Key Vault Managed HSM 復旧の概要 | Microsoft Docs
description: Managed HSM 復旧機能は、HSM リソースとキーが偶発的または不正に削除されるのを防ぐように設計されています。
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.author: mbaldwin
author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: c0688d40e0ccb71b98e598da7d5ff8100b735229
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195151"
---
# <a name="managed-hsm-soft-delete-and-purge-protection"></a>Managed HSM の論理的削除と消去保護

この記事では、Managed HSM の 2 つの復旧機能 (論理的削除と消去保護) について説明します。 これらの機能の概要について説明し、Azure CLI と Azure PowerShell を使用してそれらを管理する方法を示します。

詳細については、[Managed HSM の概要](overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [無料で作成できます](https://azure.microsoft.com/free/dotnet)。
* [PowerShell モジュール](/powershell/azure/install-az-ps)。
* Azure CLI 2.25.0 以降。 `az --version` を実行して、使用しているバージョンを確認します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* マネージド HSM。 これを作成するには、[Azure CLI](./quick-create-cli.md) または [Azure PowerShell](./quick-create-powershell.md) を使用します。
* ユーザーは、論理的に削除された HSM またはキーに対して操作を実行するために次のアクセス許可が必要になります。

  | ロールの割り当て | 説明 |
  |---|---|
  |[Managed HSM 共同作成者](../../role-based-access-control/built-in-roles.md#managed-hsm-contributor)|論理的に削除された HSM を一覧表示、復旧、消去する|
  |[Managed HSM 暗号化ユーザー](./built-in-roles.md)|論理的に削除されたキーを一覧表示する|
  |[Managed HSM 暗号化担当者](./built-in-roles.md)|論理的に削除されたキーを消去および復旧する|



## <a name="what-are-soft-delete-and-purge-protection"></a>論理的な削除と消去保護とは

[論理的な削除](soft-delete-overview.md)と消去保護は、復旧機能です。


*論理的な削除* は、HSM とキーが誤って削除されるのを防ぐよう設計されています。 論理的な削除は、ごみ箱のような働きをします。 HSM またはキーを削除すると、構成可能な保持期間または既定期間の 90 日間、回復可能な状態に維持されます。 論理的に削除された状態の HSM とキーは、*消去* することもできます。これは、完全に削除されることを意味します。 消去すると、HSM とキーを消去されたものと同じ名前で再作成できます。 HSM とキーの復旧と削除はどちらも特定のロールの割り当てが必要です。 論理的な削除を無効にすることはできません。

> [!NOTE]
> 基になるリソースは、削除された状態にあるときでも HSM に割り当てられたままなので、HSM リソースはその状態にある間、時間あたりの料金が発生し続けます。

Managed HSM 名は、すべてのクラウド環境でグローバルに一意です。 そのためマネージド HSM を、論理的に削除された状態で存在するものと同じ名前で作成することはできません。 同様に、キーの名前は HSM 内で一意です。 キーを、論理的に削除された状態で存在するものと同じ名前で作成することはできません。

詳細については、[Managed HSM の論理的な削除の概要](soft-delete-overview.md)に関する記事を参照してください。

*消去保護* は、悪意のある内部関係者によって HSM とキーが削除されるのを防ぐように設計されています。 これは、時間ベースのロックを備えたごみ箱のようなものです。 構成可能な保持期間中であればいつでも、項目を回復できます。 HSM やキーを、保持期間が終わるまでは、完全に削除したり消去したりできません。 保持期間が終了すると、HSM やキーは自動的に消去されます。

> [!NOTE]
> 管理者のロールまたはアクセス許可によって消去保護をオーバーライド、無効化、または回避することができません。 *消去保護が有効な場合、それを Microsoft を含む誰もが無効にしたりオーバーライドしたりすることができません。* そのため、削除された HSM を復旧するか、保持期間が終了するまで待った後で HSM 名を再利用する必要があります。

## <a name="manage-keys-and-managed-hsms"></a>マネージド キーとマネージド HSM

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="managed-hsms-cli"></a>マネージド HSM (CLI) 

* マネージド HSM の論理的な削除と消去保護の状態を確認するには:

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```

* HSM を削除するには:

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```
    
  このアクションが復旧可能な理由は、論理的な削除が既定でオンになっているからです。

* 論理的に削除されたすべての HSM を一覧表示するには:

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type hsm
    ```

* 論理的に削除された HSM を復旧するには:

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```


* 論理的に削除された HSM を消去するには:

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```
    > [!WARNING] 
    > この操作を行うと、HSM が完全に削除されます。

* HSM で消去保護を有効にするには:

    ```azurecli
    az keyvault update-hsm --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME} --enable-purge-protection true
    ```

### <a name="keys-cli"></a>キー (CLI)

* キーを削除するには:

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* 削除されたキーを一覧表示するには:

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME}
    ```

* 削除されたキーを復旧するには:

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* 論理的に削除されたキーを消去するには: 

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```
    > [!WARNING] 
    > この操作を行うと、キーが完全に削除されます。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="managed-hsms-powershell"></a>マネージド HSM (PowerShell)

* マネージド HSM の論理的な削除と消去保護の状態を確認するには:

    ```powershell
    Get-AzKeyVaultManagedHsm -Name "ContosoHSM"
    ```

* HSM を削除するには:

    ```powershell
    Remove-AzKeyVaultManagedHsm -Name 'ContosoHSM'
    ```
  このアクションが復旧可能な理由は、論理的な削除が既定でオンになっているからです。

### <a name="keys-powershell"></a>キー (PowerShell)

* キーを削除するには:

  ```powershell
  Remove-AzKeyVaultKey -HsmName ContosoHSM -Name 'MyKey'
  ```

* 削除されたすべてのキーを一覧表示するには: 

  ```powershell
  Get-AzKeyVaultKey -HsmName ContosoHSM -InRemovedState
  ```

* 論理的に削除されたキーを復旧するには:

    ```powershell
    Undo-AzKeyVaultKeyRemoval -HsmName ContosoHSM -Name ContosoFirstKey
    ```

* 論理的に削除されたキーを消去するには:

    ```powershell
    Remove-AzKeyVaultKey -HsmName ContosoHSM -Name ContosoFirstKey -InRemovedState
    ```
    > [!WARNING] 
    > この操作を行うと、キーが完全に削除されます。
    
---

## <a name="next-steps"></a>次のステップ

- [Managed HSM PowerShell コマンドレット](/powershell/module/az.keyvault)
- [Key Vault の Azure CLI コマンド](/cli/azure/keyvault)
- [Managed HSM の完全バックアップと復元](backup-restore.md)
- [Managed HSM のログ記録を有効にする方法](logging.md)

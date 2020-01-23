---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021117"
---
Azure Data Explorer は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、データの暗号化に使用する目的で、カスタマー マネージド キーを提供できます。 顧客が管理するキーは [Azure Key Vault](/azure/key-vault/key-vault-overview) に格納する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Data Explorer クラスターとキー コンテナーは同じリージョンに存在している必要があります。ただし、サブスクリプションは異なっていてもかまいません。 カスタマー マネージド キーの詳細については、[カスタマー マネージド キーと Azure Key Vault](/azure/storage/common/storage-service-encryption) に関する記事を参照してください。 この記事では、カスタマー マネージド キーを構成する方法について説明します。

> [!Note]
> Azure Data Explorer でカスタマー マネージド キーを構成するには、[キー コンテナーの 2 つのプロパティを設定](/azure/key-vault/key-vault-ovw-soft-delete)する必要があります。 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** です。 これらのプロパティは、既定では有効になっていません。 これらのプロパティを有効にするには、[PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) または [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) を使用します。 RSA キーで 2048 のキー サイズのみがサポートされています。

## <a name="assign-an-identity-to-the-cluster"></a>クラスターに ID を割り当てる

お使いのクラスターでカスタマー マネージド キーを有効にするには、まず、そのクラスターにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をクラスターに付与します。 システム割り当てマネージド ID の構成については、[マネージド ID](/azure/data-explorer/managed-identities) に関するページを参照してください。

## <a name="create-a-new-key-vault"></a>新しいキー コンテナーを作成する

PowerShell を使用して、新しいキー コンテナーを作成するには、[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) を呼び出します。 Azure Data Explorer 暗号化用のカスタマー マネージド キーの格納に使用するキー コンテナーには、2 つのキー保護設定 ( **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** ) を有効にする必要があります。 次の例のかっこ内のプレースホルダー値は、独自の値に置き換えてください。

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを構成する

次に、キー コンテナーのアクセス ポリシーを構成し、クラスターからアクセスできるようにします。 この手順では、先ほどクラスターに割り当てたシステム割り当てマネージド ID を使用します。 キー コンテナーのアクセス ポリシーを設定するには、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>新しいキーを作成する

次に、キー コンテナーに新しいキーを作成します。 新しいキーを作成するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) を呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

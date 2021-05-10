---
title: Managed HSM データ プレーン ロールを管理する - Azure Key Vault | Microsoft Docs
description: マネージド HSM のロールの割り当てを管理するには、こちらの記事をご利用ください
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 4d36b2c2178c7205246cd7c59aefedef3358e473
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951744"
---
# <a name="managed-hsm-role-management"></a>Managed HSM のロール管理

> [!NOTE]
> Key Vault では、コンテナーとマネージド HSM という 2 種類のリソースがサポートされています。 この記事では、**Managed HSM** について説明します。 コンテナーの管理方法については、「[Azure CLI を使用して Key Vault を管理する](../general/manage-with-cli2.md)」を参照してください。

Managed HSM の概要については、[Managed HSM の概要](overview.md)に関するページをご覧ください。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

この記事では、Managed HSM データ プレーン ロールを管理する方法について説明します。 Managed HSM アクセス制御モデルの詳細については、「[Managed HSM のアクセス制御](access-control.md)」をご覧ください。

セキュリティ プリンシパル (ユーザー、サービス プリンシパル、グループ、またはマネージド ID など) が Managed HSM データ プレーンを操作できるようにするには、それらの操作の実行を許可するロールが割り当てられている必要があります。 たとえば、アプリケーションでキーを使用して署名操作を実行できるようにするには、データ アクションの 1 つとして "Microsoft.KeyVault/managedHSM/keys/sign/action" を含むロールが割り当てられている必要があります。 ロールは特定のスコープで割り当てることができます。 Managed HSM のローカル RBAC では、HSM 全体 (`/` または `/keys`) およびキーごと (`/keys/<keyname>`) の 2 つのスコープがサポートされています。

すべての Managed HSM 組み込みロールと、それらで許可される操作のリストは、[Managed HSM の組み込みロール](built-in-roles.md)に関する記事で確認できます。

## <a name="prerequisites"></a>前提条件

この記事にある Azure CLI コマンドを使用するには、以下を用意する必要があります。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* Azure CLI バージョン 2.21.0 以降。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* お使いのサブスクリプション内のマネージド HSM。 「[クイック スタート:Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする](quick-create-cli.md)」を参照して、マネージド HSM をプロビジョニングしてアクティブにします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

## <a name="create-a-new-role-assignment"></a>新しいロールの割り当てを作成する

### <a name="assign-roles-for-all-keys"></a>すべてのキーを対象としたロールを割り当てる

ContosoHSM 内のすべての **キー** (スコープ `/keys`) を対象として、ユーザー プリンシパル名 **user2\@contoso.com** で識別されるユーザーに **Managed HSM 暗号化担当者** ロールを割り当てるには、`az keyvault role assignment create` コマンドを使用します。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>特定のキーを対象としたロールを割り当てる

**myrsakey** という名前の特定のキーを対象として、ユーザー プリンシパル名 **user2\@contoso.com** で識別されるユーザーに **Managed HSM 暗号化担当者** ロールを割り当てるには、`az keyvault role assignment create` コマンドを使用します。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>既存のロールの割り当てを一覧表示する

ロールの割り当てを一覧表示するには `az keyvault role assignment list` を使用します。

すべてのユーザー (--assignee が指定されていない場合に既定) を対象として、スコープが "/" (--scope が指定されていない場合に既定) である場合のすべてのロールの割り当て。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

特定のユーザー **user1@contoso.com** への、HSM レベルでのすべてのロールの割り当て。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> スコープが / (または /keys) の場合、list コマンドでは、最上位レベルのすべてのロールの割り当てが一覧表示されるのみで、個々のキー レベルのロールの割り当ては表示されません。

特定のキー **myrsakey** を対象とした、特定のユーザー **user2@contoso.com** へのすべてのロールの割り当て。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

特定のキー **myrsakey** を対象とした、特定のユーザー **user2@contoso.com** への **Managed HSM 暗号化担当者** ロールの割り当て。


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>ロールの割り当てを削除する

キー **myrsakey2** を対象として、ユーザー **user2\@contoso.com** に割り当てられた **Managed HSM 暗号化担当者** ロールを削除するには、`az keyvault role assignment delete` コマンドを使用します。

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>使用可能なすべてのロールの定義を一覧表示する

すべてのロールの定義を一覧表示するには、`az keyvault role definition list` コマンドを使用します。

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>新しいロール定義を作成する

マネージド HSM には、最も一般的な使用シナリオに役立ついくつかの組み込み (定義済み) のロールがあります。 ロールでの実行が許可される特定のアクションの一覧を使用して、独自のロールを定義できます。 次に、このロールをプリンシパルに割り当てることによって、指定されたアクションに対するアクセス許可をプリンシパルに付与することができます。 

JSON 文字列を使用して **My Custom Role** という名前のロールを作成するには、`az keyvault role definition create` コマンドを使用します。
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

ロール定義用の JSON 文字列を含む **my-custom-role-definition.json** という名前のファイルからロールを作成するには、`az keyvault role definition create` コマンドを使用します。 (上記の例を参照)。
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>ロール定義の詳細を表示する

名前 (GUID) を使用して特定のロールの定義の詳細を表示するには、`az keyvault role definition show` コマンドを使用します。

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>カスタムのロールの定義を更新する

JSON 文字列を使用して **My Custom Role** という名前のロールを更新するには、`az keyvault role definition update` コマンドを使用します。
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>カスタムのロール定義の削除

名前 (GUID) を使用して特定のロールの定義の詳細を表示するには、`az keyvault role definition delete` コマンドを使用します。 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> 組み込みロールは削除できません。 カスタム ロールを削除すると、そのカスタム ロールが使用されているすべてのロール割り当てが機能しなくなります。


## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) の概要を確認します。
- [Managed HSM のロール管理](role-management.md)に関するチュートリアルを確認します。
- [Managed HSM アクセス制御モデル](access-control.md)の詳細を確認します。
- すべての [Managed HSM のローカル RBAC の組み込みロール](built-in-roles.md)を確認します。

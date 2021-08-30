---
title: テーブル データ (プレビュー) にアクセスするための Azure ロールを割り当てる
titleSuffix: Azure Storage
description: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Active Directory セキュリティ プリンシパルに、テーブル データ (プレビュー) に対するアクセス許可を割り当てる方法について説明します。 Azure Storage では、Azure AD を使用した認証および認可用の組み込みロールと Azure カスタム ロールがサポートされています。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19c377c1b6d0b5cad7515ca199133f8d10c8742b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760603"
---
# <a name="assign-an-azure-role-for-access-to-table-data-preview"></a>テーブル データ (プレビュー) にアクセスするための Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Storage のテーブル データ (プレビュー) へのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールは、Azure Storage によって定義されます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

Azure AD を使用してテーブル データへのアクセスを認可する方法の詳細については、[Azure Active Directory を使用したテーブルへのアクセスの認可](authorize-access-azure-active-directory.md)に関するページを参照してください。

> [!IMPORTANT]
> Azure AD を使用したテーブルの認可は、現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="assign-an-azure-role"></a>Azure ロールを割り当てる

PowerShell、Azure CLI、または Azure Resource Manager テンプレートを使用して、データ アクセスのロールを割り当てることができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure ロールをセキュリティ プリンシパルに割り当てるには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドを呼び出します。 コマンドの形式は、割り当てのスコープによって異なります。 このコマンドを実行するには、対応するスコープ以上で割り当てられた **Microsoft.Authorization/roleAssignments/write** アクセス許可を含むロールが必要です。

テーブルに対してスコープが指定されたロールを割り当てるには、テーブルのスコープが含まれる文字列を `--scope` パラメーターに指定します。 テーブルのスコープの形式は次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

次の例では、テーブルに対してスコープが指定された **ストレージ テーブル データ共同作成者** ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Table Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

PowerShell を使用して、サブスクリプション、リソース グループ、またはストレージ アカウントをスコープとしたロールを割り当てる方法については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

セキュリティ プリンシパルに Azure ロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドを使用します。 コマンドの形式は、割り当てのスコープによって異なります。 コマンドの形式は、割り当てのスコープによって異なります。 このコマンドを実行するには、対応するスコープ以上で割り当てられた **Microsoft.Authorization/roleAssignments/write** アクセス許可を含むロールが必要です。

テーブルに対してスコープが指定されたロールを割り当てるには、テーブルのスコープが含まれる文字列を `--scope` パラメーターに指定します。 テーブルのスコープの形式は次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

次の例では、テーブルのレベルにスコープが指定された **ストレージ テーブル データ共同作成者** ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```azurecli-interactive
az role assignment create \
    --role "Storage Table Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

PowerShell を使用して、サブスクリプション、リソース グループ、またはストレージ アカウントをスコープとしたロールを割り当てる方法については、「[Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用して Azure ロールを割り当てる方法については、「[Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](../../role-based-access-control/role-assignments-template.md)」を参照してください。

---

Azure Storage での Azure ロールの割り当てについては、次の点に注意してください。

- Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の Azure ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、またはテーブルのレベルで割り当てることができます。
- ストレージ アカウントが Azure Resource Manager 読み取り専用ロックでロックされている場合、このロックによって、ストレージ アカウントまたはテーブルにスコープが設定された Azure ロールを割り当てることはできなくなります。
- Azure portal の Storage Explorer のプレビュー バージョンでは、テーブル データを表示して変更するために Azure AD 資格情報を使用することはサポートされていません。 Azure portal の Storage Explorer では、データは常にアカウント キーを使用してアクセスされます。 Azure portal で Storage Explorer を使用するには、**Microsoft. Storage/storageAccounts/listkeys/action** を含むロールが割り当てられている必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)
- [Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)

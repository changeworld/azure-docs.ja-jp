---
title: キュー データにアクセスするための Azure ロールを割り当てる
titleSuffix: Azure Storage
description: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Active Directory セキュリティ プリンシパルに、キュー データに対するアクセス許可を割り当てる方法について説明します。 Azure Storage では、Azure AD を使用した認証および認可用の組み込みロールと Azure カスタム ロールがサポートされています。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28185918667b35012bd5fd0cc6bb6785d92b29a5
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113761637"
---
# <a name="assign-an-azure-role-for-access-to-queue-data"></a>キュー データにアクセスするための Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Storage では、キュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールが定義されます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

Azure AD を使用してキュー データへのアクセスを承認する方法の詳細については、「[Azure Active Directory を使用してキューへのアクセスを承認する](authorize-access-azure-active-directory.md)」を参照してください。

> [!NOTE]
> この記事では、ストレージ アカウントのキュー データにアクセスするために Azure ロールを割り当てる方法について説明します。 Azure Storage での管理操作のロールの割り当てについては、「[Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする](../common/authorization-resource-provider.md)」を参照してください。

## <a name="assign-an-azure-role"></a>Azure ロールを割り当てる

Azure portal、PowerShell、Azure CLI、または Azure Resource Manager テンプレートを使用して、データ アクセスのロールを割り当てることができます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal で Azure AD の資格情報を使用してキュー データにアクセスするには、ユーザーに次のロールが割り当てられている必要があります。

- **ストレージ キュー データ共同作成者** などのデータ アクセス ロール
- Azure Resource Manager **閲覧者** ロール

これらのロールをユーザーに割り当てる方法については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」に記載されている手順に従ってください。

[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールは Azure Resource Manager のロールであり、ユーザーにストレージ アカウントのリソースの表示を許可しますが、変更は許可しません。 これは Azure Storage 内のデータに読み取りアクセス許可を提供しませんが、アカウント管理リソースに対してのみです。 ユーザーが Azure portal でキューやメッセージに移動できるようにするには、**閲覧者** ロールが必要です。

たとえば、**sample-queue** という名前のキューのレベルで、Mary というユーザーに **ストレージ キュー データ共同作成者** ロールを割り当てた場合、Mary にはそのキューに対する読み取り、書き込み、削除のアクセス権が付与されます。 ただし、Mary が Azure portal でキューを表示する必要がある場合、**ストレージ キュー データ共同作成者** ロール自体では、ポータル内でそのキューに移動して表示するための十分なアクセス許可が提供されません。 ポータル内を移動し、そこに表示されている他のリソースを表示するには、追加のアクセス許可が必要です。

Azure AD の資格情報で Azure portal を使用するには、ユーザーに **閲覧者** ロールを割り当てる必要があります。 ただし、**Microsoft.Storage/storageAccounts/listKeys/action** アクセス許可を含むロールがユーザーに割り当てられている場合、ユーザーは共有キーによる認可を経てストレージ アカウント キーでポータルを使用できます。 ストレージ アカウント キーを使用するには、ストレージ アカウントに対して共有キー アクセスを許可する必要があります。 共有キー アクセスを許可または禁止する方法の詳細については、「[Azure ストレージ アカウントの共有キーによる承認を禁止する](../common/shared-key-authorization-prevent.md)」を参照してください。

**閲覧者** ロール以外の追加のアクセス許可を提供する Azure Resource Manager ロールを割り当てることもできます。 セキュリティのベスト プラクティスとして、割り当てるアクセス許可を最小限にとどめることをお勧めします。 詳細については、「[Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)」を参照してください。

> [!NOTE]
> データ アクセスのためのロールを自分に割り当てる前に、Azure portal 経由でストレージ アカウントのデータにアクセスできるようになります。これは、Azure portal もデータ アクセスにアカウント キーを使用できるためです。 詳細については、「[Azure portal でキュー データへのアクセスの承認方法を選択する](../queues/authorize-data-operations-portal.md)」を参照してください。
>
> Azure portal の Storage Explorer のプレビュー バージョンでは、キュー データを表示および変更するための Azure AD 資格情報の使用はサポートされていません。 Azure portal の Storage Explorer では、データは常にアカウント キーを使用してアクセスされます。 Azure portal で Storage Explorer を使用するには、**Microsoft. Storage/storageAccounts/listkeys/action** を含むロールが割り当てられている必要があります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure ロールをセキュリティ プリンシパルに割り当てるには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドを呼び出します。 コマンドの形式は、割り当てのスコープによって異なります。 このコマンドを実行するには、対応するスコープ以上で割り当てられた **Microsoft.Authorization/roleAssignments/write** アクセス許可を含むロールが必要です。

キューに対してスコープが指定されたロールを割り当てるには、キューのスコープを含む文字列を `--scope` パラメーターに指定します。 キューのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

次の例では、*sample-queue* という名前のキューに対してスコープが指定された **ストレージ キュー データ共同作成者** ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

PowerShell を使用して、サブスクリプション、リソース グループ、またはストレージ アカウントをスコープとしたロールを割り当てる方法については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

セキュリティ プリンシパルに Azure ロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドを使用します。 コマンドの形式は、割り当てのスコープによって異なります。 コマンドの形式は、割り当てのスコープによって異なります。 このコマンドを実行するには、対応するスコープ以上で割り当てられた **Microsoft.Authorization/roleAssignments/write** アクセス許可を含むロールが必要です。

キューに対してスコープが指定されたロールを割り当てるには、キューのスコープを含む文字列を `--scope` パラメーターに指定します。 キューのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

次の例では、キューのレベルにスコープが指定された **ストレージ キュー データ共同作成者** ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

PowerShell を使用して、サブスクリプション、リソース グループ、またはストレージ アカウントをスコープとしたロールを割り当てる方法については、「[Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用して Azure ロールを割り当てる方法については、「[Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](../../role-based-access-control/role-assignments-template.md)」を参照してください。

---

Azure Storage での Azure ロールの割り当てについては、次の点に注意してください。

- Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の Azure ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、またはキューのレベルで割り当てることができます。
- ストレージ アカウントが Azure Resource Manager 読み取り専用ロックでロックされている場合、このロックによって、スコープがストレージ アカウントまたはキューに設定された Azure ロールを割り当てることはできなくなります。

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)
- [Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)
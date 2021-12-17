---
title: BLOB データにアクセスするための Azure ロールを割り当てる
titleSuffix: Azure Storage
description: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Active Directory セキュリティ プリンシパルに、BLOB データに対するアクセス許可を割り当てる方法について説明します。 Azure Storage では、Azure AD を使用した認証および認可用の組み込みロールと Azure カスタム ロールがサポートされています。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6d2d2f530d64e3c459e0363079bebc9f99d44cf3
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578821"
---
# <a name="assign-an-azure-role-for-access-to-blob-data"></a>BLOB データにアクセスするための Azure ロールを割り当てる

Azure Active Directory (AAD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Storage では、BLOB データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールが定義されます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

Azure AD を使用して BLOB データへのアクセスを認可する方法の詳細については、[Azure Active Directory を使用して BLOB へのアクセスを認可する](authorize-access-azure-active-directory.md)ことに関する記事を参照してください。

> [!NOTE]
> この記事では、ストレージ アカウントの BLOB データにアクセスするために Azure ロールを割り当てる方法について説明します。 Azure Storage での管理操作のロールの割り当てについては、「[Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする](../common/authorization-resource-provider.md)」を参照してください。

## <a name="assign-an-azure-role"></a>Azure ロールを割り当てる

Azure portal、PowerShell、Azure CLI、または Azure Resource Manager テンプレートを使用して、データ アクセスのロールを割り当てることができます。

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Azure portal で Azure AD の資格情報を使用して BLOB データにアクセスするには、ユーザーに次のロールが割り当てられている必要があります。

- **ストレージ BLOB データ共同作成者** などのデータ アクセス ロール
- Azure Resource Manager **閲覧者** ロール

これらのロールをユーザーに割り当てる方法については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」に記載されている手順に従ってください。

[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールは Azure Resource Manager のロールであり、ユーザーにストレージ アカウントのリソースの表示を許可しますが、変更は許可しません。 これは Azure Storage 内のデータに読み取りアクセス許可を提供しませんが、アカウント管理リソースに対してのみです。 ユーザーが Azure portal 内の BLOB コンテナーに移動できるようにするには、**閲覧者** ロールが必要です。

たとえば、**ストレージ BLOB データ共同作成者** ロールを **sample-container** という名前のコンテナーのレベルで Mary というユーザーに割り当てると、Mary にはそのコンテナー内のすべての BLOB に対する読み取り、書き込み、および削除のアクセス権が付与されます。 ただし、Mary が Azure portal で BLOB を表示する場合、**ストレージ BLOB データ共同作成者** ロール自体は、BLOB を表示するためにポータル内でその BLOB に移動するための十分なアクセス許可を提供しません。 ポータル内を移動し、そこに表示されている他のリソースを表示するには、追加のアクセス許可が必要です。

Azure AD の資格情報で Azure portal を使用するには、ユーザーに **閲覧者** ロールを割り当てる必要があります。 ただし、**Microsoft.Storage/storageAccounts/listKeys/action** アクセス許可を含むロールがユーザーに割り当てられている場合、ユーザーは共有キーによる認可を経てストレージ アカウント キーでポータルを使用できます。 ストレージ アカウント キーを使用するには、ストレージ アカウントに対して共有キー アクセスを許可する必要があります。 共有キー アクセスを許可または禁止する方法の詳細については、「[Azure ストレージ アカウントの共有キーによる承認を禁止する](../common/shared-key-authorization-prevent.md)」を参照してください。

**閲覧者** ロール以外の追加のアクセス許可を提供する Azure Resource Manager ロールを割り当てることもできます。 セキュリティのベスト プラクティスとして、割り当てるアクセス許可を最小限にとどめることをお勧めします。 詳細については、「[Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)」を参照してください。

> [!NOTE]
> データ アクセスのためのロールを自分に割り当てる前に、Azure portal 経由でストレージ アカウントのデータにアクセスできるようになります。これは、Azure portal もデータ アクセスにアカウント キーを使用できるためです。 詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../blobs/authorize-data-operations-portal.md)」を参照してください。
>
> Azure portal の Storage Explorer のプレビュー バージョンでは、BLOB データを表示および変更するための Azure AD 資格情報の使用はサポートされていません。 Azure portal の Storage Explorer では、データは常にアカウント キーを使用してアクセスされます。 Azure portal で Storage Explorer を使用するには、**Microsoft. Storage/storageAccounts/listkeys/action** を含むロールが割り当てられている必要があります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して Azure ロールをセキュリティ プリンシパルに割り当てるには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドを呼び出します。 このコマンドを実行するには、対応するスコープ以上で割り当てられた **Microsoft.Authorization/roleAssignments/write** アクセス許可を含むロールが必要です。 

コマンドの形式は、割り当てのスコープによって異なりますが、`-ObjectId` と `-RoleDefinitionName` は必須のパラメーターです。 必須ではありませんが、最小限の特権の原則を保持するために、`-Scope` パラメーターの値を渡すことを強くお勧めします。 ロールとスコープを制限することで、セキュリティ プリンシパルが侵害された場合にリスクにさらされるリソースを制限することができます。

`-ObjectId` パラメーターは、ロールが割り当てられるユーザー、グループ、またはサービス プリンシパルの Azure Active Directory (AAD) オブジェクト ID です。 ID を取得するには、次の例に示すように、[Get AzADUser](/powershell/module/az.resources/get-azaduser) を使用して Azure Active Directory ユーザーをフィルター処理できます。

```azurepowershell
Get-AzADUser -DisplayName '<Display Name>'
(Get-AzADUser -StartsWith '<substring>').Id
```

最初の応答はセキュリティ プリンシパルを返し、2 番目の応答はセキュリティ プリンシパルのオブジェクト ID を返します。

```Response
UserPrincipalName : markpdaniels@contoso.com
ObjectType        : User
DisplayName       : Mark P. Daniels
Id                : ab12cd34-ef56-ab12-cd34-ef56ab12cd34
Type              : 

ab12cd34-ef56-ab12-cd34-ef56ab12cd34
```

`-RoleDefinitionName` パラメーター値は、プリンシパルに割り当てる必要がある RBAC ロールの名前です。 Azure portal で Azure AD の資格情報を使用して BLOB データにアクセスするには、ユーザーに次のロールが割り当てられている必要があります。

- **ストレージ BLOB データ共同作成者**、**ストレージ BLOB データ閲覧者** などのデータ アクセス ロール
- Azure Resource Manager **閲覧者** ロール

BLOB コンテナーまたはストレージ アカウントにスコープ指定されたロールを割り当てるには、`-Scope` パラメーターのリソースのスコープを含む文字列を指定する必要があります。 このアクションは、最小限の特権の原則に準拠しています。これは、ユーザーがジョブ機能を実行するために必要な最小限のアクセス レベルを付与される情報セキュリティの概念です。 この実践により、不要な特権によって発生する可能性のある偶発的または意図的な破損のリスクが軽減されます。

コンテナーのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

ストレージ アカウントのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

ストレージ アカウントに対してスコープが指定されたロールを割り当てるには、コンテナーのスコープを含む文字列を `--scope` パラメーターに指定します。

次の例では、*sample-container* という名前のコンテナーにスコープが指定された **ストレージ BLOB データ共同作成者** ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

次の例では、オブジェクト ID を指定することによって、**ストレージ BLOB データ閲覧者** ロールをユーザーに割り当てます。 ロールの割り当ては、**storage-account** という名前のストレージ アカウントにスコープが設定されています。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。 

```powershell
New-AzRoleAssignment -ObjectID "ab12cd34-ef56-ab12-cd34-ef56ab12cd34" `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storage-account"
```

出力は次のようになります。

```Response
RoleAssignmentId   : /subscriptions/<subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.Storage/storageAccounts/<Storage Account>/providers/Microsoft.Authorization/roleAssignments/<Role Assignment ID>
Scope              : /subscriptions/<subscription ID>/resourceGroups/<Resource Group>/providers/Microsoft.Storage/storageAccounts/<Storage Account>
DisplayName        : Mark Patrick
SignInName         : markpdaniels@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : <Role Definition ID>
ObjectId           : <Object ID>
ObjectType         : User
CanDelegate        : False
```

PowerShell を使用して、サブスクリプションまたはリソース グループをスコープとしたロールを割り当てる方法については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してセキュリティ プリンシパルに Azure ロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドを使用します。 コマンドの形式は、割り当てのスコープによって異なります。 コマンドの形式は、割り当てのスコープによって異なります。 このコマンドを実行するには、対応するスコープ以上で割り当てられた **Microsoft.Authorization/roleAssignments/write** アクセス許可を含むロールが必要です。

コンテナーに対してスコープが指定されたロールを割り当てるには、コンテナーのスコープを含む文字列を `--scope` パラメーターに指定します。 コンテナーのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

次の例では、コンテナーのレベルにスコープが指定された **ストレージ BLOB データ共同作成者** ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

PowerShell を使用して、サブスクリプション、リソース グループ、またはストレージ アカウントをスコープとしたロールを割り当てる方法については、「[Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用して Azure ロールを割り当てる方法については、「[Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](../../role-based-access-control/role-assignments-template.md)」を参照してください。

---

Azure Storage での Azure ロールの割り当てについては、次の点に注意してください。

- Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の Azure ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいはコンテナーのレベルで割り当てることができます。
- ストレージ アカウントが Azure Resource Manager 読み取り専用ロックでロックされている場合、このロックによって、スコープがストレージ アカウントまたはコンテナーに設定された Azure ロールを割り当てることはできなくなります。
- Azure AD 経由でデータにアクセスするための適切なアクセス許可を設定し、データにアクセスできない場合 (たとえば、"AuthorizationPermissionMismatch" エラーが発生している場合)。 Azure AD で行ったアクセス許可の変更がレプリケートされるのに十分な時間を割り当て、アクセスをブロックする拒否割り当てが存在しないことを確認します。「[Azure 拒否割り当てについて](../../role-based-access-control/deny-assignments.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)
- [Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)

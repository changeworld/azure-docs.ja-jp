---
title: Azure ファイル共有へのアクセスを制御する - オンプレミス AD DS 認証
description: ストレージ アカウントを表す Active Directory Domain Services ID にアクセス許可を割り当てる方法について説明します。 これにより、ID ベースの認証を使用してアクセスを制御できます。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/13/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: cf2d9c2921599680781695631eae9c5276ff53c2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400646"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>パート 2: ID に共有レベルのアクセス許可を割り当てる

この記事を開始する前に、前の記事「[アカウントへの AD DS 認証を有効にする](storage-files-identity-ad-ds-enable.md)」を完了しておいてください。

ストレージ アカウントで Active Directory Domain Services (AD DS) 認証を有効にしたら、ファイル共有にアクセスするために、共有レベルのアクセス許可を構成する必要があります。 共有レベルのアクセス許可を割り当てる方法は 2 つあります。 それらは特定の Azure AD ユーザーやユーザー グループに割り当てることができるほか、既定の共有レベルのアクセス許可として、すべての認証済み ID に割り当てることができます。

> [!IMPORTANT]
> ファイルの所有権を引き受ける機能を含めて、ファイル共有を完全に管理制御するには、ストレージ アカウント キーを使用する必要があります。 Azure AD 資格情報を使用した管理制御はサポートされていません。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="which-configuration-should-you-use"></a>どの構成を使用する必要があるか

ほとんどのユーザーは、共有レベルのアクセス許可を特定の Azure AD ユーザーまたはグループに割り当て、その後 Windows ACL を使用してディレクトリおよびファイル レベルできめ細かくアクセスを制御する必要があります。 これは最も厳重でセキュリティで保護された構成です。

代わりに、すべての認証済み ID に割り当てられている、既定の共有レベルのアクセス許可を使用することを推奨するシナリオが 3 つあります。

- オンプレミスの AD DS を Azure AD に同期できない。その場合は、代わりに既定の共有レベルのアクセス許可を使用できます。 既定の共有レベルのアクセス許可を割り当てると、Azure AD で ID に対するアクセス許可を指定する必要がないため、同期要件を回避できます。 その後、ファイルやディレクトリに対して Windows ACL を使用して、アクセス許可をよりきめ細かく適用できます。
- 使用しているオンプレミスの AD DS が、ファイル共有がデプロイされている Azure AD とは別の Azure AD に同期されている。
    - これは、マルチテナント環境を管理するときに一般的です。 既定の共有レベルのアクセス許可を使用すると、Azure AD ハイブリッド ID の要件を回避できます。 その場合でもファイルやディレクトリに対して Windows ACL を使用してアクセス許可をよりきめ細かく適用できます。
- ファイルとディレクトリ レベルで Windows ACL のみを使用して認証を適用したほうがよい。 

## <a name="share-level-permissions"></a>共有レベルのアクセス許可

次の表に、共有レベルのアクセス許可と、それらに対応する組み込みの RBAC ロールを示します。

|サポートされている組み込みロール  |説明  |
|---------|---------|
|[ストレージ ファイル データの SMB 共有の閲覧者](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-reader)     |Azure ファイル共有のファイルまたはディレクトリに対する読み取りアクセスを許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の読み取りに相当します。 [詳細については、こちらを参照してください](storage-files-identity-auth-active-directory-enable.md)。         |
|[記憶域ファイル データの SMB 共有の共同作成者](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-contributor)     |Azure ファイル共有のファイルまたはディレクトリに対する読み取り、書き込み、削除アクセスを許可します。 [詳細については、こちらを参照してください](storage-files-identity-auth-active-directory-enable.md)。         |
|[記憶域ファイル データの SMB 共有の管理者特権共同作成者](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-elevated-contributor)     |Azure ファイル共有のファイルまたはディレクトリに対する ACL の読み取り、書き込み、削除、変更を許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の変更に相当します。 [詳細については、こちらを参照してください](storage-files-identity-auth-active-directory-enable.md)。         |


## <a name="share-level-permissions-for-specific-azure-ad-users-or-groups"></a>特定の Azure AD ユーザーまたはグループに対する共有レベルのアクセス許可

特定の Azure AD ユーザーまたはグループを使用して Azure ファイル共有のリソースにアクセスする場合、その ID は、**オンプレミスの AD DS と Azure AD の両方に存在するハイブリッド ID** である必要があります。 たとえば、ご使用の AD に user1@onprem.contoso.com というユーザーがいて、Azure AD Connect 同期を使用して user1@contoso.com として Azure AD に同期しているとします。このユーザーが Azure Files にアクセスするには、user1@contoso.com に共有レベルのアクセス許可を割り当てる必要があります。 グループまたはサービス プリンシパルにも同じ概念が適用されます。 このため、Azure AD Connect 同期を使用して、ユーザーとグループを AD から Azure AD に同期する必要があります。 

Azure ファイル共有への AD DS 認証をサポートするために、共有レベルのアクセス許可を、AD DS 内の同じユーザーまたはグループを表す Azure AD ID に割り当てる必要があります。 Azure Managed Identities (MSIs) など、Azure AD にのみ存在する ID に対する認証と承認は、AD DS 認証ではサポートされていません。

Azure portal、Azure PowerShell モジュール、または Azure CLI を使用して、共有レベルのアクセス許可を付与するために、組み込みのロールをユーザーの Azure AD ID に割り当てることができます。

> [!IMPORTANT]
> 共有レベルのアクセス許可は、完了してから有効になるまで最大 3 時間かかります。 資格情報を使用してファイル共有に接続する前に、アクセス許可が同期されるまで待ってください   

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure portal](https://portal.azure.com) を使用して Azure ロールを Azure AD ID に割り当てるには、これらの手順に従います。

1. Azure portal でファイル共有に移動するか、[ファイル共有を作成](storage-how-to-create-file-share.md)します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当てを追加する]** を選択します。
1. **[ロールの割り当てを追加する]** ブレードで、 **[ロール]** の一覧から [適切な組み込みロール](#share-level-permissions)を選択します。
    1. 記憶域ファイル データの SMB 共有の閲覧者
    1. 記憶域ファイル データの SMB 共有の共同作成者
    1. ストレージ ファイル データの SMB 共有の管理者特権の共同作成者
1.  **[アクセスの割り当て先]** は既定の設定のままにしておきます。**Azure AD のユーザー、グループ、サービス プリンシパル**。 名前またはメール アドレスで、ターゲット Azure AD ID を選択します。 **選択した Azure AD ID は、ハイブリッド ID である必要があり、クラウドのみの ID にすることはできません。** これは、同じ ID が AD DS でも表示されることを意味します。
1. **[保存]** を選択して、ロールの割り当て操作を完了します。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

次の PowerShell サンプルは、Azure ロールをサインイン名に基づいて Azure AD ID に割り当てる方法を示しています。 PowerShell で Azure ロールを割り当てる方法の詳細については、[Azure PowerShell モジュールを使用した Azure ロールの割り当ての追加または削除](../../role-based-access-control/role-assignments-powershell.md)に関するページをご覧ください。

次のサンプル スクリプトを実行する前に、プレースホルダー値 (かっこを含む) を独自の値に置き換えます。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
次の CLI 2.0 コマンドでは、Azure ロールをサインイン名に基づいて Azure AD ID に割り当てています。 Azure CLI で Azure ロールを割り当てる方法の詳細については、「[Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)」をご覧ください。 

次のサンプル スクリプトを実行する前に、プレースホルダー値 (かっこを含む) を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="share-level-permissions-for-all-authenticated-identities"></a>すべての認証済み ID に対する共有レベルのアクセス許可

Azure AD のユーザーまたはグループに対して共有レベルのアクセス許可を構成するのではなく、ストレージ アカウントに既定の共有レベルのアクセス許可を追加することができます。 ストレージ アカウントに割り当てられた既定の共有レベルのアクセス許可は、そのストレージ アカウントに含まれるすべてのファイル共有に適用されます。 

既定の共有レベルのアクセス許可を設定すると、認証されたすべてのユーザーとグループに同じアクセス許可が付与されます。 認証済みのユーザーやグループは、そのストレージ アカウントが関連付けられているオンプレミスの AD DS に対して認証できる ID として識別されます。 初期化すると、既定の共有レベルのアクセス許可は **[なし]** に設定されます。これは、Azure ファイル共有内のファイルやディレクトリへのアクセスが許可されていないことを示します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

現時点では、Azure portal を使用してストレージ アカウントにアクセス許可を割り当てることはできません。 代わりに Azure PowerShell モジュールまたは Azure CLI を使用してください。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

次のスクリプトを使用して、ストレージ アカウントに対する既定の共有レベルのアクセス許可を構成できます。 Files 認証用のディレクトリ サービスに関連付けられているストレージ アカウントに対してのみ、既定の共有レベルのアクセス許可を有効にすることができます。 

次のスクリプトを実行する前に、Az.Storage モジュールのバージョンが 3.7.0 以降であることを確認してください。

```azurepowershell
$defaultPermission = "None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice

$account = Set-AzStorageAccount -ResourceGroupName "<resource-group-name-here>" -AccountName "<storage-account-name-here>" -DefaultSharePermission $defaultPermission

$account.AzureFilesIdentityBasedAuth
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次のスクリプトを使用して、ストレージ アカウントに対する既定の共有レベルのアクセス許可を構成できます。 Files 認証用のディレクトリ サービスに関連付けられているストレージ アカウントに対してのみ、既定の共有レベルのアクセス許可を有効にすることができます。 

次のスクリプトを実行する前に、Azure CLI のバージョンが 2.24.1 以降であることを確認してください。

```azurecli
# Declare variables
storageAccountName="YourStorageAccountName"
resourceGroupName="YourResourceGroupName"
defaultPermission="None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice


az storage account update --name $storageAccountName --resource-group $resourceGroupName --default-share-permission $defaultPermission
```
---

## <a name="what-happens-if-you-use-both-configurations"></a>両方の構成を使用した場合の動作

アクセス許可は、認証済みのすべての Azure AD ユーザーに割り当てることも、特定の Azure AD ユーザーやグループに割り当てることもできます。 この構成では、特定のユーザーまたはグループが、既定の共有レベルのアクセス許可と、RBAC 割り当てから許可されたアクセス許可のスーパーセットを持つことになります。 このしくみを理解するための例を紹介します。あるユーザーにターゲットのファイル共有に対する **[記憶域ファイル データの SMB 共有の閲覧者]** を付与したとします。 また、認証済みのすべてのユーザーに、既定の共有レベルのアクセス許可 **[ストレージ ファイル データの SMB 共有の管理者特権の共同作成者]** を付与しました。 この構成では、その特定のユーザーは、ファイル共有に対する **[ストレージ ファイル データの SMB 共有の管理者特権の共同作成者]** レベルのアクセス権限を持ちます。 より高いレベルのアクセス許可が常に優先されます。

## <a name="next-steps"></a>次のステップ

共有レベルのアクセス許可を割り当てたので、ディレクトリとファイル レベルのアクセス許可を構成する必要があります。 次の記事に進みます。

[パート 3: SMB 経由でディレクトリとファイル レベルのアクセス許可を構成する](storage-files-identity-ad-ds-configure-permissions.md)

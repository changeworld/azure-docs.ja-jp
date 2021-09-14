---
title: RBAC を使用してギャラリーを共有する
description: ロールベースのアクセス制御 (RBAC) を使用してギャラリーを共有する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 01c98ba8d619573c241e5a8fdfa2f774b4aed22d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452346"
---
# <a name="use-rbac-to-share-image-gallery-resources"></a>RBAC を使用してイメージ ギャラリーリソースを共有する

共有イメージ ギャラリー、イメージ定義、イメージ バージョンはすべてリソースであるため、組み込みのネイティブ Azure RBAC コントロールを使用して共有できます。 Azure RBAC を使用して、他のユーザー、サービス プリンシパル、グループなどに、これらのリソースを共有できます。 それらが作成されたテナントの外部の個人とアクセスを共有することもできます。 共有されたイメージ バージョンにアクセスできるユーザーは、VM または仮想マシン スケール セットをデプロイできます。  

最良のエクスペリエンスのため、ギャラリー レベルで共有することをお勧めします。 個別のイメージ バージョンの共有はお勧めできません。 Azure RBAC の詳細については、[Azure のロールの割り当て](../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

ユーザーが組織外の場合は、組織に参加するための招待メールを受け取ります。 そのユーザーは招待に同意する必要があり、その後ギャラリーとリソースの一覧にイメージの定義とバージョンをすべて表示できるようになります。

## <a name="share-a-gallery"></a>ギャラリーを共有する

### <a name="portal"></a>[ポータル](#tab/portal)

ユーザーが組織外の場合は、組織に参加するための招待メールを受け取ります。 そのユーザーは招待に同意する必要があり、その後ギャラリーとリソースの一覧にイメージの定義とバージョンをすべて表示できるようになります。

1. イメージ ギャラリーのページで、左側のメニューにある **[アクセス制御 (IAM)]** を選択します。 
1. **[ロールの割り当てを追加する]** で **[追加]** を選択します。 **[ロールの割り当てを追加する]** ウィンドウが開きます。 
1. **[ロール]** で **[閲覧者]** を選択します。
1. **[アクセスの割り当て先]** で既定値の **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにします。
1. **[選択]** の下で、招待する人のメール アドレスを入力します。
1. ユーザーが組織外の場合、"**このユーザーには、Microsoft で共同作業できるようにするメールが送信されます**" というメッセージが表示されます。 メール アドレスでユーザーを選択して、 **[保存]** をクリックします。


### <a name="cli"></a>[CLI](#tab/cli)

ギャラリーのオブジェクト ID を取得するには、[az sig show](/cli/azure/sig#az_sig_show) を使用します。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

電子メール アドレスおよび [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) と共に、オブジェクト ID をスコープとして使用して、ユーザーに共有イメージ ギャラリーへのアクセスを付与します。 `<email-address>` と `<gallery iD>` は、実際の情報に置き換えてください。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

RBAC を使用してリソースを共有する方法の詳細については、「[RBAC と Azure CLI を使用してアクセスを管理する](../role-based-access-control/role-assignments-cli.md)」を参照してください。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

電子メール アドレスと [Get-AzADUser](/powershell/module/az.resources/get-azaduser) コマンドレットを使用して、ユーザーのオブジェクト ID を取得し、[New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) を使用して、ギャラリーへのアクセス権を付与します。 この例のサンプル電子メール alinne_montes@contoso.com を独自の情報に置き換えます。

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

---


## <a name="next-steps"></a>次のステップ

[イメージ定義とイメージ バージョン](image-version.md)を作成します。

[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./windows/image-builder-gallery-update-image-version.md)に使用することさえできます。 

共有イメージ ギャラリー リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

---
title: Azure の予約を表示および管理するためのアクセス許可
description: Azure portal で Azure の予約を表示および管理する方法について説明します。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 5ee6972ae409a2912f677c0411daf01aaa96f6f7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464701"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Azure の予約を表示および管理するためのアクセス許可

この記事では、予約のアクセス許可のしくみと、ユーザーが Azure portal で、および Azure PowerShell を使用して、Azure の予約を表示して管理する方法について説明します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="who-can-manage-a-reservation-by-default"></a>既定で予約を管理できるユーザー

既定では、次のユーザーが予約を表示および管理できます。

- 予約を購入した担当者と、予約の購入に使用した課金サブスクリプションのアカウント管理者が、予約注文に追加されます。
- マイクロソフト エンタープライズ契約および Microsoft 顧客契約の課金管理者。
- すべての Azure サブスクリプションと管理グループを管理するための昇格されたアクセス権を持つユーザー
- Azure Active Directory (Azure AD) テナント (ディレクトリ) の予約の予約管理者
- Azure Active Directory テナント (ディレクトリ) の予約に対する読み取り専用アクセス権を持つ予約閲覧者

現時点で、予約管理者ロールと予約閲覧者ロールで可能なのは、PowerShell を使用して割り当てることのみです。 Azure portal では、表示することも、割り当てることもできません。 詳細については、「[PowerShell でアクセス権を付与する](#grant-access-with-powershell)」を参照してください。

予約のライフサイクルは Azure サブスクリプションに依存しないため、予約は Azure サブスクリプションに属するリソースではありません。 そうではなく、サブスクリプションとは別の独自の Azure RBAC アクセス許可があるテナントレベルのリソースです。 予約には、購入後にサブスクリプションからアクセス許可が継承されません。

## <a name="view-and-manage-reservations"></a>予約の表示と管理

課金管理者の場合は、次の手順を使用して、Azure portal ですべての予約と予約トランザクションを表示および管理します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[Cost Management + Billing]** に移動します。
    - EA 管理者の場合は、左側のメニューで **[課金スコープ]** を選択し、課金スコープの一覧でスコープを選択します。
    - Microsoft 顧客契約の課金プロファイル所有者の場合は、左側のメニューで **[課金プロファイル]** を選択します。 課金プロファイルの一覧でプロファイルを選択します。
1. 左側のメニューで、 **[製品とサービス]**  >  **[予約]** を選択します。
1. EA 加入契約または課金プロファイルの予約の完全な一覧が表示されます。
1. 課金管理者は、1 つまたは複数の予約を選択し、 **[アクセスの付与]** をクリックし、表示されたウィンドウで **[アクセスの付与]** を選択することで、予約の所有権を取得できます。

### <a name="add-billing-administrators"></a>課金管理者を追加する

Azure portal で、ユーザーを課金管理者としてマイクロソフト エンタープライズ契約または Microsoft 顧客契約に追加します。

- マイクロソフト エンタープライズ契約の場合は、"_エンタープライズ管理者_" ロールを持つユーザーを追加することで、マイクロソフト エンタープライズ契約に適用されるすべての予約注文を表示および管理できます。 エンタープライズ管理者は、 **[コストの管理と請求]** で予約を表示および管理できます。
    - "_エンタープライズ管理者 (読み取り専用)_ " ロールを持つユーザーは、 **[コストの管理と請求]** から予約の表示のみを実行できます。 
    - 部門管理者およびアカウント所有者は、アクセス制御 (IAM) を使用して明示的にこれらに追加されて "_いない限り_"、予約を表示することはできません。 詳細については、「[Azure エンタープライズ ロールの管理](../manage/understand-ea-roles.md)」を参照してください。
- Microsoft 顧客契約の場合は、課金プロファイル所有者ロールまたは課金プロファイル共同作成者ロールを持つユーザーが、課金プロファイルを使用して行われたすべての予約購入を管理できます。 課金プロファイル閲覧者と請求書管理者は、請求プロファイルを使用して支払われるすべての予約を表示できます。 ただし、予約に変更を加えることはできません。
    詳細については、「[課金プロファイルのロールとタスク](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

## <a name="view-reservations-with-azure-rbac-access"></a>Azure RBAC アクセス権がある予約を表示する

予約を購入した場合、または自分が予約に追加された場合は、Azure portal で次の手順を使用して予約を表示および管理します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]**  >  **[予約]** の順に選択し、自分にアクセス権がある予約を一覧表示します。

## <a name="manage-subscriptions-and-management-groups-with-elevated-access"></a>昇格されたアクセス権でサブスクリプションと管理グループを管理する

ユーザーの[アクセス権を昇格して、すべての Azure サブスクリプションと管理グループを管理](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)できます。

アクセス権を昇格した後:

1. **[すべてのサービス]**  >  **[予約]** に移動して、テナント内のすべての予約を表示します。
1. 予約に変更を加えるには、アクセス制御 (IAM) を使用して自分自身を予約注文の所有者として追加します。

## <a name="grant-access-to-individual-reservations"></a>個々の予約へのアクセス権を付与する

予約の所有者アクセス権を持つユーザーと課金管理者は、Azure portal で個々の予約注文のアクセス管理を委任できます。

他のユーザーが予約を管理できるようにするには、次の 2 つのオプションがあります。

- 予約注文のリソース スコープのユーザーに Owner ロールを割り当てることにより、個別の予約注文に対するアクセス管理を委任する。 制限付きアクセス権を付与する場合は、別のロールを選択します。  
     詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

- ユーザーを課金管理者としてマイクロソフト エンタープライズ契約または Microsoft 顧客契約に追加するには、次の手順を実行します。
    - マイクロソフト エンタープライズ契約の場合は、"_エンタープライズ管理者_" ロールを持つユーザーを追加することで、マイクロソフト エンタープライズ契約に適用されるすべての予約注文を表示および管理できます。 "_エンタープライズ管理者 (読み取り専用)_ " ロールを持つユーザーは、予約の表示のみを実行できます。 部門管理者およびアカウント所有者は、アクセス制御 (IAM) を使用して明示的にこれらに追加されて "_いない限り_"、予約を表示することはできません。 詳細については、「[Azure エンタープライズ ロールの管理](../manage/understand-ea-roles.md)」を参照してください。

        "_エンタープライズ管理者は、予約注文の所有権を取得し、アクセス制御 (IAM) を使用して他のユーザーを予約に追加することができます。_ "
    - Microsoft 顧客契約の場合は、課金プロファイル所有者ロールまたは課金プロファイル共同作成者ロールを持つユーザーが、課金プロファイルを使用して行われたすべての予約購入を管理できます。 課金プロファイル閲覧者と請求書管理者は、請求プロファイルを使用して支払われるすべての予約を表示できます。 ただし、予約に変更を加えることはできません。
    詳細については、「[課金プロファイルのロールとタスク](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

## <a name="grant-access-with-powershell"></a>PowerShell でアクセス権を付与する

予約注文に対する所有者アクセス権を持つユーザー、昇格されたアクセス権を持つユーザー、[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)は、アクセスできるすべての予約注文のアクセス管理を委任できます。

PowerShell を使用して付与されたアクセス権は、Azure portal には表示されません。 代わりに、次のセクションの `get-AzRoleAssignment` コマンドを使用して、割り当てられたロールを表示します。

## <a name="assign-the-owner-role-for-all-reservations"></a>すべての予約に所有者ロールを割り当てる

次の Azure PowerShell スクリプトを使用して、ユーザーに Azure AD テナント (ディレクトリ) 内のすべての予約注文に対する Azure RBAC アクセス権を付与します。

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources
 
Connect-AzAccount -Tenant <TenantId>
 
$response = Invoke-AzRestMethod -Path /providers/Microsoft.Capacity/reservations?api-version=2020-06-01 -Method GET
 
$responseJSON = $response.Content | ConvertFrom-JSON
 
$reservationObjects = $responseJSON.value
 
foreach ($reservation in $reservationObjects)
{
  $reservationOrderId = $reservation.id.substring(0, 84)
  Write-Host "Assigning Owner role assignment to "$reservationOrderId
  New-AzRoleAssignment -Scope $reservationOrderId -ObjectId <ObjectId> -RoleDefinitionName Owner
}
```

PowerShell スクリプトを使用して所有権のロールを割り当て、正常に実行した場合、成功メッセージは返されません。 ただし、ロールが割り当てられたことは以下により確認できます。

```azurepowershell
get-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" |?{$_.RoleDefinitionName -Like "Reservations*"}
```

### <a name="parameters"></a>パラメーター

**-ObjectId**  ユーザー、グループ、またはサービス プリンシパルの Azure AD ObjectId。
- 型: String
- エイリアス: Id、PrincipalId
- 位置: Named
- 既定値: なし
- パイプライン入力を許可する: True
- ワイルドカード文字を許可する:   False

**-TenantId** テナントの一意識別子。
- 型: String
- 位置: 5
- 既定値: なし
- パイプライン入力を許可する: False
- ワイルドカード文字を許可する: False

[ユーザーアクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator) は、予約管理者ロールと予約閲覧者ロールにユーザーを追加できます。

## <a name="add-a-reservation-administrator-role-at-the-tenant-level"></a>予約管理者ロールをテナント レベルで追加する

PowerShell を使用して予約管理者ロールをテナント レベルで追加するには、次の Azure PowerShell スクリプトを使用します。

```azurepowershell
Import-Module Az.Accounts
Import-Module Az.Resources
Connect-AzAccount -Tenant <TenantId>
New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Administrator"
```

### <a name="parameters"></a>パラメーター

**-ObjectId** ユーザー、グループ、またはサービス プリンシパルの Azure AD ObjectId。
- 型: String
- エイリアス: Id、PrincipalId
- 位置: Named
- 既定値: なし
- パイプライン入力を許可する: True
- ワイルドカード文字を許可する: False

**-TenantId** テナントの一意識別子。
- 型: String
- 位置: 5
- 既定値: なし
- パイプライン入力を許可する: False
- ワイルドカード文字を許可する: False

## <a name="assign-a-reservation-reader-role-at-the-tenant-level"></a>予約閲覧者ロールをテナント レベルで割り当てる

PowerShell を使用して予約閲覧者ロールをテナント レベルで割り当てるには、次の Azure PowerShell スクリプトを使用します。

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources

Connect-AzAccount -Tenant <TenantId>

New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Reader"
```

### <a name="parameters"></a>パラメーター

**-ObjectId** ユーザー、グループ、またはサービス プリンシパルの Azure AD ObjectId。
- 型: String
- エイリアス: Id、PrincipalId
- 位置: Named
- 既定値: なし
- パイプライン入力を許可する: True
- ワイルドカード文字を許可する: False

**-TenantId** テナントの一意識別子。
- 型: String
- 位置: 5
- 既定値: なし
- パイプライン入力を許可する: False
- ワイルドカード文字を許可する: False


## <a name="next-steps"></a>次のステップ

- [Azure の予約を管理する](manage-reserved-vm-instance.md)。

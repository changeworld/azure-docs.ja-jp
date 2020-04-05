---
title: Azure PowerShell を使用して Azure リソースの拒否割り当てを一覧表示する
description: Azure PowerShell を使用して、特定のスコープの特定の Azure リソース アクションへのアクセスが拒否されているユーザー、グループ、サービス プリンシパル、およびマネージド ID を一覧表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137393"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Azure PowerShell を使用して Azure リソースの拒否割り当てを一覧表示する

[拒否割り当て](deny-assignments.md)を使用すると、ロールの割り当てでアクセスを許可されている場合であっても、指定した Azure リソース アクションをユーザーが実行できなくなります。 この記事では、Azure PowerShell を使用して拒否割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 独自の拒否割り当てを直接作成することはできません。 割り当て拒否が作成されるしくみの詳細については、[拒否割り当て](deny-assignments.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

拒否割り当てに関する情報を取得するのに必要なものは次のとおりです:

- `Microsoft.Authorization/denyAssignments/read`Azure リソースのほとんどの組み込みロール[に含まれている ](built-in-roles.md) アクセス許可
- [Azure Cloud Shell の PowerShell](/azure/cloud-shell/overview) または [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>拒否割り当てを一覧表示する

### <a name="list-all-deny-assignments"></a>すべての拒否割り当てを一覧表示する

現在のサブスクリプションに対するすべての拒否割り当てを一覧表示するには、[Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment) を使用します。

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>リソース グループ スコープの拒否割り当てを一覧表示する

リソース グループ スコープのすべての拒否割り当てを一覧表示するには、[Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment) を使用します。

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>サブスクリプション スコープの拒否割り当てを一覧表示する

サブスクリプション スコープのすべての拒否割り当てを一覧表示するには、[Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで、または [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) を使用して、その ID を見つけることができます。

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>次のステップ

- [Azure リソースの拒否割り当ての概要](deny-assignments.md)
- [Azure portal を使用して Azure リソースの拒否割り当てを一覧表示する](deny-assignments-portal.md)
- [REST API を使用して Azure リソースの拒否割り当てを一覧表示する](deny-assignments-rest.md)
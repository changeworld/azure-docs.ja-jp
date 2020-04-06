---
title: Azure RBAC と REST API を使用してロールの割り当てを一覧表示する
description: Azure ロールベースのアクセス制御 (RBAC) と REST API を使用して、ユーザー、グループ、サービス プリンシパル、およびマネージド ID のアクセスを決定する方法について説明します。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062152"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Azure RBAC と REST API を使用してロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]この記事では、REST API を使用してロールの割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 組織で、[Azure の委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を使用するサービス プロバイダーに管理機能を外部委託している場合、そのサービス プロバイダーによって承認されているロールの割り当てはここに表示されません。

## <a name="list-role-assignments"></a>ロールの割り当ての一覧表示

RBAC でアクセス権を一覧表示するには、ロールの割り当てを一覧表示します。 ロールの割り当てを一覧表示するには、いずれかの[ロールの割り当て - 一覧表示](/rest/api/authorization/roleassignments/list) REST API を使用します。 結果を絞り込むには、スコープと任意のフィルターを指定します。

1. 次の要求から開始します。

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI の *{scope}* を、ロールの割り当てを一覧表示するスコープに変更します。

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    > | `subscriptions/{subscriptionId1}` | サブスクリプション |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | リソース |

    前の例で、microsoft.web は App Service インスタンスを参照するリソース プロバイダーです。 同様に、他の任意のリソース プロバイダーを使用してスコープを指定できます。 詳細については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」およびサポートされている「[Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)」を参照してください。  
     
1. *{filter}* には、ロールの割り当て一覧をフィルター処理するために適用する条件を指定します。

    > [!div class="mx-tableFixed"]
    > | Assert | 説明 |
    > | --- | --- |
    > | `$filter=atScope()` | サブスコープのロールの割り当ては含めずに、指定スコープのみを対象にロールの割り当てを一覧表示します。 |
    > | `$filter=assignedTo('{objectId}')` | 指定したユーザーまたはサービス プリンシパルを対象にロールの割り当てを一覧表示します。<br/>ユーザーが、ロール割り当てがあるグループのメンバーである場合は、そのロール割り当ても一覧表示されます。 このフィルターはグループにとって推移的です。つまり、ユーザーがあるグループのメンバーで、そのグループが、ロール割り当てのある別のグループのメンバーである場合は、そのロール割り当ても一覧表示されます。<br/>このフィルターは、ユーザーまたはサービス プリンシパルのオブジェクト ID のみを受け入れます。 グループのオブジェクト ID を渡すことはできません。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 指定したユーザーまたはサービス プリンシパルを対象に、指定したスコープでロールの割り当てを一覧表示します。 |
    > | `$filter=principalId+eq+'{objectId}'` | 指定したユーザー、グループ、またはサービス プリンシパルを対象にロールの割り当てを一覧表示します。 |

## <a name="next-steps"></a>次のステップ

- [Azure RBAC と REST API を使用してロールの割り当てを追加または削除する](role-assignments-rest.md)
- [Azure REST API リファレンス](/rest/api/azure/)

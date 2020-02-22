---
title: RBAC と REST API を使用してロールの割り当てを追加または削除する
description: Azure のロールベースのアクセス制御 (RBAC) と REST API を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ae6fce5ab962637fe477ade75cf81b6ac237bdd2
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138307"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Azure RBAC と REST API を使用してロールの割り当てを追加または削除する

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] この記事では、REST API を使用してロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールの割り当てを追加または削除するには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)

## <a name="add-a-role-assignment"></a>ロールの割り当てを追加する

RBAC でアクセス権を付与するには、ロールの割り当てを追加します。 ロールの割り当てを追加するには、[ロールの割り当て - 作成](/rest/api/authorization/roleassignments/create) REST API を使用し、セキュリティ プリンシパル、ロールの定義、スコープを指定します。 この API を呼び出すには、`Microsoft.Authorization/roleAssignments/write` 操作へのアクセス許可が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。

1. [ロールの定義 - 一覧表示](/rest/api/authorization/roledefinitions/list) REST API を使用するか、[組み込みロール](built-in-roles.md)を参照し、割り当てるロール定義の識別子を取得します。

1. GUID ツールを使用し、ロール割り当て識別子に使用する一意の識別子を生成します。 この識別子の形式は `00000000-0000-0000-0000-000000000000` になります。

1. 次の要求と本文から開始します。

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. URI の *{scope}* をロールの割り当てのスコープに変更します。

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    | `subscriptions/{subscriptionId1}` | サブスクリプション |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleAssignmentName}* を、ロールの割り当ての GUID 識別子に置き換えます。

1. 要求本文内で、 *{scope}* をロールの割り当てのスコープに変更します。

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    | `subscriptions/{subscriptionId1}` | サブスクリプション |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleDefinitionId}* を、ロールの定義の識別子に置き換えます。

1. *{principalId}* を、ロールが割り当てられるユーザー、グループ、またはサービス プリンシパルのオブジェクト識別子に置き換えます。

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

RBAC では、アクセス権を削除するにはロールの割り当てを削除する必要があります。 ロールの割り当てを削除するには、[ロールの割り当て - 削除](/rest/api/authorization/roleassignments/delete) REST API を使用します。 この API を呼び出すには、`Microsoft.Authorization/roleAssignments/delete` 操作へのアクセス許可が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。

1. ロールの割り当ての識別子 (GUID) を取得します。 この識別子は、ロールの割り当てを最初に作成するときに返されます。あるいは、ロールの割り当てを一覧表示することで取得できます。

1. 次の要求から開始します。

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. URI の *{scope}* を、ロールの割り当てを削除するためのスコープに変更します。

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    | `subscriptions/{subscriptionId1}` | サブスクリプション |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleAssignmentName}* を、ロールの割り当ての GUID 識別子に置き換えます。

## <a name="next-steps"></a>次のステップ

- [Azure RBAC と REST API を使用してロールの割り当てを一覧表示する](role-assignments-list-rest.md)
- [Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API リファレンス](/rest/api/azure/)
- [REST API を使用して Azure リソースのカスタム ロールを作成する](custom-roles-rest.md)

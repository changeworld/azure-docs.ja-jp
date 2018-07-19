---
title: Azure Active Directory で全体管理者のアクセス権を昇格する | Microsoft Docs
description: Azure portal または REST API を使用して、Azure Active Directory の全体管理者のアクセス権を昇格する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0abf0a5971435fc3842a93e79d39468cba5c74da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445213"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Azure Active Directory の全体管理者のアクセス権を昇格する

Azure Active Directory (Azure AD) の[全体管理者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator)の場合は、以下の操作を行うことがあります。

- ユーザーがアクセス権を失ったときに Azure サブスクリプションへのアクセス権を回復する
- 別のユーザーまたは自分に Azure サブスクリプションへのアクセス権を付与する
- 組織内のすべての Azure サブスクリプションを表示する
- オートメーション アプリ (請求書作成アプリや監査アプリなど) がすべての Azure サブスクリプションにアクセスできるようにする

既定では、Azure AD の管理者ロールと Azure のロールベース アクセス制御 (RBAC) ロールは、Azure AD と Azure にまたがっていません。 ただし、Azure AD の全体管理者であれば、アクセス権を昇格して Azure のサブスクリプションと管理グループを管理することができます。 アクセス権を昇格すると、特定のテナント内のすべてのサブスクリプションに対して[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)ロール (RBAC ロール) が付与されます。 ユーザー アクセス管理者ロールを使用すると、ルート スコープ (`/`) の Azure リソースに対するアクセス権を他のユーザーに付与できます。

この昇格は一時的なものにして、必要なときにのみ実行するようにしてください。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Azure portal を使用して全体管理者のアクセス権を昇格する

1. [Azure Portal](https://portal.azure.com) または [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。

1. ナビゲーション リストで **[Azure Active Directory]**、**[プロパティ]** の順にクリックします。

   ![Azure AD のプロパティ - スクリーンショット](./media/elevate-access-global-admin/aad-properties.png)

1. **[全体管理者は、Azure サブスクリプションと管理グループを管理できます]** のスイッチを **[はい]** に設定します。

   ![[全体管理者は、Azure サブスクリプションと管理グループを管理できます] - スクリーンショット](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   スイッチを **[はい]** に設定すると、全体管理者アカウント (現在ログインしているユーザー) は、ルート スコープ (`/`) にある Azure RBAC のユーザー アクセス管理者ロールに追加されます。これで、Azure AD テナントに関連付けられているすべての Azure サブスクリプションについて表示およびレポートできるアクセス権が付与されます。

   **[いいえ]** にスイッチを設定すると、全体管理者アカウント (現在ログインしているユーザー) は Azure RBAC のユーザー アクセス管理者ロールから削除されます。 Azure AD テナントに関連付けられているすべての Azure サブスクリプションは表示できません。アクセス権が付与されている Azure サブスクリプションのみを表示および管理できます。

1. **[保存]** をクリックして設定を保存します。

   この設定はグローバル プロパティではなく、現在ログインしているユーザーのみに適用されます。

1. 昇格したアクセス権で行う必要のあるタスクを実行します。 完了したら、スイッチを **[いいえ]** に戻します。

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>PowerShell を使用してルート スコープ (/) のロールの割り当てを一覧表示する

ルート スコープ (`/`) のユーザーについてユーザー アクセス管理者ロールの割り当てを一覧表示するには、[Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) コマンドを使用します。

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

## <a name="remove-a-role-assignment-at-the-root-scope--using-powershell"></a>PowerShell を使用してルート スコープ (/) のロールの割り当てを削除する

ルート スコープ (`/`) のユーザーについてユーザー アクセス管理者ロールの割り当てを削除するには、[Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) コマンドを使用します。

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>REST API を使用して全体管理者のアクセス権を昇格する

REST API を使用して全体管理者のアクセス権を昇格するには、以下の基本的な手順を実行します。

1. REST を使用して `elevateAccess` を呼び出します。これにより、ユーザー アクセス管理者ロールがルート スコープ (`/`) で付与されます。

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. [ロールの割り当て](/rest/api/authorization/roleassignments)を作成し、任意の役割を任意のスコープで割り当てます。 次の例は、{roleDefinitionID} ロールをルート スコープ (`/`) で割り当てるためのプロパティを示しています。

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. ユーザー アクセス管理者である間は、ルート スコープ (`/`) でロールの割り当てを削除することもできます。

1. 再び必要になるまで、ユーザー アクセス管理者特権を削除します。

## <a name="list-role-assignments-at-the-root-scope--using-the-rest-api"></a>REST API を使用してルート スコープ (/) のロールの割り当てを一覧表示する

ルート スコープ (`/`) でのユーザーに対するロールの割り当ての一覧を表示することができます。

- [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) を呼び出します。`{objectIdOfUser}` は、ロールの割り当てを取得するユーザーのオブジェクト ID です。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

## <a name="remove-elevated-access-using-the-rest-api"></a>REST API を使用して昇格したアクセス権を削除する

`elevateAccess` を呼び出すと、自分自身に対するロールの割り当てが作成されます。このため、これらの権限を取り消すには、割り当てを削除する必要があります。

1. [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) を呼び出します。この `roleName` はユーザー アクセス管理者であり、ユーザー アクセス管理者ロールの名前 ID を判別します。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    この場合の `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` では、`name` パラメーターの ID を省略できます。

2. また、テナント スコープでテナント管理者のロール割り当ての一覧を表示する必要もあります。 アクセス権の昇格呼び出しを行ったテナント管理者の `principalId` について、テナント スコープの割り当て一覧を表示します。 これにより、objectid についてテナントの割り当ての一覧が表示されます。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >テナント管理者の割り当ては多くありません。前のクエリが返す割り当ての数が多すぎる場合は、テナント スコープ レベルのみで、すべての割り当てに対してクエリを実行し、結果をフィルターすることもできます。`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. 前の呼び出しは、ロール割り当ての一覧を返します。 スコープが `"/"` で、`roleDefinitionId` が手順 1 で見つかったロール名 ID で終了し、`principalId` がテナント管理者の objectId と一致する、ロール割り当てを検索します。 
    
    サンプル ロールの割り当て

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    もう一度、`name` パラメーターの ID を保存します (この例では e7dd75bc-06f6-4e71-9014-ee96a929d099)。

    3. 最後に、ロール割り当て ID を使用して、`elevateAccess` によって追加された割り当てを削除します。

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>次の手順

- [REST を使用したロールベースのアクセス制御](role-assignments-rest.md)
- [Privileged Identity Management で Azure リソースへのアクセスを管理する](pim-azure-resource.md)
- [条件付きアクセスを使用して Azure 管理へのアクセスを管理する](conditional-access-azure-management.md)

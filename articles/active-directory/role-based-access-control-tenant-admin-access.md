---
title: "テナント管理者によるアクセス権の昇格 | Microsoft Docs"
description: "このトピックでは、ロール ベースのアクセス制御 (RBAC) の組み込みのロールについて説明します。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: andredm
ms.openlocfilehash: cb6e5a398a1d7e20efbcc4a8900f9e8dea43ad2c
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>ロール ベースのアクセス制御を使用してテナント管理者としてアクセス権を昇格させる

テナント管理者は、ロール ベースのアクセス制御を使用して、通常よりも上位のアクセス許可を付与できるように、アクセス権を一時的に昇格させることができます。 テナント管理者は、必要に応じて、自分自身をユーザー アクセス管理者ロールに昇格させることができます。 このロールは、自分自身または他のユーザーに "/" スコープでロールを付与する権限をテナント管理者に与えます。

この機能は、組織内に存在するすべてのサブスクリプションをテナント管理者が確認できるようにするために重要です。 さらに、請求、監査などのオートメーション アプリからすべてのサブスクリプションにアクセスし、課金や資産管理のために組織の状況を正確に提供できるようになります。  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Azure AD 管理センターでテナントへのアクセスに elevateAccess を使用する

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、ご自身の資格情報でログインします。

2. Azure AD の左側のメニューから **[プロパティ]** を選択します。

3. **[プロパティ]** ウィンドウで、**[全体管理者は Azure サブスクリプションを管理できます]** を見つけて、**[はい]**、**[保存]** の順に選択します。
    > [!IMPORTANT] 
    > **[はい]** を選択した場合は、ポータルに現在ログインしているユーザーについて、ルート "/" (ルート スコープ) で、**ユーザー アクセス管理者**のロールが割り当てられます。 **これにより、ユーザーにその他のすべての Azure サブスクリプションが表示されます。**
    
    > [!NOTE] 
    > **[いいえ]** を選択した場合は、ポータルに現在ログインしているユーザーについて、ルート "/" (ルート スコープ) で、**ユーザー アクセス管理者**のロールが削除されます。

> [!TIP] 
> これは、Azure Active Directory のグローバル プロパティと似ているように思われるかもしれませんが、現在ログオンしているユーザーを対象にして、ユーザーごとに機能します。 Azure Active Directory のグローバル管理者権限がある場合は、Azure Active Directory 管理センターに現在ログインしているユーザーに対してこの elevateAccess 機能を呼び出すことができます。

![Azure AD 管理センター - プロパティ - 全体管理者は Azure サブスクリプションを管理できます - スクリーンショット](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>REST API で elevateAccess を使用してテナントへのアクセス権を付与する

基本的なプロセスは、次の手順で動作します。

1. REST を使用して *elevateAccess*を呼び出します。これにより、ユーザー アクセス管理者ロールが "/" スコープで付与されます。

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. [ロールの割り当て](/rest/api/authorization/roleassignments)を作成し、任意の役割を任意のスコープで割り当てます。 次の例は、閲覧者ロールを "/" スコープで割り当てるためのプロパティを示しています。

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. ユーザー アクセス管理管理者である間は、ロールの割り当てを "/" スコープで削除することもできます。

4. 再び必要になるまで、ユーザー アクセス管理権限を取り消します。


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>REST API で elevateAccess アクションを取り消す方法

*elevateAccess* を呼び出すと、自分自身に対するロールの割り当てが作成されます。このため、これらの権限を取り消すには、割り当てを削除する必要があります。

1.  GET role definitions を呼び出します。roleName = User Access Administrator で、ユーザー アクセス管理者ロールの名前の GUID を判別します。
    1.  GET *https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator*

        ```
        {"value":[{"properties":{
        "roleName":"User Access Administrator",
        "type":"BuiltInRole",
        "description":"Lets you manage user access to Azure resources.",
        "assignableScopes":["/"],
        "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
        "createdOn":"0001-01-01T08:00:00.0000000Z",
        "updatedOn":"2016-05-31T23:14:04.6964687Z",
        "createdBy":null,
        "updatedBy":null},
        "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "type":"Microsoft.Authorization/roleDefinitions",
        "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
        "nextLink":null}
        ```

        *name* パラメーターの GUID を保存します (この例では **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**)。

2. また、テナント スコープでテナント管理者のロール割り当ての一覧を表示する必要もあります。 アクセス権の昇格呼び出しを行った TenantAdmin の PrincipalId について、テナント スコープの割り当て一覧を表示します。 これにより、ObjectID についてテナントの割り当ての一覧が表示されます。 
    1. GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'*
    
        >[!NOTE] 
        >テナント管理者の割り当ては多くありません。上記のクエリが返す割り当ての数が多すぎる場合は、テナント スコープ レベルのみで、すべての割り当てに対してクエリを実行し、結果をフィルターすることもできます: GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()*
        
    2. 上記の呼び出しは、ロール割り当ての一覧を返します。 スコープが "/" で、RoleDefinitionId が手順 1. で見つかったロール名 GUID で終了し、PrincipalId がテナント管理者の ObjectId と一致する、ロール割り当てを検索します。ロール割り当ては次のようになります。

        ```
        {"value":[{"properties":{
        "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "principalId":"{objectID}",
        "scope":"/",
        "createdOn":"2016-08-17T19:21:16.3422480Z",
        "updatedOn":"2016-08-17T19:21:16.3422480Z",
        "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
        "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
        "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
        "type":"Microsoft.Authorization/roleAssignments",
        "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
        "nextLink":null}
        ```
        
        もう一度、*name* パラメーターの GUID を保存します (この例では **e7dd75bc-06f6-4e71-9014-ee96a929d099**)。

    3. 最後に、強調表示された **RoleAssignment ID** を使用して、アクセス権の昇格によって追加された割り当てを削除します。

        DELETE https://management.azure.com /providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01

## <a name="next-steps"></a>次のステップ

- 「[REST API を使用したロールベースの Access Control の管理](role-based-access-control-manage-access-rest.md)」を参照する

- Azure Portal で[アクセスの割り当ての管理](role-based-access-control-manage-assignments.md)を行う

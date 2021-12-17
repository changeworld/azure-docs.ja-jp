---
title: Azure Active Directory でグループからロールの割り当てを削除する
description: Azure portal、PowerShell、または Microsoft Graph API を使用して、Azure Active Directory のグループからロールの割り当てを削除します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 07/30/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: add87a653c977c1378feeaa6d204a7bce0184431
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732328"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Azure Active Directory でグループからロールの割り当てを削除する

この記事では、グループに割り当てられている Azure AD ロールを IT 管理者が削除する方法について説明します。 Azure portal で、ユーザーに対する直接的と間接的なロールの割り当ての両方を削除できるようになりました。 ユーザーにグループ メンバーシップによってロールが割り当てられている場合は、そのユーザーをグループから削除してロールの割り当てを削除します。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P1 または P2 ライセンス
- 特権ロール管理者または全体管理者
- PowerShell を使用する場合は、AzureAD モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]**  > <*ロール名*> を選択します。

1. ロールの割り当てを削除するグループを選択し、 **[割り当ての削除]** を選択します。

   ![選択したグループからロールの割り当てを削除します。](./media/groups-remove-assignment/remove-assignment.png)

1. アクションを確認するよう求められたら、 **[はい]** を選択します。

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>ロールに割り当てることができるグループを作成する

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>グループを割り当てるロールの定義を取得する

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>役割の割り当ての作成

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>ロールの割り当てを削除する

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Azure AD ロールを割り当てることができるグループを作成する

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>ロールの定義を取得する

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=displayName+eq+'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>ロールの割り当てを作成する

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"{object-id-of-group}",
"roleDefinitionId":"{role-definition-id}",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>ロール割り当ての削除

```http
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/{role-assignment-id}
```

## <a name="next-steps"></a>次のステップ

- [Azure AD グループを使用してロールの割り当てを管理する](groups-concept.md)
- [グループに割り当てられている Azure AD ロールをトラブルシューティングする](groups-faq-troubleshooting.yml)

---
title: Azure AD ロールをグループに割り当てる - Azure Active Directory
description: Azure portal、PowerShell、または Graph API で、ロールを割り当て可能なグループに Azure AD ロールを割り当てます。
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
ms.openlocfilehash: 35982d62a0e92b5f4647243cde5920529d6c2989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723114"
---
# <a name="assign-azure-ad-roles-to-groups"></a>Azure AD ロールをグループに割り当てる

このセクションでは、IT 管理者が Azure Active Directory (Azure AD) ロールを Azure AD グループに割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P1 または P2 ライセンス
- 特権ロール管理者または全体管理者
- PowerShell を使用する場合は、AzureAD モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="azure-portal"></a>Azure portal

Azure AD ロールにグループを割り当てることは、ロールが割り当て可能なグループのみを使用できる点を除いて、ユーザーとサービス プリンシパルの割り当てに似ています。 Azure portal には、ロールを割り当て可能なグループのみが表示されます。

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択し、割り当てるロールを選択します。

1. ***ロール名** _ ページで、> _*[割り当ての追加]** を選択します。

   ![新しいロールの割り当てを追加](./media/groups-assign-role/add-assignment.png)

1. グループを選択します。 Azure AD ロールに割り当てることができるグループのみが表示されます。

    [![新しいロールの割り当てには、割り当て可能なグループのみが表示されます。](./media/groups-assign-role/eligible-groups.png "新しいロールの割り当てには、割り当て可能なグループのみが表示されます。")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. **[追加]** を選択します。

ロールのアクセス許可の割り当ての詳細については、[ユーザーに管理者および管理者以外のロールを割り当てる](../fundamentals/active-directory-users-assign-role-azure-portal.md)方法に関するページを参照してください。

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>ロールに割り当てることができるグループを作成する

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>割り当てるロールのロールの定義を取得する

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>役割の割り当ての作成

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Azure AD ロールを割り当てることができるグループを作成する

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [],
"mailEnabled": false,
"securityEnabled": true,
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true
}
```

### <a name="get-the-role-definition"></a>ロールの定義を取得する

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq 'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>ロールの割り当てを作成する

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>次のステップ

- [Azure AD グループを使用してロールの割り当てを管理する](groups-concept.md)
- [グループに割り当てられている Azure AD ロールをトラブルシューティングする](groups-faq-troubleshooting.yml)

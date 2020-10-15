---
title: Azure Active Directory でグループに割り当てられているロールを表示する | Microsoft Docs
description: Azure AD 管理センターを使用して、グループに割り当てられているロールを表示する方法について説明します。 グループと割り当てられたロールの表示は、既定のユーザー アクセス許可で行うことができます。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0a34b3861c82b3d2ef54a36108f9ea522d716d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983100"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory でグループに割り当てられているロールを表示する

この項では、Azure AD 管理センターを使用して、グループに割り当てられているロールを表示する方法について説明します。 グループと割り当てられたロールの表示は、既定のユーザー アクセス許可で行うことができます。

1. 管理者以外の資格情報または管理者の資格情報を使用して、[Azure AD 管理センター](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。

1. 目的のグループを選択します。

1. **[割り当てられたロール]** を選択します。 このグループに割り当てられているすべての Azure AD ロールが表示されます。

   ![選択したグループに割り当てられているすべてのロールの表示](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell の使用

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>グループへのロールの割り当てを表示する

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API の使用

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>グループへのロールの割り当てを取得する

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>次のステップ

- [クラウド グループを使用してロールの割り当てを管理する](roles-groups-concept.md)
- [クラウド グループに割り当てられているロールのトラブルシューティング](roles-groups-faq-troubleshooting.md)

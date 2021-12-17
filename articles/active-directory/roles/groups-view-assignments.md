---
title: Azure Active Directory でグループに割り当てられているロールを表示する | Microsoft Docs
description: Azure portal を使用して、グループに割り当てられているロールを表示する方法について説明します。 グループと割り当てられたロールの表示は、既定のユーザー アクセス許可で行うことができます。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87e59c7ab9bfc5fa9211d84d2d9a855dea97ec1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732326"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory でグループに割り当てられているロールを表示する

このセクションでは、Azure portal を使用してグループに割り当てられているロールを表示する方法について説明します。 グループと割り当てられたロールの表示は、既定のユーザー アクセス許可で行うことができます。

## <a name="prerequisites"></a>前提条件

- PowerShell を使用する場合は、AzureAD モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[グループ]** を選択します。

1. 目的のロール割り当て可能なグループを選択します。

1. **[割り当てられたロール]** を選択します。 このグループに割り当てられているすべての Azure AD ロールが表示されます。

   ![選択したグループに割り当てられているすべてのロールの表示](./media/groups-view-assignments/view-assignments.png)

## <a name="powershell"></a>PowerShell

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>グループへのロールの割り当てを表示する

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>グループへのロールの割り当てを取得する

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>次のステップ

- [Azure AD グループを使用してロールの割り当てを管理する](groups-concept.md)
- [グループに割り当てられている Azure AD ロールをトラブルシューティングする](groups-faq-troubleshooting.yml)

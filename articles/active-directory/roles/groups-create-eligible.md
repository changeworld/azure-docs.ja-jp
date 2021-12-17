---
title: Azure Active Directory でロールを割り当てるためのグループを作成する | Microsoft Docs
description: Azure AD でロールを割り当て可能なグループを作成する方法について説明します。 Azure portal、PowerShell、または Graph API で Azure ロールを管理します。
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
ms.openlocfilehash: c14580790891190f40dd2866aaac25ad2c286137
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741670"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Azure Active Directory でロールを割り当て可能なグループを作成する

ロールを割り当てることができるグループは、'isAssignableToRole' プロパティを True に設定して作成したものか、Azure portal で **[グループに Azure AD ロールを割り当てることができる]** をオンにして作成したものだけです。 このグループ属性を使用すると、そのグループを Azure Active Directory (Azure AD) のロールに割り当てることができるようになります。 この記事では、この特別な種類のグループを作成する方法について説明します。 **注:** isAssignableToRole プロパティが true に設定されているグループは、動的メンバーシップ型になりません。 詳細については、[Azure AD グループを使用したロール割り当ての管理](groups-concept.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P1 または P2 ライセンス
- 特権ロール管理者または全体管理者
- PowerShell を使用する場合は、AzureAD モジュール
- Microsoft Graph API の Graph エクスプローラーを使用する場合の管理者の同意

詳細については、[PowerShell または Graph エクスプローラーを使用するための前提条件](prerequisites.md)に関するページを参照してください。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[グループ]**  >  **[すべてのグループ]**  >  **[新しいグループ]** の順に選択します。

    [![Azure Active Directory を開いて新しいグループを作成する。](./media/groups-create-eligible/new-group.png "Azure Active Directory を開いて新しいグループを作成する。")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. **[新しいグループ]** タブで、グループの種類、名前、説明を指定します。

1. **[グループに Azure AD ロールを割り当てることができる]** をオンにします。 このスイッチは、特権ロール管理者とグローバル管理者にのみ表示されます。この 2 つのロールのみが、このスイッチを設定できます。

    [![新しいグループをロールの割り当ての対象にする](./media/groups-create-eligible/eligible-switch.png "新しいグループをロールの割り当ての対象にする")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. グループのメンバーと所有者を選択します。 オプションでグループにロールを割り当てることもできますが、こちらでロールを割り当てる必要はありません。

    [![ロールを割り当て可能なグループにメンバーを追加し、ロールを割り当てる。](./media/groups-create-eligible/specify-members.png "ロールを割り当て可能なグループにメンバーを追加し、ロールを割り当てる。")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. メンバーと所有者を指定したら、 **[作成]** を選択します。

    [![ページの下部にある [作成] ボタン。](./media/groups-create-eligible/create-button.png "ページの下部にある [作成] ボタン。")](./media/groups-create-eligible/create-button.png#<lightbox>)

ロールを割り当てていた場合にはそれが付いた状態で、グループが作成されます。

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>ロールに割り当てることができるグループを作成する

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

この種類のグループでは、`isPublic` は常に false、`isSecurityEnabled` は常に true になります。

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>ロールを割り当て可能なグループに 1 つのグループのユーザーとサービス プリンシパルをコピーする

```powershell
#Basic set up
Install-Module -Name AzureAD
Import-Module -Name AzureAD
Get-Module -Name AzureAD

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>ロールを割り当て可能なグループを Azure AD で作成する

```http
POST https://graph.microsoft.com/beta/groups
{
  "description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
  "displayName": "Contoso_Helpdesk_Administrators",
  "groupTypes": [
    "Unified"
  ],
  "isAssignableToRole": true,
  "mailEnabled": true,
  "securityEnabled": true,
  "mailNickname": "contosohelpdeskadministrators",
  "visibility" : "Private"
}
```

この種類のグループでは、`isPublic` は常に false、`isSecurityEnabled` は常に true になります。

## <a name="next-steps"></a>次のステップ

- [Azure AD ロールをグループに割り当てる](groups-assign-role.md)
- [Azure AD グループを使用してロールの割り当てを管理する](groups-concept.md)
- [グループに割り当てられている Azure AD ロールをトラブルシューティングする](groups-faq-troubleshooting.yml)

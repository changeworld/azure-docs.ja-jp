---
title: 管理単位スコープを使用してロールを割り当ておよび一覧表示する (プレビュー) - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で管理単位を使用してロールの割り当ての範囲を制限します
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870412"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>スコープ付きロールを管理単位に割り当てる

Azure Active Directory (Azure AD) では、きめ細かい管理制御を行うために、1 つ以上の管理単位 (AU) に制限されたスコープでユーザーを Azure AD ロールに割り当てることができます。

管理単位の管理に PowerShell や Microsoft Graph を使用するよう準備する手順については、[作業の開始](roles-admin-units-manage.md#get-started)に関するページを参照してください。

## <a name="roles-available"></a>利用可能なロール

Role  |  説明
----- |  -----------
認証管理者  |  割り当てられた管理単位内でのみ、管理者以外のユーザーの認証方法の情報を表示、設定、リセットするためにアクセスできます。
グループ管理者  |  割り当てられた管理単位内でのみ、グループとグループ設定 (名前付けポリシーや有効期限ポリシーなど) のすべての側面を管理できます。
ヘルプデスク管理者  |  割り当てられた管理単位内でのみ、管理者以外のユーザーとヘルプデスク管理者のパスワードをリセットできます。
ライセンス管理者  |  管理単位内でのみ、ライセンスを割り当てたり、ライセンス割り当ての削除や更新を行ったりできます。
パスワード管理者  |  割り当てられた管理単位内でのみ、管理者以外のユーザーとパスワード管理者のパスワードをリセットできます。
ユーザー管理者  |  割り当てられた管理単位内でのみ、ユーザーとグループのすべての側面 (制限付き管理者のパスワードのリセットを含む) を管理できます。

## <a name="assign-a-scoped-role"></a>スコープ付きロールを割り当てる

### <a name="azure-portal"></a>Azure portal

ポータルで、**Azure AD > [管理単位]** の順にアクセスします。 ロールをユーザーに割り当てる対象となる管理単位を選択します。 左側のウィンドウで、[ロールと管理者] を選択して、利用可能なすべてのロールを一覧表示します。

![ロールのスコープを変更する管理単位を選択する](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

割り当てるロールを選択して、 **[割り当ての追加]** を選択します。 これにより、右側にパネルがスライドして開き、ロールに割り当てるユーザーを 1 人以上選択できます。

![スコープを設定するロールを選択してから、[割り当ての追加] を選択する](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

強調表示されたセクションは、それぞれの環境で必要に応じて変更される場合があります。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>AU のスコープ付き管理者を一覧表示する

### <a name="azure-portal"></a>Azure portal

管理単位スコープで実行されるすべてのロール割り当ては、[Azure AD の管理単位セクション](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)で確認できます。 ポータルで、**Azure AD > [管理単位]** の順にアクセスします。 一覧表示するロールの割り当ての管理単位を選択します。 **[ロールと管理者]** を選択し、ロールを開いて、管理単位内の割り当てを表示します。

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

強調表示されたセクションは、それぞれの環境で必要に応じて変更される場合があります。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>次のステップ

- [管理単位のトラブルシューティングと FAQ](roles-admin-units-faq-troubleshoot.md)

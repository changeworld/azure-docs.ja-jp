---
title: PIM で Azure AD ロールをアクティブ化する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールをアクティブにする方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae2bc007d6172e6f7ef2bfc7e22af89acf2c8afb
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669437"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールをアクティブ化する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用すると、企業における Azure AD や他の Microsoft オンライン サービス (Microsoft 365 や Microsoft Intune など) のリソースへの特権アクセスの管理が簡略化されます。  

管理ロールの *候補者* となっている場合は、特権が必要な操作を実行する必要があるときに、ロールの割り当てを *アクティブ* にできます。 たとえば、Microsoft 365 の機能をときどきしか管理しないユーザーは、組織の特権ロール管理者によって永続的なグローバル管理者には設定されない可能性があります。このロールは他のサービスにも影響を与えるからです。 その代わりに、Exchange Online 管理者などの Azure AD ロールが割り当てられます。 このような権限が必要な場合には、ロールをアクティブ化することを要求できます。それにより、事前に定義された期間だけ管理者権限が付与されます。

この記事は、Privileged Identity Management で自分の Azure AD ロールをアクティブ化する必要のある管理者を対象としています。

## <a name="activate-a-role"></a>ロールのアクティブ化

Azure AD ロールを想定する必要がある場合は、Privileged Identity Management で **[自分のロール]** を開いてアクティブ化を要求できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。 ダッシュボードに [Privileged Identity Management] タイルを追加する方法については、「[Privileged Identity Management の使用開始](pim-getting-started.md)」を参照してください。

1. **[自分のロール]** を選択し、 **[Azure AD ロール]** を選択して、資格がある Azure AD ロールの一覧を表示します。

    ![アクティブ化できるロールを表示した [自分のロール] ページ](./media/pim-how-to-activate-role/my-roles.png)

1. **[Azure AD ロール]** の一覧で、アクティブにするロールを見つけます。

    ![[Azure AD ロール] - 資格があるロールの一覧](./media/pim-how-to-activate-role/activate-link.png)

1. **[アクティブ化]** を選択して、[アクティブ化] ウィンドウを開きます。

    ![期間とスコープを含む Azure AD ロールのアクティブ化ページ](./media/pim-how-to-activate-role/activate-page.png)

1. **[追加の検証が必要]** を選択し、指示に従ってセキュリティ確認を提供します。 認証が要求されるのは、各セッションにつき 1 回だけです。

    ![PIN コードなどのセキュリティ確認を提供するための画面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 多要素認証の後に、 **[続行する前にアクティブにする]** を選択します。

    ![ロールをアクティブ化する前に MFA で自分の ID を確認する](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. より狭いスコープを指定する場合は、 **[Scope]\(スコープ\)** を選択して [フィルター] ペインを開きます。 [フィルター] ペインでは、アクセス権が必要な Azure AD リソースを指定できます。 必要最低限のリソースに対するアクセスを要求することをお勧めします。

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。 Azure AD ロールは、選択した時刻になるとアクティブになります。

1. **[理由]** ボックスに、アクティブ化要求の理由を入力します。

1. **[アクティブ化]** を選びます。

    アクティブ化に[承認が必要なロール](pim-resource-roles-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![アクティブ化要求は承認通知待ち中](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-using-graph-api"></a>Graph API を使用してロールをアクティブにする

### <a name="get-all-eligible-roles-that-you-can-activate"></a>アクティブにできる、資格のあるロールをすべて取得する

ユーザーがグループ メンバーシップを使用して自分のロールの適格性を取得した場合、この Graph 要求では適格性は返されません。

#### <a name="http-request"></a>HTTP 要求

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleEligibilityScheduleRequests/filterByCurrentUser(on='principal')  
````

#### <a name="http-response"></a>HTTP 応答

領域を節約するために、あるロールの応答のみが表示されていますが、アクティブにできる、資格のあるロールの割り当てすべてが一覧表示されます。

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleEligibilityScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleEligibilityScheduleRequest", 
            "id": "<request-ID-GUID>", 
            "status": "Provisioned", 
            "createdDateTime": "2021-07-15T19:39:53.33Z", 
            "completedDateTime": "2021-07-15T19:39:53.383Z", 
            "approvalId": null, 
            "customData": null, 
            "action": "AdminAssign", 
            "principalId": "<principal-ID-GUID>", 
            "roleDefinitionId": "<definition-ID-GUID>", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "<schedule-ID-GUID>", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "<user-ID-GUID>" 
                } 
            }, 
            "scheduleInfo": { 
                "startDateTime": "2021-07-15T19:39:53.3846704Z", 
                "recurrence": null, 
                "expiration": { 
                    "type": "noExpiration", 
                    "endDateTime": null, 
                    "duration": null 
                } 
            }, 
            "ticketInfo": { 
                "ticketNumber": null, 
                "ticketSystem": null 
            } 
        },
} 
````

### <a name="activate-a-role-assignment-with-justification"></a>正当な理由を指定してロールの割り当てをアクティブにする

#### <a name="http-request"></a>HTTP 要求

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 

{ 
    "action": "SelfActivate", 
    "justification": "adssadasasd", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>" 
} 
````

#### <a name="http-response"></a>HTTP 応答

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "f1ccef03-8750-40e0-b488-5aa2f02e2e55", 
    "status": "PendingApprovalProvisioning", 
    "createdDateTime": "2021-07-15T19:51:07.1870599Z", 
    "completedDateTime": "2021-07-15T19:51:17.3903028Z", 
    "approvalId": "<approval-ID-GUID>", 
    "customData": null, 
    "action": "SelfActivate", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": "<schedule-ID-GUID>", 
    "justification": "test", 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "<user-ID-GUID>" 
        } 
    }, 
    "scheduleInfo": { 
        "startDateTime": null, 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT5H30M" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="view-the-status-of-activation-requests"></a>アクティブ化要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択して、Azure AD ロールおよび Azure リソース ロール要求の一覧を表示します。

    ![承認待ち中の要求を表示している [個人の要求 - Azure AD] ページ](./media/pim-how-to-activate-role/my-requests-page.png)

1. 右へスクロールして **[Request Status]\(要求の状態\)** 列を表示します。

## <a name="cancel-a-pending-request-for-new-version"></a>新しいバージョンの保留要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択します。

1. 取り消すロールの **[キャンセル]** リンクを選択します。

    [キャンセル] を選択すると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![[キャンセル] アクションが強調表示された個人の要求の一覧](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-portal-delay"></a>ポータルの遅延のトラブルシューティングを行う

### <a name="permissions-arent-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

Privileged Identity Management でロールをアクティブ化しても、アクティブ化が、特権ロールを必要とするすべてのポータルに直ちには伝播されない可能性があります。 場合によっては、変更が伝達されても、ポータルにおける Web キャッシュが原因で、変更がすぐに有効にならないことがあります。 アクティブ化が遅れている場合は、操作を実行しようとしているポータルからサインアウトしてから、もう一度サインインします。 Azure portal では、PIM により自動的にサインアウトし、もう一度サインインが行われます。

## <a name="next-steps"></a>次のステップ

- [Azure AD ロールに対する監査履歴を表示する](pim-how-to-use-audit-log.md)

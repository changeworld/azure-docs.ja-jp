---
title: PIM で Azure AD ロールに対する要求を承認または拒否する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールに対する要求を承認または拒否する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80965c0a1ff0ba14586ac99f08833f3a02a3a608
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667233"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールに対する要求を承認または拒否する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、アクティブ化の承認を必要とするようにロールを構成できます。また、代理承認者として 1 名以上のユーザーまたはグループを選択できます。 代理承認者は、要求を承認するまでに 24 時間あります。 要求が 24 時間以内に承認されない場合、有資格ユーザーは新しい要求を再送信する必要があります。 24 時間の承認時間枠は構成できません。

## <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure AD ロール要求が代理承認者による承認を待っている状態になると、メール通知を受け取ります。 これらの保留中の要求は、Privileged Identity Management で表示できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[要求の承認]** を選択します。

    ![申請の承認 - Azure AD ロールのレビューの要求を示すページ](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    **[ロールのアクティブ化に関する要求]** セクションに、承認が保留されている要求の一覧が表示されます。

## <a name="view-pending-requests-using-graph-api"></a>Graph API を使用して保留中の要求を表示する

### <a name="http-request"></a>HTTP 要求

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests/filterByCurrentUser(on='approver')?$filter=status eq 'PendingApproval' 
````

### <a name="http-response"></a>HTTP 応答

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleAssignmentScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleAssignmentScheduleRequest", 
            "id": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "status": "PendingApproval", 
            "createdDateTime": "2021-07-15T19:57:17.76Z", 
            "completedDateTime": "2021-07-15T19:57:17.537Z", 
            "approvalId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "customData": null, 
            "action": "SelfActivate", 
            "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
            "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "d96ea738-3b95-4ae7-9e19-78a083066d5b" 
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
    ] 
} 
````

## <a name="approve-requests"></a>要求の承認

1. 承認する要求を見つけて選択します。 承認または拒否のページが表示されます。

    ![[申請の承認 - Azure AD ロール] ページを示すスクリーンショット。](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[承認]** を選択します。 承認に関する Azure 通知を受信します。

    ![要求が承認されたことを示す承認の通知](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

## <a name="approve-pending-requests-using-graph-api"></a>Graph API を使用して保留中の要求を承認する

### <a name="get-ids-for-the-steps-that-require-approval"></a>承認が必要なステップの ID を取得する

特定のアクティブ化要求については、このコマンドによって、承認が必要とされる承認手順が取得されます。 現在、複数段階の承認はサポートされていません。

#### <a name="http-request"></a>HTTP 要求

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID> 
````

#### <a name="http-response"></a>HTTP 応答

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals/$entity", 
    "id": "<request-ID-GUID>",
    "steps@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals('<request-ID-GUID>')/steps", 
    "steps": [ 
        { 
            "id": "<approval-step-ID-GUID>", 
            "displayName": null, 
            "reviewedDateTime": null, 
            "reviewResult": "NotReviewed", 
            "status": "InProgress", 
            "assignedToMe": true, 
            "justification": "", 
            "reviewedBy": null 
        } 
    ] 
} 
````

### <a name="approve-the-activation-request-step"></a>アクティブ化要求の手順を承認する

#### <a name="http-request"></a>HTTP 要求

````HTTP
PATCH 
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID>/steps/<approval-step-ID-GUID> 
{ 
    "reviewResult": "Approve", 
    "justification": "abcdefg" 
} 
 ````

#### <a name="http-response"></a>HTTP 応答

PATCH の呼び出しに成功すると、空の応答が生成されます。

## <a name="deny-requests"></a>要求を拒否する

1. 拒否する要求を見つけて選択します。 承認または拒否のページが表示されます。

    ![申請の承認 - 詳細と [根拠] ボックスを含む承認または拒否のウィンドウ](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[理由]** ボックスに、業務上の正当な理由を入力します。

1. **[拒否]** を選択します。 拒否すると同時に通知が表示されます。

## <a name="workflow-notifications"></a>ワークフロー通知

ワークフロー通知に関するいくつかの情報を次に示します。

- ロールの要求のレビューが保留中の場合、承認者にメールで通知されます。 電子メール通知には、承認者が承認または拒否できる、要求への直接リンクが含まれています。
- 要求は、承認または拒否した最初の承認者によって解決されます。
- 承認者が要求に応答すると、すべての承認者にその動作が通知されます。
- 全体管理者と特権ロール管理者には、承認されたユーザーがそのロール内でアクティブになると通知されます。

>[!NOTE]
>全体管理者または特権ロール管理者は、承認されたユーザーをアクティブにするべきではないと判断した場合、Privileged Identity Management でアクティブなロールの割り当てを削除できます。 管理者は、承認者でない限り、保留中の要求の通知を受け取りませんが、Privileged Identity Management で保留中の要求を表示することで、すべてのユーザーの保留中の要求を確認およびキャンセルできます。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management での電子メール通知](pim-email-notifications.md)
- [Privileged Identity Management で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)

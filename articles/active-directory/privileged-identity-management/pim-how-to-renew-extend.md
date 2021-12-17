---
title: PIM で Azure AD リソース ロール割り当てを延長または更新する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure Active Directory ロール割り当てを延長または更新する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/19/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51385ce98da9e163be0d70d0c0662f51094094a3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222516"
---
# <a name="extend-or-renew-azure-ad-role-assignments-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールの割り当てを延長または更新する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) には、Azure AD ロールへのアクセスと割り当てのライフサイクルを管理するための新しいコントロールが提供されています。 管理者は、[開始日時] と [終了日時] プロパティを使用してメンバーシップを割り当てることができます。 割り当ての終了日時が近づくと、Privileged Identity Management は、影響を受けるユーザーまたはグループに電子メール通知を送信します。 適切なアクセスが維持されることを保証するために Azure AD の管理者にも電子メール通知を送信します。 アクセスが延長されなかった場合でも、割り当てを更新して、最大 30 日間、期限切れの状態のままで表示できます。

## <a name="who-can-extend-and-renew"></a>延長と更新を実行できるのは誰か

Azure AD ロールの割り当てを拡張または更新できるのは、グローバル管理者または特権ロール管理者のみです。 影響を受けるユーザーまたはグループは、まもなく期限切れになるロールの延長と、既に期限切れになっているロールの更新を要求できます。

## <a name="when-are-notifications-sent"></a>通知の送信時機

Privileged Identity Management は、14 日以内に期限切れになるロールの管理者と影響を受けるユーザーまたはグループに電子メール通知を送信します。電子メール通知は、有効期限の前日にも送信されます。 電子メールは、割り当てが正式に期限切れになったときにも送信されます。

管理者は、ユーザーまたはグループが期限が迫っているか期限切れになっているロールが期限の延長または更新を要求したときに通知を受信します。 管理者が承認または拒否として要求を解決すると、他のすべての管理者に決定が通知されます。 その後、要求したユーザーまたはグループに決定が通知されます。

## <a name="extend-role-assignments"></a>ロールの割り当てを延長する

ロールの割り当ての延長または更新の要求、解決、または管理に関連する手順とユーザー インターフェイスの要点を次に示します。

### <a name="self-extend-expiring-assignments"></a>有効期限が切れた割り当ての自動延長

ロールに割り当てられたユーザーは、 **[自分のロール]** ページ ( **[Azure AD ロール]** か、Privileged Identity Management ポータルの最上位レベルの **[自分のロール]** ページのいずれか) の **[有資格]** または **[アクティブ]** タブから、有効期限が切れるロールの割り当てを直接延長できます。 このポータルでは、ユーザーは 14 日後に期限切れになる有資格ロールと (割り当て済みの) アクティブ ロールの延長を要求できます。

![資格のあるロールと [アクション] 列が一覧表示されている [Azure AD ロール] - [自分のロール] ページ](./media/pim-how-to-renew-extend/pim-extend-link-in-portal.png)

割り当ての終了日時が 14 日以内にある場合は、ユーザー インターフェイスの **延長** ボタンがアクティブ リンクになります。 次の例では、現在の日付が 3 月 27 日であると想定しています。

>[!Note]
>ロールに割り当てられたグループの場合は、割り当てを継承している 1 人のユーザーがグループの割り当てを延長できないように、 **[延長]** リンクが利用できるようになることはありません。

![アクティブ化または延長するためのリンクがある [アクション] 列](./media/pim-how-to-renew-extend/pim-extend-within-fourteen.png)

このロールの割り当ての延長を要求するには、**延長** をクリックして要求フォームを開きます。

![[理由] ボックスがある [Extend role assignment]/(ロールの割り当ての延長/) ウィンドウ](./media/pim-how-to-renew-extend/extend-role-assignment-request.png)

延長要求の理由を入力し、**延長** をクリックします。

>[!NOTE]
>延長が必要な詳しい理由と希望の延長期間 (わかっている場合) を入力することをお勧めします。

管理者は、延長要求を確認する電子メール通知を受け取ります。 延長の要求が既に送信されている場合は、Azure の通知がポータルに表示されます。

![保留になっている既存のロール割り当ての延長が既にあることを示す通知](./media/pim-how-to-renew-extend/extend-notification.png)

**[保留中の要求]** ページに移動して、要求の状態を確認します。要求をキャンセルすることもできます。

![保留中の要求とキャンセルのためのリンクが一覧表示されている [Azure AD ロール] - [保留中の要求] ページ](./media/pim-how-to-renew-extend/pending-requests.png)

### <a name="admin-approved-extension"></a>管理者に承認された延長

ユーザーまたはグループがグループの割り当ての延長を求める要求を送信すると、管理者は、元の割り当ての詳細と要求の理由を含む電子メール通知を受信します。 通知には、管理者が要求を承認または拒否するための直接リンクが含まれています。

電子メール内のリンクに加え、管理者は、Privileged Identity Management の管理ポータルに移動し、左側のペインから **[要求の承認]** を選択することで、要求を承認または拒否できます。

![要求と承認または拒否するためのリンクが一覧表示されている [Azure AD ロール] - [要求の承認] ページ](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

管理者が **[承認]** または **[拒否]** を選択すると、要求の詳細が、監査ログ用の業務上の正当な理由を入力するフィールドと共に表示されます。

![[要求元の理由]、[割り当ての種類]、[開始時刻]、[終了時刻]、および [理由] がある [Approve role assignment request]/(ロールの割り当て要求の承認/)](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

要求を承認してロールの割り当てを延長するとき、管理者は、新しい開始日時、終了日時、および割り当ての種類を選択できます。 管理者が特定のタスク を完了するためのアクセス制限 を指定する (たとえば 1 日だけ延長する) 場合は、割り当ての種類の変更が必要になることがあります。 この例では、管理者は、割り当ての種類を **有資格** から **アクティブ** に変更できます。 つまり、アクティブ化の必要がないアクセスを要求者に提供できます。

### <a name="admin-initiated-extension"></a>管理者による延長

ロールに割り当てられたユーザーがロールの割り当ての延長を要求しない場合、管理者がユーザーの代わりに割り当てを延長できます。 管理者によるロールの割り当ての延長には承認は必要ありませんが、ロールの延長の完了時に他のすべての管理者に通知が送信されます。

ロールの割り当てを延長するには、Privileged Identity Management のロールまたは割り当てビューを参照します。 延長する必要がある割り当てを探します。 そして、[アクション] 列の **延長** をクリックします。

![資格のあるロールと延長のためのリンクが一覧表示されている [Azure AD ロール] - [割り当て] ページ](./media/pim-how-to-renew-extend/extend-admin-extend.png)

## <a name="extend-role-assignments-using-graph-api"></a>Graph API を使用したロールの割り当ての延長

Graph API を使用してアクティブな割り当てを延長します。

#### <a name="http-request"></a>HTTP 要求

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 
 
{ 
    "action": "AdminExtend", 
    "justification": "abcde", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    `"principalId": "<principal-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "AfterDuration", 
            "duration": "PT3H" 
        } 
    } 
}
````

#### <a name="http-response"></a>HTTP 応答

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "<assignment-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T20:26:44.865248Z", 
    "completedDateTime": "2021-07-15T20:26:47.9434068Z", 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminExtend", 
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
        "startDateTime": "2021-07-15T20:26:47.9434068Z", 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT3H" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="renew-role-assignments"></a>ロールの割り当ての更新

概念上は似ていますが、有効期限が切れたロールの割り当てを更新するプロセスは、メンバーにとっても管理者にとっても、延長の要求とは異なっています。 割り当てと管理者は、次の手順を使用して、必要な場合に有効期限が切れたロールへのアクセスを更新できます。

### <a name="self-renew"></a>自己更新

リソースにアクセスできなくなったユーザーは、最大 30 日間分の有効期限が切れた割り当て履歴にアクセスできます。 これを行うには、左ペインで **[自分のロール]** に移動し、[Azure AD ロール] セクションの **[期限切れのロール]** タブを選択します。

![[自分のロール] ページ - [期限切れのロール] タブ](./media/pim-how-to-renew-extend/renew-from-myroles.png)

ロールの一覧には、**有資格の割り当て** が既定で表示されます。 **[有資格]** または **[アクティブ]** の割り当て済みロールを選択します。

一覧のいずれかのロールの割り当ての更新を要求するには、**更新** アクションを選択します。 そして、要求の理由を入力します。 詳しい理由または業務上の正当な理由を加えて延長期間を入力すると、管理者が承認するか拒否するかを決定する際に役立ちます。

![[理由] ボックスが表示されている [ロールの割り当ての更新] ウィンドウ](./media/pim-how-to-renew-extend/renew-request-form.png)

要求が送信されると、管理者は、ロールの割り当ての更新を求めている保留中の要求の通知を受信します。

### <a name="admin-approves"></a>管理者による承認

Azure AD 管理者は、電子メール通知内のリンクから更新要求にアクセスできます。Azure portal から Privileged Identity Management にアクセスし、PIM の **[要求の承認]** を選択してもアクセスできます。

![要求と承認または拒否するためのリンクが一覧表示されている [Azure AD ロール] - [要求の承認] ページ](./media/pim-how-to-renew-extend/extend-admin-approve-list.png)

管理者が **[承認]** または **[拒否]** を選択すると、要求の詳細が、監査ログ用の業務上の正当な理由を入力するフィールドと共に表示されます。

![[要求元の理由]、[割り当ての種類]、[開始時刻]、[終了時刻]、および [理由] がある [Approve role assignment request]/(ロールの割り当て要求の承認/)](./media/pim-how-to-renew-extend/extend-admin-approve-form.png)

要求を承認してロールの割り当てを更新するとき、管理者は、新しい開始日時、終了日時、および割り当ての種類を入力する必要があります。

### <a name="admin-renew"></a>管理者による更新

Azure AD ロールの **[期限切れのロール]** タブで、有効期限が切れているロールの割り当てを更新することもできます。 有効期限が切れたすべてのロールの割り当ての一覧を表示するには、 **[割り当て]** 画面で **[期限切れのロール]** を選択します。

![有効期限が切れたロールと更新のためのリンクが一覧表示されている [Azure AD ロール] - [割り当て] ページ](./media/pim-how-to-renew-extend/renew-from-assignments-pane.png)

## <a name="next-steps"></a>次の手順

- [Privileged Identity Management で Azure AD ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
- [Privileged Identity Management で Azure AD ロールの設定を構成する](pim-how-to-change-default-settings.md)

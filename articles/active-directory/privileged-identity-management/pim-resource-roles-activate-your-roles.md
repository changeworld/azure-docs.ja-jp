---
title: PIM で Azure リソース ロールをアクティブ化する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールをアクティブにする方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c87f4d4baff8b19ea7512ac387525a31339e7294
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669057"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で自分の Azure リソース ロールをアクティブ化する

Azure リソースの適格なロール メンバーは、Privileged Identity Management (PIM) を使用して、将来の日時のアクティブ化をスケジュールできます。 最大範囲 (管理者が設定) 内で特定のアクティブ化期間を選択することもできます｡

この記事の対象者は、Privileged Identity Management で自分の Azure リソース ロールをアクティブ化する必要のあるメンバーです。

## <a name="activate-a-role"></a>ロールのアクティブ化

Azure リソース ロールを引き受ける必要がある場合は、Privileged Identity Management の **[自分のロール]** ナビゲーション オプションを使用してアクティブ化を要求できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。 ダッシュボードに [Privileged Identity Management] タイルを追加する方法については、「[Privileged Identity Management の使用開始](pim-getting-started.md)」を参照してください。

1. **[自分のロール]** を選択します。

    ![アクティブ化できるロールを表示した [自分のロール] ページ](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. **[Azure リソース ロール]** を選択して、適格な Azure リソース ロールの一覧を表示します。

    ![[自分のロール - Azure リソース ロール] ページ](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. **[Azure リソース ロール]** の一覧で、アクティブにするロールを見つけます。

    ![[Azure リソース ロール] - 自分の [資格のあるロール] の一覧](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **[アクティブ化]** を選択して、[アクティブ化] ページを開きます。

    ![開いた [アクティブ化] ウィンドウと、スコープ、開始時刻、期間、理由](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. お使いのロールで多要素認証が必要な場合は、 **[続行する前に ID を確認してください]** をクリックします。 認証は、セッションごとに 1 回だけ行う必要があります。

    ![ロールをアクティブ化する前に MFA で自分の ID を確認する](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **[ID を確認]** を選択し、指示に従って追加のセキュリティ確認を提供します。

    ![PIN コードなどのセキュリティ確認を提供するための画面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. より狭いスコープを指定する場合は、 **[Scope]\(スコープ\)** を選択して [リソース フィルター] ウィンドウを開きます。

    ベスト プラクティスは、必要なリソースへのアクセスのみを要求することです。 [リソース フィルター] ウィンドウでは、アクセスする必要があるリソース グループまたはリソースを指定できます。

    ![[アクティブ化] - スコープを指定する [リソース フィルター] ウィンドウ](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。 メンバーは、選択した時刻になるとアクティブになります。

1. **[理由]** ボックスに、アクティブ化要求の理由を入力します。

    ![スコープ、開始時刻、期間、理由が入力された [アクティブ化] ウィンドウ](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **[アクティブ化]** を選びます。

    アクティブ化に[承認が必要なロール](pim-resource-roles-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![アクティブ化要求は承認通知待ち中](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-with-arm-api"></a>ARM API を使用してロールをアクティブ化する

Privileged Identity Management では、[PIM ARM API リファレンス](/rest/api/authorization/roleeligibilityschedulerequests)に関するページに記載のとおり、Azure リソース ロールの管理で、Azure Resource Manager (ARM) API コマンドをサポートしています。 PIM API の使用に必要なアクセス許可については、「[Privileged Identity Management API について理解する](pim-apis.md)」を参照してください。

資格のある Azure ロールの割り当てをアクティブ化する HTTP 要求のサンプルを次に示します。

### <a name="request"></a>Request

````HTTP
PUT https://management.azure.com/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleEligibilityScheduleRequests/64caffb6-55c0-4deb-a585-68e948ea1ad6?api-version=2020-10-01-preview
````

### <a name="request-body"></a>要求本文

````JSON
{
  "properties": {
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "requestType": "SelfActivate",
    "linkedRoleEligibilityScheduleId": "b1477448-2cc6-4ceb-93b4-54a202a89413",
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
      }
    },
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0"
  }
}
````

### <a name="response"></a>Response

状態コード: 201

````HTTP
{
  "properties": {
    "targetRoleAssignmentScheduleId": "c9e264ff-3133-4776-a81a-ebc7c33c8ec6",
    "targetRoleAssignmentScheduleInstanceId": null,
    "scope": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "principalType": "User",
    "requestType": "SelfActivate",
    "status": "Provisioned",
    "approvalId": null,
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:35:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "PT8H"
      }
    },
    "ticketInfo": {
      "ticketNumber": null,
      "ticketSystem": null
    },
    "justification": null,
    "requestorId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "createdOn": "2020-09-09T21:35:27.91Z",
    "condition": "@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:ContainerName] StringEqualsIgnoreCase 'foo_storage_container'",
    "conditionVersion": "1.0",
    "expandedProperties": {
      "scope": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
        "displayName": "Pay-As-You-Go",
        "type": "subscription"
      },
      "roleDefinition": {
        "id": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
        "displayName": "Contributor",
        "type": "BuiltInRole"
      },
      "principal": {
        "id": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
        "displayName": "User Account",
        "email": "user@my-tenant.com",
        "type": "User"
      }
    }
  },
  "name": "fea7a502-9a96-4806-a26f-eee560e52045",
  "id": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/RoleAssignmentScheduleRequests/fea7a502-9a96-4806-a26f-eee560e52045",
  "type": "Microsoft.Authorization/RoleAssignmentScheduleRequests"
}
````

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択して、Azure AD ロールおよび Azure リソース ロール要求の一覧を表示します。

    ![承認待ち中の要求を表示した [個人の要求 - Azure リソース] ページ](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 右へスクロールして **[Request Status]\(要求の状態\)** 列を表示します。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択します。

1. 取り消すロールの **[キャンセル]** リンクを選択します。

    [キャンセル] を選択すると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![[キャンセル] アクションが強調表示された個人の要求の一覧](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="permissions-are-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

Privileged Identity Management でロールをアクティブ化しても、アクティブ化が、特権ロールを必要とするすべてのポータルに直ちには伝播されない可能性があります。 場合によっては、変更が伝達されても、ポータルにおける Web キャッシュが原因で、変更がすぐに有効にならないことがあります。 アクティブ化が遅延している場合は、次の手順を行ってください。

1. Azure portal からサインアウトした後に、もう一度サインインします。
1. Privileged Identity Management で、自分がそのロールのメンバーとして一覧表示されていることを確認します。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management で自分の Azure AD ロールをアクティブ化する](pim-how-to-activate-role.md)

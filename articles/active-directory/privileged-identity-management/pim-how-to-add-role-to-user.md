---
title: PIM で Azure AD ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールを割り当てる方法を説明します。
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
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: 3ea66bbd5708e5964bb2c258feaa1fc44fcecff1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444195"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールを割り当てる

全体管理者は、Azure Active Directory (Azure AD) を使用して、**永続的** な Azure AD 管理者ロールの割り当てを行うことができます。 こうしたロールの割り当ては、[Azure portal](../roles/permissions-reference.md) または [PowerShell のコマンド](/powershell/module/azuread#directory_roles) を使用して作成できます。

永続的な管理ロールの割り当ては、特権ロール管理者が Azure AD Privileged Identity Management (PIM) サービスを使用して行うこともできます。 さらに、特権ロール管理者は、ユーザーを Azure AD 管理者ロールの **候補** にすることもできます。 管理者候補は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。

Privileged Identity Management では、組み込みとカスタムの両方の Azure AD ロールがサポートされます。 Azure AD のカスタム ロールの詳細については、[Azure Active Directory のロールベースのアクセス制御](../roles/custom-overview.md)に関する記事を参照してください。

## <a name="assign-a-role"></a>ロールの割り当て

ユーザーを Azure AD 管理者ロールの候補にするには、次の手順を実行します。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[ロール]** を選択して、Azure AD のアクセス許可のロール一覧を表示します。

    ![[割り当ての追加] 操作が選択されている [ロール] ページのスクリーンショット。](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **[割り当ての追加]** を選択して、 **[割り当ての追加]** ページを開きます。

1. **[ロールを選択]** を選択して **[ロールを選択]** ページを開きます。

    ![[新しい割り当て] ウィンドウ](./media/pim-how-to-add-role-to-user/select-role.png)

1. 割り当てるロールを選択し、ロールを割り当てるメンバーを選択してから、 **[次へ]** を選択します。

1. **[メンバーシップ設定]** ウィンドウの **[割り当ての種類]** リストで、 **[対象]** または **[アクティブ]** を選択します。

    - **[対象]** 割り当ての場合、このロールのメンバーは、ロールを使用するにはアクションを実行する必要があります。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

    - **[アクティブ]** 割り当ての場合、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

1. 特定の割り当て期間を指定するには、開始日時と終了日時のボックスを追加します。 完了したら、 **[割り当て]** を選択して、新しいロールの割り当てを作成します。

    - **永続的** な割り当てに有効期限はありません。 このオプションは、ロールのアクセス許可を頻繁に必要とする永続的なワーカーに対して使用します。

    - **期限付き** 割り当ては、指定された期間の終了時に期限切れになります。 このオプションは、たとえばプロジェクトの終了日時がわかっている、一時的なまたはコントラクト ワーカーで使用します。

    ![メンバー シップ設定 - 日付と時刻](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. ロールが割り当てられると、割り当て状態の通知が表示されます。

    ![新しい割り当て - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>制限スコープがあるロールを割り当てる

特定のロールでは、付与されるアクセス許可の範囲を 1 つの管理単位、サービス プリンシパル、またはアプリケーションに制限することができます。 この手順は、管理単位のスコープを持つロールを割り当てる場合の例です。 管理単位を介してスコープをサポートするロールの一覧については、「[スコープ付きロールを管理単位に割り当てる](../roles/admin-units-assign-roles.md)」を参照してください。 この機能は、現在 Azure AD 組織に展開されています。

1. 特権ロール管理者アクセス許可を使用して、[Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[ロールと管理者]** を選択します。

1. **[ユーザー管理者]** を選択します。

    ![ポータルでロールを開くと、[割り当ての追加] コマンドを使用できます](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. **[割り当ての追加]** を選択します。

    ![ロールがスコープをサポートしている場合は、スコープを選択できます](./media/pim-how-to-add-role-to-user/add-scope.png)

1. **[割り当ての追加]** ページでは、次のことができます。

   - ロールに割り当てるユーザーまたはグループを選択する
   - ロールのスコープ (この場合は管理単位) を選択する
   - スコープの管理単位を選択する

管理単位の作成の詳細については、[管理単位の追加と削除](../roles/admin-units-manage.md)に関するページを参照してください。

## <a name="assign-a-role-using-graph-api"></a>Graph API を使用してロールを割り当てる

PIM API の使用に必要なアクセス許可については、「[Privileged Identity Management API について理解する](pim-apis.md)」を参照してください。 

### <a name="eligible-with-no-end-date"></a>終了日なしの有資格

次に、終了日のない、資格のある割り当てを作成するための HTTP 要求の例を示します。 C# や JavaScript などのサンプルを含む API コマンドの詳細については、[unifiedRoleEligibilityScheduleRequest の作成](/graph/api/unifiedroleeligibilityschedulerequest-post-unifiedroleeligibilityschedulerequests?view=graph-rest-beta&tabs=http&preserve-view=true)に関するページを参照してください。

#### <a name="http-request"></a>HTTP 要求

````HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests 

    "action": "AdminAssign", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "NoExpiration"        } 
    } 
{ 
} 
````

#### <a name="http-response"></a>HTTP 応答

応答の例を次に示します。 ここに示されている応答オブジェクトは、読みやすくするために短縮されている可能性があります。

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleEligibilityScheduleRequests/$entity", 
    "id": "<schedule-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T19:47:41.0939004Z", 
    "completedDateTime": "2021-07-15T19:47:42.4376681Z", 
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
        "startDateTime": "2021-07-15T19:47:42.4376681Z", 
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
}   
````

### <a name="active-and-time-bound"></a>アクティブで期限付き

次に、期限付きのアクティブな割り当てを作成するための HTTP 要求の例を示します。 C# や JavaScript などのサンプルを含む API コマンドの詳細については、[unifiedRoleEligibilityScheduleRequest の作成](/graph/api/unifiedroleeligibilityschedulerequest-post-unifiedroleeligibilityschedulerequests?view=graph-rest-beta&tabs=http&preserve-view=true)に関するページを参照してください。

#### <a name="http-request"></a>HTTP 要求

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 

{ 
    "action": "AdminAssign", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
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

応答の例を次に示します。 ここに示されている応答オブジェクトは、読みやすくするために短縮されている可能性があります。

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "<schedule-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T19:15:09.7093491Z", 
    "completedDateTime": "2021-07-15T19:15:11.4437343Z", 
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
        "startDateTime": "2021-07-15T19:15:11.4437343Z", 
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

## <a name="update-or-remove-an-existing-role-assignment"></a>既存のロールの割り当てを更新または削除する

既存のロールの割り当てを更新または削除するには、次の手順を実行します。 **Azure AD P2 ライセンス供与されたお客様のみ**:Azure AD と Privileged Identity Management (PIM) の両方を使用して、グループをアクティブとしてロールに割り当てないでください。 詳細については、「[既知の問題](../roles/groups-concept.md#known-issues)」を参照してください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[ロール]** を選択して、Azure AD のロール一覧を表示します。

1. 更新または削除するロールを選択します。

1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。

    ![ロールの割り当ての更新または削除](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. **[更新]** または **[削除]** を選択して、ロールの割り当てを更新または削除します。

## <a name="remove-eligible-assignment-via-api"></a>API を使用して資格のある割り当てを削除する

### <a name="request"></a>Request

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleEligibilityScheduleRequests 

 

{ 
    "action": "AdminRemove", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
    "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b" 
} 
````

### <a name="response"></a>Response

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleEligibilityScheduleRequests/$entity", 
    "id": "fc7bb2ca-b505-4ca7-ad2a-576d152633de", 
    "status": "Revoked", 
    "createdDateTime": "2021-07-15T20:23:23.85453Z", 
    "completedDateTime": null, 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminRemove", 
    "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
    "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": null, 
    "justification": "test", 
    "scheduleInfo": null, 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "5d851eeb-b593-4d43-a78d-c8bd2f5144d2" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD 管理者ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [Privileged Identity Management で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)

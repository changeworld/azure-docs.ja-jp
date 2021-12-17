---
title: Privileged Identity Management で Azure リソース ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールを割り当てる方法を説明します。
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
ms.date: 09/28/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0837fc83bec560f12c31da1d9f0001b5bdd7768f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669931"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールを割り当てる

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) では、組み込みの Azure リソース ロールと、以下を含むカスタム ロール (ただしこれらに限定されません) を管理できます。

- 所有者
- User Access Administrator
- Contributor
- セキュリティ管理者
- Security Manager

> [!NOTE]
> Azure AD でサブスクリプション管理を有効にする、所有者またはユーザー アクセス管理者サブスクリプション ロールに割り当てられたグループのユーザーまたはメンバー、および Azure AD 全体管理者には、既定でリソース管理者のアクセス許可が与えられます。 これらの管理者は、ロールの割り当て、ロール設定の構成、Azure リソース用 Privileged Identity Management を使用したアクセスの確認ができます。 リソース管理者のアクセス許可がないユーザーは、リソース用 Privileged Identity Management を管理できません。 [Azure の組み込みロール](../../role-based-access-control/built-in-roles.md)の一覧を表示します。

Privileged Identity Management では、組み込みとカスタムの両方の Azure ロールがサポートされます。 Azure カスタム ロールの詳細については、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="role-assignment-conditions"></a>ロールの割り当て条件

Azure ABAC (Azure の属性ベースのアクセス制御) プレビューを使用すると、資格のあるロールの割り当てに対し、Privileged Identity Management (PIM) を使用してリソースの条件を設定できます。 PIM を使用する場合、エンド ユーザーは、資格のあるロールの割り当てをアクティブ化して、特定のアクションを実行するためのアクセス許可を取得する必要があります。 PIM で Azure の属性を使用したアクセス制御条件を使用すると、リソースに対するユーザーのロールでのアクセス許可をきめ細かな条件を使用して制限できるだけでなく、PIM を使用して、期限付きの設定、承認ワークフロー、監査証跡などでロールの割り当てを保護することもできます。 詳細については、[Azure の属性ベースのアクセス制御 (パブリック プレビュー)](../../role-based-access-control/conditions-overview.md) に関するページを参照してください。

## <a name="assign-a-role"></a>ロールの割り当て

ユーザーを Azure リソース ロールの候補にするには、次の手順を実行します。

1. 所有者またはユーザー アクセス管理者ロールのアクセス許可で [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. リソース フィルターを使用して、探している管理対象リソースを検索します。

    ![管理する Azure リソースの一覧](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 管理するリソースを選択して、リソース概要ページを開きます。

1. **[管理]** で **[ロール]** を選択して、Azure リソースのロール一覧を表示します。

    ![Azure リソース ロール](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **[割り当ての追加]** を選択して、 **[割り当ての追加]** ペインを開きます。

1. **[ロールを選択]** を選択して **[ロールを選択]** ページを開きます。

    ![[新しい割り当て] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 割り当てるロールを選択し、 **[選択]** をクリックします。

    **[メンバーまたはグループの選択]** ペインが開きます。

1. ロールに割り当てるメンバーまたはグループを選択し、 **[選択]** をクリックします。

    ![[メンバーまたはグループの選択] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. **[設定]** タブの **[割り当ての種類]** リストで **[対象]** または **[アクティブ]** を選択します。

    ![[メンバーシップ設定] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure リソース向けの Privileged Identity Management には、2 つの明確な割り当ての種類があります。

    - **[対象]** 割り当ての場合、このロールのメンバーは、ロールを使用するにはアクションを実行する必要があります。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

    - **[アクティブ]** 割り当ての場合、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

1. 特定の割り当て期間を指定するには、開始日時と終了日時を変更します。

1. 完了したら、 **[割り当て]** を選択します。

1. 新しいロールの割り当てが作成されると、状態の通知が表示されます。

    ![新しい割り当て - 通知](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="assign-a-role-using-arm-api"></a>ARM API を使用したロールの割り当て

Privileged Identity Management では、[PIM ARM API リファレンス](/rest/api/authorization/roleeligibilityschedulerequests)に関するページに記載のとおり、Azure リソース ロールの管理で、Azure Resource Manager (ARM) API コマンドをサポートしています。 PIM API の使用に必要なアクセス許可については、「[Privileged Identity Management API について理解する](pim-apis.md)」を参照してください。

次に、Azure のロールに資格のある割り当てを作成するための HTTP 要求の例を示します。

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
    "requestType": "AdminAssign",
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:31:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "P365D"
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
    "targetRoleEligibilityScheduleId": "b1477448-2cc6-4ceb-93b4-54a202a89413",
    "targetRoleEligibilityScheduleInstanceId": null,
    "scope": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f",
    "roleDefinitionId": "/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
    "principalId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "principalType": "User",
    "requestType": "AdminAssign",
    "status": "Provisioned",
    "approvalId": null,
    "scheduleInfo": {
      "startDateTime": "2020-09-09T21:31:27.91Z",
      "expiration": {
        "type": "AfterDuration",
        "endDateTime": null,
        "duration": "P365D"
      }
    },
    "ticketInfo": {
      "ticketNumber": null,
      "ticketSystem": null
    },
    "justification": null,
    "requestorId": "a3bb8764-cb92-4276-9d2a-ca1e895e55ea",
    "createdOn": "2020-09-09T21:32:27.91Z",
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
  "name": "64caffb6-55c0-4deb-a585-68e948ea1ad6",
  "id": "/providers/Microsoft.Subscription/subscriptions/dfa2a084-766f-4003-8ae1-c4aeb893a99f/providers/Microsoft.Authorization/RoleEligibilityScheduleRequests/64caffb6-55c0-4deb-a585-68e948ea1ad6",
  "type": "Microsoft.Authorization/RoleEligibilityScheduleRequests"
}
````

## <a name="update-or-remove-an-existing-role-assignment"></a>既存のロールの割り当てを更新または削除する

既存のロールの割り当てを更新または削除するには、次の手順を実行します。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. 管理するリソースを選択して、その概要ページを開きます。

1. **[管理]** で **[ロール]** を選択して、Azure リソースのロール一覧を表示します。

    ![Azure リソース ロール - ロールの選択](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 更新または削除するロールを選択します。

1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。

    ![ロールの割り当ての更新または削除](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Azure リソースへのアクセスを細かく制限するための条件を追加または更新するには、ロールの割り当ての **[条件]** 列で **[追加]** または **[表示/編集]** を選択します。 現在、[Azure の属性ベースのアクセス制御パブリック プレビュー](../../role-based-access-control/conditions-overview.md)でサポートされる Privileged Identity Management のロールは、ストレージ BLOB データ所有者、ストレージ BLOB データ閲覧者、Blob Storage BLOB データ共同作成者のみです。

    ![アクセスの制御に使用される属性を更新または削除する](./media/pim-resource-roles-assign-roles/resources-abac-update-remove.png)

1. **[更新]** または **[削除]** を選択して、ロールの割り当てを更新または削除します。

    ロールの割り当てを延長する方法については、[Privileged Identity Management で Azure リソース ロールを延長または更新する方法](pim-resource-roles-renew-extend.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)

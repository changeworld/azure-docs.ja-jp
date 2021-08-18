---
title: PIM で Azure AD ロールをアクティブ化する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールをアクティブにする方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3926fb277251f7020539942c76d9c97f0ce46d75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736170"
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

1. **[追加の検証が必要]** "** を選択し、指示に従って追加のセキュリティ確認を提供します。 認証が要求されるのは、各セッションにつき 1 回だけです。

    ![PIN コードなどのセキュリティ確認を提供するための画面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 多要素認証の後に、 **[続行する前にアクティブにする]** を選択します。

    ![ロールをアクティブ化する前に MFA で自分の ID を確認する](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. より狭いスコープを指定する場合は、 **[Scope]\(スコープ\)** を選択して [フィルター] ペインを開きます。 [フィルター] ペインでは、アクセス権が必要な Azure AD リソースを指定できます。 必要最低限のリソースに対するアクセスを要求することをお勧めします。

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。 Azure AD ロールは、選択した時刻になるとアクティブになります。

1. **[理由]** ボックスに、アクティブ化要求の理由を入力します。

1. **[アクティブ化]** を選びます。

    アクティブ化に[承認が必要なロール](pim-resource-roles-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![アクティブ化要求は承認通知待ち中](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>新しいバージョンの要求の状態を表示する

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

## <a name="troubleshoot-for-new-version"></a>新しいバージョンの問題を解決する

### <a name="permissions-are-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

Privileged Identity Management でロールをアクティブ化しても、アクティブ化が、特権ロールを必要とするすべてのポータルに直ちには伝播されない可能性があります。 場合によっては、変更が伝達されても、ポータルにおける Web キャッシュが原因で、変更がすぐに有効にならないことがあります。 アクティブ化が遅れている場合は、操作を実行しようとしているポータルからサインアウトしてから、もう一度サインインします。 Azure portal では、PIM により自動的にサインアウトし、もう一度サインインが行われます。

## <a name="next-steps"></a>次のステップ

- [Azure AD ロールに対する監査履歴を表示する](pim-how-to-use-audit-log.md)

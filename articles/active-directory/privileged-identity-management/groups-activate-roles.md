---
title: PIM で特権アクセス グループ ロールをアクティブにする - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で特権アクセス グループ ロールをアクティブにする方法を説明します。
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
ms.openlocfilehash: 365f480af81289a9f48dd6ce5a1102be7a7ed6a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444157"
---
# <a name="activate-my-privileged-access-group-roles-in-privileged-identity-management"></a>Privileged Identity Management で特権アクセス グループ ロールをアクティブにする

Privileged Identity Management (PIM) を使用して、特権アクセス グループの対象のロール メンバーが、指定した日付と時刻にロールのアクティブ化をスケジュールできるようにします。 管理者が構成した最大期間までのアクティブ化期間を選択することもできます｡

この記事の対象は、Privileged Identity Management で特権アクセス グループ ロールをアクティブ化したいと考える、対象メンバーです。

## <a name="activate-a-role"></a>ロールのアクティブ化

特権アクセス グループ ロールを引き受ける必要がある場合は、Privileged Identity Management の **[自分のロール]** ナビゲーション オプションを使用してアクティブ化を要求できます。

1. 全体管理者またはグループ所有者のアクセス許可を使用して [Azure AD ポータルにサインイン](https://aad.portal.azure.com)します。

1. [[Privileged Identity Management]](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) を開きます。

1. **[Privileged access groups (Preview)]\(特権アクセス グループ (プレビュー)\)** を選択し、 **[ロールのアクティブ化]** を選択して特権アクセス グループの **[自分のロール]** ページを開きます。

    ![PIM の特権アクセス ロール ページ](./media/groups-activate-roles/groups-select-group.png)

1. **[自分のロール]** ページの、アクティブ化する対象の割り当ての行で **[アクティブ化]** を選択します。

    ![対象のロール割り当て行の [アクティブ化] リンク](./media/groups-activate-roles/groups-activate-link.png)

1. お使いのロールで多要素認証が必要な場合は、 **[続行する前に ID を確認してください]** をクリックします。 認証は、セッションごとに 1 回だけ行う必要があります。

    ![ロールをアクティブ化する前に MFA で自分の ID を確認する](./media/groups-activate-roles/groups-my-roles-mfa.png)

1. **[ID を確認]** を選択し、指示に従って追加のセキュリティ確認を提供します。

    ![PIN コードなどのセキュリティ確認を提供するための画面](./media/groups-activate-roles/groups-mfa-enter-code.png)

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。 メンバーまたは所有者は、選択した時間が経過してからのみアクティブ化されます。

1. **[理由]** ボックスに、アクティブ化要求の理由を入力します。

    ![期間と理由を含む [アクティブ化] ページ](./media/groups-activate-roles/groups-activate-page.png)

1. **[アクティブ化]** を選びます。

アクティブ化するために[ロールで承認が必要](pim-resource-roles-approval-workflow.md)な場合は、ブラウザーの右上隅に Azure の通知が表示され、要求が承認待ちになっていることが示されます。

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択して、Azure AD ロールおよび特権アクセス グループ ロール要求の一覧を表示します。

1. 必要に応じて右へスクロールして **[申請の状態]** 列を表示します。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[My requests]\(個人の要求\)** を選択します。

1. 取り消すロールの **[キャンセル]** リンクを選択します。

    **[キャンセル]** を選択すると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="permissions-are-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

Privileged Identity Management でロールをアクティブ化しても、アクティブ化が、特権ロールを必要とするすべてのポータルに直ちには伝播されない可能性があります。 場合によっては、変更が伝達されても、ポータルにおける Web キャッシュが原因で、変更がすぐに有効にならないことがあります。 アクティブ化が遅延している場合は、次の手順を行ってください。

1. Azure portal からサインアウトした後に、もう一度サインインします。
1. Privileged Identity Management で、自分がそのロールのメンバーとして一覧表示されていることを確認します。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で特権アクセス グループ ロールを延長または更新する](groups-renew-extend.md)
- [Privileged Identity Management で特権アクセス グループ ロールを割り当てる](groups-assign-member-owner.md)

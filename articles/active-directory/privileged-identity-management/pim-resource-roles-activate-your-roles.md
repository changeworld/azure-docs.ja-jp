---
title: PIM で自分の Azure リソース ロールをアクティブにする | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールをアクティブにする方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e37435b0f2d52e2015caf3d2e0db6db0dba60c75
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187353"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>PIM で自分の Azure リソース ロールをアクティブにする

Azure リソースの適格なロール メンバーは、Azure AD Privileged Identity Management (PIM) を使用して、将来の日時のアクティブ化をスケジュールできます。 最大範囲 (管理者が設定) 内で特定のアクティブ化期間を選択することもできます｡

この記事は、PIM で Azure リソース ロールをアクティブにする必要があるメンバー向けです。

## <a name="activate-a-role"></a>ロールのアクティブ化

Azure リソース ロールが必要な場合は、PIM の **[自分のロール]** ナビゲーション オプションを使用してアクティブ化を要求できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。 ダッシュボードに PIM タイルを追加する方法については、「[PIM の使用を開始する](pim-getting-started.md)」をご覧ください。

1. **[自分のロール]** をクリックして、適格な Azure AD ディレクトリ ロールおよび Azure リソース ロールの一覧を表示します。

    ![Azure AD ディレクトリ ロールおよび Azure リソース ロール - [自分のロール]](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. **[Azure リソース ロール]** の一覧で、アクティブにするロールを見つけます。

    ![Azure リソース ロール - [自分のロール] 一覧](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **[アクティブ化]** をクリックして、[アクティブ化] ウィンドウを開きます。

1. お使いのロールで多要素認証 (MFA) が必要な場合は、**[続行する前に ID を確認してください]** をクリックします。 認証は、セッションごとに 1 回だけ行う必要があります。

    ![ロール アクティブ化前の MFA の確認](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. **[ID を確認]** をクリックし、指示に従って追加のセキュリティ確認を提供します。

    ![追加のセキュリティ確認](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. より狭いスコープを指定する場合は、**[Scope]\(スコープ\)** をクリックして [リソース フィルター] ウィンドウを開きます。

    ベスト プラクティスは、必要なリソースへのアクセスのみを要求することです。 [リソース フィルター] ウィンドウでは、アクセスする必要があるリソース グループまたはリソースを指定できます。

    ![[アクティブ化] - [リソース フィルター]](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 必要に応じて、カスタムのアクティブ化開始時刻を指定します。 メンバーは、選択した時刻になるとアクティブになります。

1. **[理由]** ボックスに、アクティブ化要求の理由を入力します。

    ![完了した [アクティブ化] ウィンドウ](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. **[アクティブ化]** をクリックします。

    承認が必要ないロールは、アクティブ化され、アクティブなロールの一覧に追加されます。 すぐにロールを使用する場合は、次のセクションの手順に従います。

    アクティブ化に[承認が必要なロール](pim-resource-roles-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![要求保留の通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>アクティブ化後すぐにロールを使用する

PIM でロールをアクティブにすると、必要な管理ポータルにアクセスしたり、特定の管理ワークロード内で機能を実行したりできるようになるには、少なくとも 10 分かかります。 アクセス許可の更新を強制するには、次の手順で説明されている **[アプリケーションへのアクセス]** のページを使用します。

1. Azure AD Privileged Identity Management を開きます。

1. **[アプリケーションへのアクセス]** ページをクリックします。

    ![PIM のアプリケーションへのアクセス - スクリーンショット](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. **[すべてのリソース]** ページでポータルを再度開くには、**[Azure リソース]** リンクをクリックします。

    このリンクをクリックすると、現在のトークンが無効になり、更新されたアクセス許可を含む新しいトークンが Azure portal によって強制的に取得されます。

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[個人の要求]** をクリックして、Azure AD ディレクトリ ロールおよび Azure リソース ロール要求の一覧を表示します。

    ![Azure AD ディレクトリ ロールおよび Azure リソース ロール - [個人の要求]](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 右へスクロールして **[Request Status]\(要求の状態\)** 列を表示します。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[個人の要求]** をクリックします。

1. 取り消すロールの **[キャンセル]** リンクをクリックします。

    [キャンセル] をクリックすると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![保留中の要求をキャンセルする](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="permissions-not-granted-after-activating-a-role"></a>ロールをアクティブにした後、アクセス許可が付与されない

PIM でロールをアクティブにすると、必要な管理ポータルにアクセスしたり、特定の管理ワークロード内で機能を実行したりできるようになるには、少なくとも 10 分かかります。 アクセス許可の更新を強制するには、先の「[アクティブ化後すぐにロールを使用する](#use-a-role-immediately-after-activation)」で説明した **[アプリケーションへのアクセス]** のページを使用します。

追加のトラブルシューティング手順については、[昇格されたアクセス許可のトラブルシューティング](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx)に関するページを参照してください。

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>リソースのロックのためにロールをアクティブにできない

ロールをアクティブ化しようとするときに、Azure リソースがロックされているというメッセージが表示される場合は、ロール割り当てのスコープ内にあるリソースにリソース ロックが設定されていることが原因である可能性があります。 ロックを行うと、リソースが誤って削除されたり予期しない変更を加えられたりしないように保護できます。 また、リソースに対するロールの割り当てがアクティブ化期間の最後に PIM によって削除されるのを防ぐことができます。 リソースのロックが適用されていると、PIM が適切に機能できません。このため、ユーザーがそのリソースに対するロールをアクティブ化することは、PIM によって禁じられています。 この問題に対処する方法は、次の 2 つです。

- 「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/resource-group-lock-resources.md)」で説明されているように、ロックを削除します。
- ロックを保持する場合は、ロールの割り当てを永続的にするか、非常用アカウントを使用します。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [PIM で自分の Azure AD ディレクトリ ロールをアクティブにする](pim-how-to-activate-role.md)

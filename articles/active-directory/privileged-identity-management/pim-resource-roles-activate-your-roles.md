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
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666249"
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

    ロールで承認が必要ない場合は、これでアクティブ化され、アクティブなロールの一覧にロールが表示されます。 アクティブ化に[承認が必要なロール](pim-resource-roles-approval-workflow.md)の場合は、ブラウザーの右上隅に通知が表示され、承認待ちになっていることが示されます。

    ![要求保留の通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>要求の状態を表示する

保留中のアクティブ化要求の状態を表示することができます。

1. Azure AD Privileged Identity Management を開きます。

1. **[個人の要求]** をクリックして、Azure AD ディレクトリ ロールおよび Azure リソース ロール要求の一覧を表示します。

    ![Azure AD ディレクトリ ロールおよび Azure リソース ロール - [個人の要求]](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 右へスクロールして **[Request Status]\(要求の状態\)** 列を表示します。

## <a name="use-a-role-immediately-after-activation"></a>アクティブ化後すぐにロールを使用する

キャッシュのため、更新しなければ Azure portal でアクティブ化がすぐに行われることはありません。 ロールをアクティブ化した後の遅延の可能性を低くする必要がある場合は、ポータルの **[アプリケーションへのアクセス]** ページを使用できます。 このページからアプリケーションにアクセスすると、新しいロールの割り当てがすぐに確認されます。

1. Azure AD Privileged Identity Management を開きます。

1. **[アプリケーションへのアクセス]** ページをクリックします。

    ![PIM のアプリケーションへのアクセス - スクリーンショット](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. **[すべてのリソース]** ページでポータルを再度開くには、**[Azure リソース]** をクリックします。

    このリンクをクリックすると、強制的に更新されて、新しい Azure リソース ロールの割り当てがチェックされます。

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。

1. Azure AD Privileged Identity Management を開きます。

1. **[個人の要求]** をクリックします。

1. 取り消すロールの **[キャンセル]** リンクをクリックします。

    [キャンセル] をクリックすると、要求が取り消されます。 ロールを再びアクティブにするには、新しいアクティブ化要求を送信する必要があります。

   ![保留中の要求をキャンセルする](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [PIM で自分の Azure AD ディレクトリ ロールをアクティブにする](pim-how-to-activate-role.md)
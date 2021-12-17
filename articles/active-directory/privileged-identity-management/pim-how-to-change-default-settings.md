---
title: PIM で Azure AD ロールの設定を構成する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの設定を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/12/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc783125641ef71d1df974bc7d8cb01c488329a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484734"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールの設定を構成する

特権ロール管理者は、ロール候補の割り当てをアクティブ化しているユーザーの操作性を変更するなど、Azure Active Directory (Azure AD) 組織の Privileged Identity Management (PIM) をカスタマイズできます。 通知をトリガーする PIM イベントと、それらを受け取る管理者の詳細については、[Privileged Identity Management の電子メール通知](pim-email-notifications.md#notifications-for-azure-ad-roles)に関するページを参照してください

## <a name="open-role-settings"></a>ロールの設定を開く

次の手順を実行して、Azure AD ロールの設定を開きます。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)ロールのユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** &gt; **[Azure AD ロール]** &gt; **[ロールの設定]** を開きます。

    ![Azure AD ロールを一覧表示する [ロールの設定] ページ](./media/pim-how-to-change-default-settings/role-settings.png)

1. 設定を構成するロールを選択します。

    ![複数の割り当てとアクティブ化の設定を一覧表示する [ロールの設定の詳細] ページ](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. **[編集]** を選択して [ロールの設定] ページを開きます。

    ![割り当てとアクティブ化の設定を更新するためのオプションがある [Edit role settings]/(ロールの設定の編集/) ページ](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    各ロールの [ロールの設定] ウィンドウでは、複数の設定を構成できます。

## <a name="assignment-duration"></a>割り当て期間

ロールの設定を構成するときに、各割り当ての種類 (対象とアクティブ) に 2 つの割り当て期間オプションから選択できます。 これらのオプションは、Privileged Identity Management でユーザーがロールに割り当てられるときの既定の最大期間になります。

このような **資格のある** 割り当て期間のオプションからいずれかを選択することができます。

| 設定 | 説明 |
| --- | --- |
| 永続的な有資格割り当てを許可する | 全体管理者と特権ロール管理者は、永続的に資格のある割り当てを割り当てることができます。 |
| 資格のある割り当てが次の期間後に期限切れになる | 全体管理者と特権ロール管理者は、すべての資格のある割り当てに、開始日と終了日の指定を必須にすることができます。 |

また、このような **アクティブな** 割り当て期間のオプションからいずれかを選択することができます。

| 設定 | 説明 |
| --- | --- |
| 永続的なアクティブ割り当てを許可する | 全体管理者と特権ロール管理者は、永続的にアクティブな割り当てを割り当てることができます。 |
| アクティブな割り当てが次の期間後に期限切れになる | 全体管理者と特権ロール管理者は、すべてのアクティブな割り当てに、開始日と終了日の指定を必須にすることができます。 |

> [!NOTE]
> 全体管理者と特権ロール管理者は､終了日時が指定されているすべての割り当てを更新することができます｡ これに対し､ユーザーは[ロールの割り当てを延長または更新する](pim-resource-roles-renew-extend.md)セルフサービス要求を開始することができます｡

## <a name="require-multifactor-authentication"></a>多要素認証を要求する

Privileged Identity Management により、アクティブ化時およびアクティブな割り当て時に、Azure AD Multi-Factor Authentication が適用されます。

### <a name="on-activation"></a>アクティブ化時

ロールの資格を持つユーザーに対して、アクティブ化する前に Azure AD Multi-Factor Authentication を使用していることを証明するように要求することができます。 多要素認証により、ユーザーが本人であることが合理的かつ確実に保証されます。 このオプションを強制すると、ユーザー アカウントが侵害された可能性がある状況で、重要なリソースが保護されます。

ロールの割り当てをアクティブ化するために多要素認証を要求するには、 **[Edit role setting]\(ロールの設定の編集\)** の [アクティブ化] タブで **[On activation, require Azure MFA]\(アクティブ化時に Azure MFA を要求する\)** を選択します。

### <a name="on-active-assignment"></a>アクティブな割り当て時

このオプションを選択した場合、管理者は、資格のあるロールの割り当てでなく、アクティブなロールの割り当てを作成する前に、多要素認証を完了する必要があります。 ユーザーは割り当てられた時点からそのロールでアクティブになるため、各自がそのロールの割り当てを使用するときに、Privileged Identity Management で多要素認証を強制することができません。

アクティブなロール割り当てを作成するときに多要素認証を要求するには、 **[Edit role setting]\(ロールの設定の編集\)** の [割り当て] タブで **[アクティブな割り当てに Azure Multi-Factor Authentication を必要とする]** オプションを選択します。

詳細については、「[多要素認証と Privileged Identity Management](pim-how-to-require-mfa.md)」を参照してください。

## <a name="activation-maximum-duration"></a>アクティブ化の最大期間

**[アクティブ化の最大期間]** スライダーを使用して、ロールの割り当てのアクティブ化要求が、有効期限が切れるまでアクティブなままである最大時間 (時間単位) を設定します。 1 から 24 時間の範囲の値を指定できます。

## <a name="require-justification"></a>理由を必須にする

ユーザーがアクティブ化するときに、業務上の正当な理由を入力するように要求できます。 理由を必須にするには、 **[アクティブな割り当てに理由が必要]** チェックボックスまたは **[アクティブ化に理由が必要]** チェックボックスをオンにします。

## <a name="require-approval-to-activate"></a>アクティブ化の承認を必須にする

複数の承認者を設定した場合、承認は、承認者が 1 人でも承認または拒否すると直ちに完了します。 2 番目以降の承認者からの承認は強制できません。 ロールをアクティブ化するために承認を必須にする場合は、次の手順を実行します。

1. **[アクティブにするには承認が必要です]** チェックボックスをオンにします。

1. **[承認者の選択]** を選択します。

    ![承認者を選択するための [ユーザーまたはグループの選択] ペイン](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 少なくとも 1 人のユーザーを選択し、 **[選択]** をクリックします。 少なくとも 1 人の承認者を選択してください。 特定の承認者が選択されていない場合は、特権ロール管理者と全体管理者が既定の承認者になります。

1. **[更新]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management で Azure AD ロールに対するセキュリティ アラートを構成する](pim-how-to-configure-security-alerts.md)

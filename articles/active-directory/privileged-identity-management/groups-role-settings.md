---
title: PIM で特権アクセス グループの設定を構成する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で、ロールを割り当て可能なグループの設定を構成する方法について説明します。
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
ms.date: 11/12/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97798fdfc680d2cc644a47acc814a8fbe7e44654
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490044"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Privileged Identity Management で特権アクセス グループの設定 (プレビュー) を構成する

ロールの設定は、Privileged Identity Management (PIM) でグループの所有者およびグループ メンバーの特権アクセスの割り当てに適用される既定の設定です。 承認ワークフローを設定して、特権を昇格させる要求を承認または拒否できるユーザーを指定するには、次の手順に従います。

## <a name="open-role-settings"></a>ロールの設定を開く

Azure 特権アクセス グループ ロールの設定を開くには、次の手順に従います。

1. [グローバル管理者](../roles/permissions-reference.md#global-administrator)のロール、特権ロール管理者のロール、またはグループの所有者のロールであるユーザーを使用して、[Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[特権アクセス (プレビュー)]** を選択します。

1. 管理するグループを選択します。

    ![グループ名でフィルター処理された特権アクセス グループ](./media/groups-role-settings/group-select.png)

1. **[設定]** を選択します。

    ![選択したグループのグループ設定を一覧表示する [設定] ページ](./media/groups-role-settings/group-settings-select-role.png)

1. 設定を表示または変更する所有者またはメンバーのロールを選択します。 ロールの現在の設定は、 **[ロール設定の詳細]** ページで確認できます。

    ![複数の割り当てとアクティブ化の設定を一覧表示する [ロールの設定の詳細] ページ](./media/groups-role-settings/group-role-setting-details.png)

1. **[編集]** を選択して **[Edit role setting]\(ロールの設定の編集\)** ページを開きます。 **[アクティブ化]** タブでは、永続的な資格のあるアクティブな割り当てを許可するかどうかなど、ロールのアクティブ化の設定を変更できます。

    ![[アクティブ化] タブが開かれている [Edit role settings]\(ロールの設定の編集\) ページ](./media/groups-role-settings/role-settings-activation-tab.png)

1. **[割り当て]** タブを選択して、割り当ての設定タブを開きます。これらの設定により、このロールに対する Privileged Identity Management 割り当ての設定が制御されます。

    ![[ロールの設定] ページの [ロールの割り当て] タブ](./media/groups-role-settings/role-settings-assignment-tab.png)

1. **通知** タブまたはページの下部にある **Next:Activation\(次へ: アクティブ化** ボタンから、このロールの通知設定タブを表示します。 これらの設定は、このロールに関連するすべてのメール通知を制御します。

    ![[ロールの設定] ページの [ロールの通知] タブ](./media/groups-role-settings/role-settings-notification-tab.png)

1. いつでも **[更新]** ボタンを選択して、ロールの設定を更新します。

[ロールの設定] ページの **[通知]** タブで、Privileged Identity Management によって、通知を受信するユーザーとユーザーが受信する通知をきめ細かく制御できます。

- **メールをオフにする**<br>既定の受信者のチェック ボックスをオフにして、他の受信者をすべて削除すると、特定のメールをオフにできます。  
- **指定したメール アドレスへのメールを制限する**<br>既定の受信者のチェック ボックスをオフにすると、既定の受信者に送信されるメールをオフにできます。 その後で、受信者として他のメール アドレスを追加できます。 複数のメール アドレスを追加する場合は、セミコロン (;) で区切ります。
- **既定の受信者と、いっそう多くの受信者の両方にメールを送信する**<br>既定の受信者のチェック ボックスをオンにして、他の受信者のメール アドレスを追加すると、既定の受信者と他の受信者の両方にメールを送信できます。
- **重要なメールのみ**<br>メールの種類ごとにチェック ボックスをオンにすると、重要なメールのみを受信できます。 つまり、メールで即時のアクションが求められている場合にのみ、指定された受信者に、Privileged Identity Management によって引き続きメールが送信されます。 たとえば、ユーザーにロールの割り当てを拡張するように求めるメールはトリガーされませんが、管理者に拡張の要求を承認するように求めるメールはトリガーされます。

## <a name="assignment-duration"></a>割り当て期間

ロールの設定を構成するときに、各割り当ての種類 (対象とアクティブ) に 2 つの割り当て期間オプションから選択できます。 これらのオプションは、Privileged Identity Management でユーザーがロールに割り当てられるときの既定の最大期間になります。

このような **資格のある** 割り当て期間のオプションからいずれかを選択することができます。

| | 説明 |
| --- | --- |
| **永続的に資格のある割り当てを許可する** | リソース管理者は、永続的に資格のある割り当てを行うことができます。 |
| **次の後に、資格のある割り当ての有効期限が切れる** | リソース管理者は、すべての資格のある割り当てに、開始日と終了日の指定を必須にすることができます。 |

また、このような **アクティブな** 割り当て期間のオプションからいずれかを選択することができます。

| | 説明 |
| --- | --- |
| **永続するアクティブな割り当てを許可する** | リソース管理者は、永続的にアクティブな割り当てを行うことができます。 |
| **アクティブ割り当ては次の期間後に期限切れになる** | リソース管理者は、すべてのアクティブな割り当てに、開始日と終了日の指定を必須にすることができます。 |

> [!NOTE]
> リソース管理者は､終了日時が指定されている割り当てのどれでも更新することができます｡ これに対し､ユーザーは[ロールの割り当てを延長または更新する](pim-resource-roles-renew-extend.md)セルフサービス要求を開始することができます｡

## <a name="require-multifactor-authentication"></a>多要素認証を要求する

Privileged Identity Management では、2 つの異なるシナリオで、Azure AD Multi-factor Authentication の強制を選択できます。

### <a name="require-multifactor-authentication-on-active-assignment"></a>アクティブな割り当てに多要素認証を要求する

このオプションを選択すると、管理者は、アクティブなロールの割り当て (資格のあるロールの割り当てではありません) を作成する前に、多要素認証を完了する必要があります。 ユーザーがロール割り当てを使用するときに、Privileged Identity Management で多要素認証を強制することができません。ロールが割り当てられた時点で、ロール内で既にアクティブになっているためです。

アクティブなロールの割り当てを作成するときに多要素認証を要求するには、 **[Require Multi-Factor Authentication on active assignment]\(JP\)[アクティブな割り当てに多要素認証を要求する]** チェックボックスをオンにします。

### <a name="require-multifactor-authentication-on-activation"></a>アクティブ化に多要素認証を要求する

ロールの資格を持つユーザーに対して、アクティブ化する前に Azure AD Multi-Factor Authentication を使用していることを証明するように要求することができます。 多要素認証により、ユーザーが本人であることが合理的かつ確実に保証されます。 このオプションを強制すると、ユーザー アカウントが侵害された可能性がある状況で、重要なリソースが保護されます。

アクティブ化には多要素認証の実行を必須にするには、 **[アクティブ化には Multi-Factor Authentication が必要です]** チェックボックスをオンにします。

詳細については、「[多要素認証と Privileged Identity Management](pim-how-to-require-mfa.md)」を参照してください。

## <a name="activation-maximum-duration"></a>アクティブ化の最大期間

**[アクティブ化の最大期間]** スライダーを使用して、ロールの割り当てのアクティブ化要求が、有効期限が切れるまでアクティブなままである最大時間 (時間単位) を設定します。 1 から 24 時間の範囲の値を指定できます。

## <a name="require-justification"></a>理由を必須にする

ユーザーがアクティブ化するときに、業務上の正当な理由を入力するように要求できます。 理由を必須にするには、 **[アクティブな割り当てに理由が必要]** チェックボックスまたは **[アクティブ化に理由が必要]** チェックボックスをオンにします。

## <a name="require-approval-to-activate"></a>アクティブ化の承認を必須にする

ロールをアクティブ化するには承認を必須にする場合は、次の手順を実行します。

1. **[アクティブにするには承認が必要です]** チェックボックスをオンにします。

1. **[承認者の選択]** を選択して **[メンバーまたはグループの選択]** ページを開きます。

    ![承認者を選択するための [ユーザーまたはグループの選択] ペイン](./media/groups-role-settings/group-settings-select-approvers.png)

1. 少なくとも 1 つのユーザーまたはグループを選択し、 **[選択]** をクリックします。 任意の組み合わせのユーザーとグループを追加できます。 少なくとも 1 人の承認者を選択する必要があります。 既定の承認者はいません。

    選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、 **[更新]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

- [PIM で特権アクセス グループのメンバーシップまたは所有権を割り当てる](groups-assign-member-owner.md)

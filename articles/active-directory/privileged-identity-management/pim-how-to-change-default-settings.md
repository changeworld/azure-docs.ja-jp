---
title: PIM で Azure AD ロールの設定を構成する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの設定を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021812"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールの設定を構成する

特権ロール管理者は、ロール候補の割り当てをアクティブ化しているユーザーの操作性を変更するなど、Azure Active Directory (Azure AD) 組織の Privileged Identity Management (PIM) をカスタマイズできます。

## <a name="open-role-settings"></a>ロールの設定を開く

次の手順を実行して、Azure AD ロールの設定を開きます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[設定]** をクリックします。

    ![Azure AD ロール - 設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **[ロール]** をクリックします。

1. 設定を構成するロールをクリックします。

    ![Azure AD ロール - ロールの設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    各ロールの設定ページでは、複数の設定を構成できます。 これらの設定の影響を受けるのは、**永続的**な割り当てのユーザーではなく、**有資格**の割り当てのユーザーのみです。

## <a name="activations"></a>アクティブ化

**[アクティブ化]** スライダーを使用して、ロールの有効期限が切れるまでの最大時間 (時間単位) を設定します。 1 ～ 72 時間の範囲の値を指定できます。

## <a name="notifications"></a>通知

**[通知]** スイッチを使用して、ロールがアクティブになったときに管理者がメール通知を受け取るかどうかを指定します。 この通知は、不正なアクティブ化の検出に役立ちます。

**[有効]** に設定すると、通知が送信されます。

- 特権ロール管理者
- セキュリティ管理者
- 全体管理者

詳細については、[Privileged Identity Management でのメール通知](pim-email-notifications.md)に関するページを参照してください。

## <a name="incidentrequest-ticket"></a>インシデント/要求チケット

**[インシデント/要求チケット]** スイッチを使用して、管理者候補に対して、ロールのアクティブ化時にチケット番号の入力を要求します。 このプラクティスでは、ロール アクセスの監査をより効果的に行うことができます。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**[多要素認証]** スイッチを使用して、ユーザーに対して、ロールのアクティブ化で MFA を使用した ID 確認を必須とするかどうかを指定します。 ユーザーが自分の ID を確認する必要があるのは、ロールのアクティブ化のたびではなく、セッションごとに 1 回だけです。 MFA を有効化する場合は次の 2 点に留意してください。

- メール アドレス (一般には @outlook.com ですが、例外もあります) の Microsoft アカウントを持つユーザーは、Azure Multi-Factor Authentication に登録できません。 Microsoft アカウントを持つユーザーにロールを割り当てるには、ユーザーを永続的な管理者にするか、そのロールの Multi-Factor Authentication を無効化する必要があります。
- Azure AD および Office 365 の高い特権ロールでは、Azure Multi-Factor Authentication は無効化できません。 この安全機能は、次のロールの保護に役立ちます。  
  
  - Azure Information Protection 管理者
  - 課金管理者
  - クラウド アプリケーション管理者
  - コンプライアンス管理者
  - 条件付きアクセス管理者
  - Dynamics 365 管理者
  - カスタマー ロックボックスのアクセス承認者
  - ディレクトリ ライター
  - Exchange 管理者
  - 全体管理者
  - Intune 管理者
  - Power BI 管理者
  - 特権ロール管理者
  - セキュリティ管理者
  - SharePoint 管理者
  - Skype for Business 管理者
  - ユーザー管理者

詳細については、[Multi-Factor Authentication と Privileged Identity Management](pim-how-to-require-mfa.md) に関するページを参照してください。

## <a name="require-approval"></a>承認を要求する

ロールのアクティブ化に必要な承認を委任する場合は、次の手順に従います。

1. **[承認を要求する]** スイッチを **[有効]** に設定します。 ウィンドウが展開され、承認者を選択できるオプションが表示されます。

    ![Azure AD ロール - 設定 - 承認を要求する](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    承認者を指定しない場合、特権ロール管理者は既定の承認者になり、このロールに対するすべてのアクティブ化要求を承認する必要があります。

1. 承認者を指定するには、 **[承認者の選択]** をクリックします。

    ![Azure AD ロール - 設定 - 承認を要求する](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 特権ロール管理者に加えて、1 つまたは複数の承認者を選択し、 **[選択]** をクリックします。 ユーザーまたはグループを選択することができます。 承認者を 2 人以上にすることをお勧めします。 自分自身を承認者として追加した場合でも、ロールのアクティブ化を自己承認することはできません。 選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、 **[保存]** を選択して変更を保存します。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>次の手順

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management で Azure AD ロールに対するセキュリティ アラートを構成する](pim-how-to-configure-security-alerts.md)

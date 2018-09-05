---
title: PIM で Azure AD ディレクトリ ロールの設定を構成する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ディレクトリ ロールの設定を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 20a704a0d5b61134a61b5cbf02a1c71dbc7039e1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189339"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>PIM で Azure AD ディレクトリ ロールの設定を構成する

特権ロール管理者は、ロール候補の割り当てをアクティブ化しているユーザーの操作性を変更するなど、組織の Azure AD Privileged Identity Management (PIM) をカスタマイズできます。

## <a name="open-role-settings"></a>ロールの設定を開く

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ディレクトリ ロール]** をクリックします。

1. **[設定]** をクリックします。

    ![Azure AD ディレクトリ ロール - 設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **[ロール]** をクリックします。

1. 設定を構成するロールをクリックします。

    ![Azure AD ディレクトリ ロール - ロールの設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    各ロールの設定ページでは、複数の設定を構成できます。 これらの設定の影響を受けるのは、**永続的**な割り当てのユーザーではなく、**有資格**の割り当てのユーザーのみです。

## <a name="activations"></a>アクティブ化

**[アクティブ化]** スライダーは、ロールの有効期限が切れるまでの最大時間 (時間単位) です。 1 ～ 72 時間の範囲の値を指定できます。

## <a name="notifications"></a>通知

**[通知]** スイッチでは、管理者に対してロールのアクティブ化を確認するメールを送信するかどうかを選択できます。 これは、不正なアクティブ化の検出に役立ちます。

## <a name="incidentrequest-ticket"></a>インシデント/要求チケット

**[インシデント/要求チケット]** スイッチでは、管理者候補に対して、ロールのアクティブ化時にチケット番号の入力を必須とするかどうかを選択できます。 これは、ロール アクセスの監査を行う場合に役立ちます。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**[多要素認証]** スイッチでは、ユーザーに対して、ロールのアクティブ化で MFA を使用した ID 確認を必須とするかどうかを選択できます。 ユーザーがこの確認を行う必要があるのはセッションごとに 1 回だけです。ロールのアクティブ化のたびに確認を行う必要はありません。 MFA を有効化する場合は次の 2 点に留意してください。

* メール アドレスの Microsoft アカウント (一般には @outlook.com ですが、例外もあります) を持つユーザーは、Azure MFA に登録できません。 Microsoft アカウントを持つユーザーにロールを割り当てるには、ユーザーを永続的な管理者にするか、そのロールの MFA を無効化する必要があります。
* Azure AD および Office365 の高い特権ロールでは、MFA は無効化できません。 これは、こうしたロールを慎重に保護する必要があるために設けられている安全機能です。  
  
  * アプリケーション管理者
  * アプリケーション プロキシ サーバー管理者
  * 課金管理者  
  * コンプライアンス管理者  
  * CRM サービス管理者
  * カスタマー ロックボックスのアクセス承認者
  * ディレクトリ ライター  
  * Exchange 管理者  
  * 全体管理者
  * Intune サービス管理者
  * メールボックス管理者  
  * パートナー レベル 1 のサポート  
  * パートナー レベル 2 のサポート  
  * 特権ロール管理者
  * セキュリティ管理者  
  * SharePoint 管理者  
  * Skype for Business 管理者  
  * ユーザー アカウント管理者  

PIM での MFA の使用に関して詳しくは、「[PIM で Azure AD ディレクトリ ロールに対して多要素認証を要求する](pim-how-to-require-mfa.md)」をご覧ください。

## <a name="require-approval"></a>承認を要求する

**[承認を要求する]** スイッチでは、このロールをアクティブ化するために承認が必要かどうかを選択できます。

1. このスイッチを **[有効]** に設定すると、ウィンドウが展開して承認者を選択するオプションが表示されます。

    ![Azure AD ディレクトリ ロール - 設定 - 承認を要求する](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    承認者を指定**しない**場合は、特権ロール管理者が既定の承認者になります。 特権ロール管理者が、このロールの**すべて**のアクティブ化要求を承認する必要があります。

1. 承認者を指定するには、**[承認者の選択]** をクリックします。

    ![Azure AD ディレクトリ ロール - 設定 - 承認を要求する](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 1 人以上の承認者を選択し、**[選択]** をクリックします。 ユーザーまたはグループを選択することができます。 承認者を 2 人以上にすることをお勧めします。 自己承認は許可されません。

    選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、**[保存]** をクリックして変更を保存します。


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ディレクトリ ロールに対して多要素認証を要求する](pim-how-to-require-mfa.md)
- [PIM で Azure AD ディレクトリ ロールに対するセキュリティの警告を構成する](pim-how-to-configure-security-alerts.md)

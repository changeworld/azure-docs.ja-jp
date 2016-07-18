<properties
   pageTitle="ロールのアクティブ化の設定を管理する方法 |Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management 拡張機能で特権 ID の既定の設定を変更する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management のロールのアクティブ化設定を管理する方法

特権ロール管理者は、ロール候補の割り当てをアクティブ化しているユーザーの操作性を変更するなど、組織の Azure AD Privileged Identity Management (PIM) をカスタマイズできます。

## ロールのアクティブ化の設定を管理する

1. [Azure ポータル](https://portal.azure.com)に移動し、ダッシュボードから **Azure AD Privileged Identity Management** アプリを選択します。
2. 管理する役割をロール テーブルから選択します。
3. **[設定]** をクリックします。

各ロールの [設定] ページでは、多くの設定を構成できます。これらの設定の影響を受けるのは、永続的な管理者ではなく、管理者候補であるユーザーのみです。

**アクティブ化**: ロールの有効期限が切れるまでの時間 (時間単位) です。1 ～ 72 時間までを指定できます。

**通知**: 管理者に対してロールのアクティブ化を確認する電子メールを送信するかどうかを選択できます。これは、不正なアクティブ化の検出に役立ちます。

**インシデント/要求チケット**: 管理者候補に対して、ロールをアクティブ化時にチケット番号の入力を必須とするかどうかを選択できます。これは、ロール アクセスの監査を行う場合に役立ちます。

**多要素認証**: ユーザーに対して、ロールのアクティブ化で MFA を使用した ID 確認を必須とするかどうかを選択できます。ユーザーがこの確認を行う必要があるのはセッションごとに 1 回だけです。ロールのアクティブ化のたびに確認を行う必要はありません。MFA を有効化する場合は次の 2 点に留意してください。

- 電子メール アドレスの Microsoft アカウント (一般には @outlook.com ですが、例外もあります) を持つユーザーは、Azure MFA に登録できません。Microsoft アカウントを持つユーザーにロールを割り当てるには、ユーザーを永続的な管理者にするか、そのロールの MFA を無効化する必要があります。

- Azure AD および Office365 の高い特権ロールでは、MFA は無効化できません。これは、こうしたロールを慎重に保護する必要があるために設けられている安全機能です。

    - グローバル管理者
    - 特権ロール管理者
    - ユーザー アカウント管理者
    - ディレクトリ ライター
    - パートナー レベル 1 のサポート
    - パートナー レベル 2 のサポート
    - 課金管理者
    - セキュリティ管理者
    - Exchange 管理者
    - メールボックス管理者
    - Skype for Business 管理者
    - SharePoint 管理者
    - コンプライアンス管理者

PIM での MFA の使用に関する詳細は、「[MFA を要求する方法](active-directory-privileged-identity-management-how-to-require-mfa.md)」を参照してください。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0706_2016-->
<properties
   pageTitle="ロールの既定のアクティブ化設定を変更または表示する方法 | Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management 拡張機能で特権 ID の既定の設定を変更する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: ロールの既定のアクティブ化設定を変更または表示する方法

## ロールの既定のアクティブ化の変更と表示
1. ダッシュボードのロールのテーブルで、構成するロールをクリックします。
2. **[設定]** をクリックします。
3. アクティブ化の既定の期間 (時間単位) を、スライダーを調整するか、テキスト フィールドに時間数を入力して、設定します。
4. 管理者へのアクティブ化に関する通知の送信を、**[有効]** または **[無効]** にします。
5. 管理者によるアクティブ化要求へのチケット情報の入力を **[有効]** または **[無効]** にします。
6. アクティブ化要求で多要素認証が必要かどうかを、**[有効]** または **[無効]** にします。

    次のような、Azure AD および Office365 の高い特権ロールに対しては、MFA を無効にすることはできません。

    - グローバル管理者
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

7. 一時的なグローバル管理者の許可を、**[有効]** または **[無効]** にします。
8. **[保存]** をクリックします。

PIM での MFA の使用に関する詳細は、「[MFA を要求する方法](active-directory-privileged-identity-management-how-to-require-mfa.md)」を参照してください。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->
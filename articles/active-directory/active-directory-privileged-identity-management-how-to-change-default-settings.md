<properties
   pageTitle="ロールのアクティブ化の設定を管理する方法 |Microsoft Azure"
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management のロールのアクティブ化設定を管理する方法

セキュリティ管理者は、一時的なロールの割り当てをアクティブ化しているユーザーの操作性を変更するなど、組織の Azure AD Privileged Identity Management (PIM) をカスタマイズできます。

## ロールのアクティブ化の設定を管理する

1. [Azure ポータル](https://portal.azure.com)に移動し、ダッシュボードから **Azure AD Privileged Identity Management** アプリを選択します。
2. 管理する役割をロール テーブルから選択します。
3. **[設定]** をクリックします。
4. アクティブ化の既定の期間 (時間単位) を、スライダーを調整するか、テキスト フィールドに時間数を入力して、設定します。最大値は、72 時間です。
5. アクティブ化に関する通知を管理者に送信するオプションを **[有効]** または **[無効]** にします (通知を有効にすると、承認されていない管理者アクティビティを検出するのに役立ちます)。
6. 管理者がアクティブ化の要求にチケット情報を入力できるようにするオプションを **[有効]** にします (この情報は、後でロールのアクセスを監査するときに役立ちます)。
7. アクティブ化の要求に対して多要素認証を要求するオプションを **[有効]** または **[無効]** にします。
8. **[保存]** をクリックします。

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

PIM での MFA の使用に関する詳細は、「[MFA を要求する方法](active-directory-privileged-identity-management-how-to-require-mfa.md)」を参照してください。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->
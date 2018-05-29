---
title: Azure Active Directory B2B コラボレーションの API とカスタマイズ | Microsoft Docs
description: Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f1bd93ac2ef6aa75e07eeec3e3cb2222b6febc1c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932372"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B コラボレーションの API とカスタマイズ

多くのお客様から、組織に最適な方法で招待の処理をカスタマイズできるようにしてほしいという要望がありました。 API を使用すると、そのようにすることができます。 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>招待 API の機能
API には次の機能が用意されています。

1. "*任意の*" 電子メール アドレスで外部ユーザーを招待できます。

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. 招待に応じたユーザーの移動先をカスタマイズできます。

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Microsoft を通じて標準的な招待メールを送信することができます。

    ```
    "sendInvitationMessage": true
    ```

  受信者へのカスタマイズ可能なメッセージを付けることができます。

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. また、このコラボレーターの招待について知らせておきたい人を cc することができます。

5. または、Azure AD を通じて通知を送信しないことを選択して、招待とオンボード ワークフローを完全にカスタマイズすることができます。

    ```
    "sendInvitationMessage": false
    ```

  この場合は、招待に応じるための URL を API から受け取り、それを電子メール テンプレート、IM、またはその他の任意の配布方法に埋め込むことができます。

6. 最後に、管理者である場合は、ユーザーをメンバーとして招待することができます。

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>承認モデル
API は、以下の承認モードで実行できます。

### <a name="app--user-mode"></a>アプリ + ユーザー モード
このモードでは、API を使用するユーザーは、B2B 招待を作成できるアクセス許可を付与されている必要があります。

### <a name="app-only-mode"></a>アプリのみモード
アプリのみのコンテキストで招待を成功させるには、アプリに User.Invite.All スコープが必要です。

詳しくは、https://graph.microsoft.io/docs/authorization/permission_scopes をご覧ください


## <a name="powershell"></a>PowerShell
PowerShell を使用して、簡単に外部ユーザーを組織に追加および招待できるようになりました。 次のコマンドレットを使用して招待を作成します。

```
New-AzureADMSInvitation
```

以下のオプションを使用できます。

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

招待 API のリファレンスは、[https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) で確認することもできます

## <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
- [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
- [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)


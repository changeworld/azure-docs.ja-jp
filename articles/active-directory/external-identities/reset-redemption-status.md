---
title: ゲスト ユーザーの引き換え状態をリセットする - Azure AD
description: Azure AD External Identities で Azure Active Directory B2B ゲスト ユーザーの招待の引き換え状態をリセットする方法について学習します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e19c4e6e13f4abcb8143f789dc1a7aa8de47e82
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129998962"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>ゲスト ユーザーの引き換え状態をリセットする (プレビュー)

ゲスト ユーザーが B2B コラボレーションの招待を引き換えた後、次のような場合に、サインイン情報の更新が必要になることがあります。

- ユーザーが別の電子メールおよび ID プロバイダーを使用してサインインすることを望んでいる
- ホーム テナント内のユーザーのアカウントが削除され、再作成された
- ユーザーは別の会社に転職したが、リソースへのアクセス権は引き続き同じものが必要である
- ユーザーの責任が別のユーザーに移った

以前はこれらのシナリオを管理するために、ディレクトリからゲスト ユーザーのアカウントを手動で削除し、そのユーザーを再度招待する必要がありました。 現在は PowerShell または Microsoft Graph 招待 API を使用して、ユーザーの引き換え状態をリセットし、ユーザーのオブジェクト ID、グループ メンバーシップ、アプリの割り当てを維持したままで、そのユーザーを再度招待できます。 ユーザーが新しい招待を引き換えしても、ユーザーの UPN は変更されませんが、ユーザーのサインイン名は新しい電子メールに変更されます。 その後、ユーザーは新しい電子メール、またはユーザー オブジェクトの `otherMails` プロパティに追加した電子メールを使用してサインインできます。

## <a name="use-the-azure-portal-to-reset-redemption-status"></a>引き換え状態のリセットに Azure portal を使用する

1. ディレクトリのグローバル管理者またはユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. **Azure Active Directory** を検索して選択します。
1. **[ユーザー]** を選択します。
1. 一覧でユーザーの名前を選択して、ユーザーのプロファイルを開きます。
1. ユーザーが別の電子メールを使用してサインインすることを望んでいる場合:
   - ページ上部の **編集** アイコンを選択します。
   - **[連絡先情報]** セクションの **[電子メール]** の下に、新しい電子メールを入力します。
   - **[連絡用メール アドレス]** の横の **[編集]** を選択します。 リストの連絡用メール アドレスを新しいメール アドレスに変更し、 **[更新]** を選択します。
   - ページ上部の **保存** アイコンを選択します。
1. **[ID]** セクションの **[招待が受け入れられました]** の下の **[(管理)]** リンクを選択します。
1. **[Redemption status]** \(引き換え状態\) の下の **[Reset invitation status? (Preview)]** \(招待の状態をリセットする (プレビュー)\) の横で **[はい]** を選択します。
1. **[はい]** を選択して確定します。


## <a name="use-powershell-or-microsoft-graph-api-to-reset-redemption-status"></a>引き換え状態のリセットに PowerShell または Microsoft Graph API を使用する

### <a name="reset-the-email-address-used-for-sign-in"></a>サインインに使用する電子メール アドレスをリセットする

ユーザーが別の電子メールを使用してサインインすることを望んでいる場合:

1. 新しい電子メール アドレスが、user オブジェクトの `mail` プロパティまたは `otherMails` プロパティに追加されていることを確認します。 
1.  `InvitedUserEmailAddress` プロパティの電子メール アドレスを新しい電子メール アドレスに置き換えます。
1. 次のいずれかの方法を使用して、ユーザーの引き換え状態をリセットします。

> [!NOTE]
>パブリック プレビュー期間中は、次の 2 つの推奨事項があります。
>- ユーザーのメール アドレスを新しいアドレスにリセットする場合は、`mail` プロパティを設定することをお勧めします。 このようにすると、ユーザーは招待の引き換えリンクを使用するだけでなく、ディレクトリにサインインして招待を引き換えることができます。
>- B2B ゲスト ユーザーの状態をリセットする場合は、必ずユーザー コンテキストで実行してください。 現在、アプリのみの呼び出しはサポートされていません。
>
### <a name="use-powershell-to-reset-redemption-status"></a>PowerShell を使用して引き換え状態をリセットする

最新の AzureADPreview PowerShell モジュールをインストールし、`InvitedUserEmailAddress` を新しい電子メール アドレスに設定し、`ResetRedemption` を `true` に設定して新しい招待を作成します。

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

### <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Microsoft Graph API を使用して引き換え状態をリセットする

[Microsoft Graph の招待 API](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true) を使用して、`resetRedemption` プロパティを `true` に設定し、新しい電子メール アドレスを `invitedUserEmailAddress` プロパティに指定します。

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>次のステップ

- [PowerShell を使用して Azure Active Directory B2B コラボレーション ユーザーを追加する](customize-invitation-api.md#powershell)
- [Azure AD B2B ゲスト ユーザーのプロパティ](user-properties.md)

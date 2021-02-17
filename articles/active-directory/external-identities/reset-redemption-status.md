---
title: ゲスト ユーザーの引き換え状態をリセットする - Azure AD
description: Azure AD External Identities で Azure Active Directory B2B ゲスト ユーザーの招待の引き換え状態をリセットする方法について学習します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc51a8af85b85db28265d1fca420a5b9b368b078
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576857"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>ゲスト ユーザーの引き換え状態をリセットする

ゲスト ユーザーが B2B コラボレーションの招待を引き換えた後、次のような場合に、サインイン情報の更新が必要になることがあります。

- ユーザーが別の電子メールおよび ID プロバイダーを使用してサインインすることを望んでいる
- ホーム テナント内のユーザーのアカウントが削除され、再作成された
- ユーザーは別の会社に転職したが、リソースへのアクセス権は引き続き同じものが必要である
- ユーザーの責任が別のユーザーに移った

以前はこれらのシナリオを管理するために、ディレクトリからゲスト ユーザーのアカウントを手動で削除し、そのユーザーを再度招待する必要がありました。 現在は PowerShell または Microsoft Graph 招待 API を使用して、ユーザーの引き換え状態をリセットし、ユーザーのオブジェクト ID、グループ メンバーシップ、アプリの割り当てを維持したままで、そのユーザーを再度招待できます。 ユーザーが新しい招待を引き換えると、新しい電子メール アドレスがそのユーザーの UPN になります。 その後、ユーザーは新しい電子メール、またはユーザー オブジェクトの `otherMails` プロパティに追加された電子メールを使用してサインインできます。

## <a name="use-powershell-to-reset-redemption-status"></a>PowerShell を使用して引き換え状態をリセットする

最新の AzureADPreview PowerShell モジュールをインストールし、`InvitedUserEMailAddress` を新しい電子メール アドレスに設定し、`ResetRedemption` を `true` に設定して新しい招待を作成します。

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Microsoft Graph API を使用して引き換え状態をリセットする

[Microsoft Graph の招待 API](/graph/api/resources/invitation?view=graph-rest-1.0) を使用して、`resetRedemption` プロパティを `true` に設定し、新しい電子メール アドレスを `invitedUserEmailAddress` プロパティに指定します。

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

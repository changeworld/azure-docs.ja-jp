---
title: "Azure AD SSPR データ要件 |Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワード リセットのデータ要件とそれらを満たす方法"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 6704db9a722de78d9460703330cf3fdbc238b7d2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>エンド ユーザーによる登録を必要としないパスワード リセットをデプロイする

Azure Active Directory (Azure AD) のセルフサービスによるパスワードのリセット (SSPR) をデプロイするには、認証データが存在する必要があります。 ユーザーに自分で認証データを入力させている組織もあります。 しかし、多くの組織では Active Directory に既に存在するデータと同期する方法が採用されています。 次の条件が満たされていれば、ユーザーが介入しなくても、同期されたデータは Azure AD と SSPR で利用できるようになります。
   * オンプレミスのディレクトリ内のデータが正しい形式になっていること。
   * [簡単設定を使って Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) が構成されていること。

正常に動作させるには、電話番号の形式が "*+<国コード> <電話番号>*" (例: +1 4255551234) になっている必要があります。

> [!NOTE]
> パスワードのリセットは内線番号をサポートしていません。 +1 4255551234X12345 の形式であっても、電話がかけられる前に内線番号は削除されます。

## <a name="fields-populated"></a>取り込まれるフィールド

Azure AD Connect で既定の設定を使用する場合、次のマッピングが行われます。

| オンプレミスの Active Directory | Azure AD | Azure AD 認証の連絡先情報 |
| --- | --- | --- |
| telephoneNumber | 会社電話 | Alternate phone |
| mobile | 携帯電話 | 電話 |


## <a name="security-questions-and-answers"></a>セキュリティの質問と回答

セキュリティの質問と回答は、Azure AD テナントに安全に格納されており、ユーザーは [SSPR 登録ポータル](https://aka.ms/ssprsetup)を介してのみアクセスできます。 管理者は、別のユーザーの質問と回答の内容を表示したり、変更したりすることはできません。

### <a name="what-happens-when-a-user-registers"></a>ユーザーの登録時に発生すること

ユーザーが登録するとき、登録ページには次のフィールドが設定されます。

* **認証用電話**
* **認証用電子メール**
* **セキュリティの質問と回答**

**[携帯電話]** または **[連絡用メール アドレス]** に値が指定されている場合、ユーザーはそれらの値を使用してすぐにパスワードをリセットすることができます。これはユーザーがサービスに登録していない場合でも実行できます。 さらに、これらの値は、ユーザーが初めて登録するときに表示され、ユーザーは必要に応じてそれらを変更することができます。 ユーザーが正常に登録された後、これらの値は、それぞれ **[認証用電話]** フィールドと **[認証用メール]** フィールドの固定値になります。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>PowerShell を使用した認証データの設定と読み取り

PowerShell を使用して、次のフィールドを設定することができます。

* **連絡用メール アドレス**
* **携帯電話**
* **会社電話** - オンプレミス ディレクトリと同期していない場合にのみ設定できます

### <a name="use-powershell-version-1"></a>PowerShell バージョン 1 を使う

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストールする](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)必要があります。 インストールした後、次の手順を使って各フィールドを構成できます。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>PowerShell バージョン 1 を使って認証データを設定する

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>PowerShell バージョン 1 を使って認証データを読み取る

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>[認証用電話] と [認証用電子メール] オプションを読み取る

PowerShell バージョン 1 で **[認証用電話]** と **[認証用電子メール]** を読み取るには、次のコマンドを使います。

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell バージョン 2 を使う

始めるには、[Azure AD バージョン 2 PowerShell モジュールをダウンロードしてインストールする](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)必要があります。 インストールした後、次の手順を使って各フィールドを構成できます。

Install-Module をサポートする PowerShell の最新バージョンから簡単にインストールするには、次のコマンドを実行します  (最初の行では、モジュールが既にインストールされているかどうかを確認しています)。

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>PowerShell バージョン 2 を使って認証データを設定する

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>PowerShell バージョン 2 を使って認証データを読み取る

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>次のステップ

* [SSPR のロールアウトを適切に完了する方法。](active-directory-passwords-best-practices.md)
* [パスワードのリセットまたは変更](active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](active-directory-passwords-licensing.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [パスワード ライトバックと、それが必要な理由。](active-directory-passwords-writeback.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

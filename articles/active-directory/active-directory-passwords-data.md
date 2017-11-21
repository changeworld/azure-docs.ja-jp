---
title: "Azure AD SSPR データ要件 |Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワード リセットのデータ要件とそれらを満たす方法"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
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
ms.openlocfilehash: 7936f47007285e3f7fa1d3220efa022a6e3881ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>エンド ユーザーによる登録を必要としないパスワード リセットをデプロイする

セルフ サービスのパスワード リセット (SSPR) をデプロイするには、認証データが存在する必要があります。 一部の組織では、ユーザーは認証データを自分で入力できますが、多くの組織では Active Directory の既存のデータと同期する方法を採用しています。 オンプレミス ディレクトリのデータが正しくフォーマットされている場合、[簡易設定を使用して Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) を構成すると、ユーザーによる操作なしでそのデータを Azure AD および SSPR で使用できるようになります。

正常に動作させるには、電話番号の形式が +CountryCode PhoneNumber (例: +1 4255551234) である必要があります。

> [!NOTE]
> パスワードのリセットは内線番号をサポートしていません。 +1 4255551234X12345 の形式であっても、電話がかけられる前に内線番号は削除されます。

## <a name="fields-populated"></a>取り込まれるフィールド

Azure AD Connect で既定の設定を使用する場合、次のマッピングが行われます。

| オンプレミスの AD | Azure AD | Azure AD 認証の連絡先情報 |
| --- | --- | --- |
| telephoneNumber | 会社電話 | Alternate phone |
| mobile | 携帯電話 | 電話 |


## <a name="security-questions-and-answers"></a>セキュリティの質問と回答

セキュリティの質問と回答は、Azure AD テナントに安全に格納されており、ユーザーは [SSPR 登録ポータル](https://aka.ms/ssprsetup)を介してのみアクセスできます。 管理者は、別のユーザーの質問と回答の内容を表示したり、変更したりすることはできません。

### <a name="what-happens-when-a-user-registers"></a>ユーザーの登録時に発生すること

ユーザーが登録するとき、登録ページには次のフィールドが設定されます。

* 認証用電話
* 認証用メール
* セキュリティの質問と回答

**[携帯電話]** または **[連絡用メール アドレス]** に値が指定されている場合、ユーザーはそれらの値を使用してすぐにパスワードをリセットすることができます。これはユーザーがサービスに登録していない場合でも実行できます。 さらに、これらの値は、ユーザーが初めて登録するときに表示され、ユーザーは必要に応じてそれらを変更することができます。 ユーザーが正常に登録された後、これらの値は、それぞれ **[認証用電話]** フィールドと **[認証用メール]** フィールドの固定値になります。

## <a name="set-and-read-authentication-data-using-powershell"></a>PowerShell を使用した認証データの設定と読み取り

PowerShell を使用して、次のフィールドを設定することができます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話 - オンプレミス ディレクトリと同期していない場合にのみ設定できます

### <a name="using-powershell-v1"></a>PowerShell V1 の使用

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストールする](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)必要があります。 インストールした後、次の手順に従って各フィールドを構成できます。

#### <a name="set-authentication-data-with-powershell-v1"></a>PowerShell V1 を使用して認証データを設定する

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>PowerShellPowerShell V1 を使用して認証データを読み取る

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>認証用電話と認証用電子メールは、Powershell V1 で次のコマンドを使用した場合にのみ読み取り可能

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>PowerShell V2 の使用

操作を開始するには、 [Azure AD V2 PowerShell モジュールをダウンロードしてインストールする](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)必要があります。 インストールした後、次の手順に従って各フィールドを構成できます。

Install-Module をサポートする最新バージョンの PowerShell を簡単にインストールには、次のコマンドを実行します (最初の行は、既にインストールされているかどうかをチェックするだけです)。

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>PowerShell V2 を使用して認証データを設定する

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>PowerShell V2 を使用して認証データを読み取る

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>次のステップ

* [SSPR のロールアウトを適切に完了する方法。](active-directory-passwords-best-practices.md)
* [パスワードのリセットと変更。](active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問。](active-directory-passwords-licensing.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [パスワード ライトバックと、それが必要な理由。](active-directory-passwords-writeback.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

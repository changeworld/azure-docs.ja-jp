---
title: "Azure Active Directory B2B コラボレーション ユーザーの多要素認証 | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションでは、会社のアプリケーションへの選択的なアクセスのために、多要素認証 (MFA) をサポートしています"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/07/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 049f8ecec38a518276c6deea6a285a59d1401de6
ms.openlocfilehash: 318ec4a2b807aaed5c61272016d0957d6e6f5004


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Azure Active Directory B2B コラボレーション ユーザーの多要素認証

この Azure AD B2B コラボレーションのパブリック プレビューの更新では、組織が B2B コラボレーション ユーザーにも多要素認証 (MFA) ポリシーを強制できる機能を導入します。 この更新では、リソース テナントに MFA が常に強制されます。

そのため、次のようになります。
1. 会社 A の管理者またはインフォメーション ワーカーが、会社 B のユーザーを会社 A のアプリケーション Foo に招待します。
2. 会社 A のアプリケーション *Foo* は、アクセス時に MFA を要求するように構成されています。
3. 会社 B のユーザーが会社 A テナントのアプリ Foo にアクセスしようとすると、会社 A の MFA ポリシーで要求されているように MFA チャレンジを完了するように求められます。
4. ユーザーは、会社 A で MFA をセットアップし、MFA オプションを選択することができます。
5. これは、どの ID でも機能します (会社 B のユーザーがソーシャル ID を使用して認証を行う場合は、たとえば Azure AD または MSA)。
6. 会社 A は、MFA をサポートしている十分な数の Premium Azure AD SKU を所持している必要があります。 会社 B のユーザーは、A 社のこのライセンスを使用します。
7. まとめると、パートナー組織自体ではなく招待側のテナントが、"*常に*" パートナー組織の B2B コラボレーション ユーザーの MFA に対する責任を持ちます (パートナー組織が MFA 機能を持っている場合でも)。 今後のリリースでは、招待側の組織が自社の MFA を使用するのではなく、特定のパートナー組織の MFA を信頼できるようになっていく予定です。

## <a name="setting-up-mfa-for-b2b-users"></a>B2B ユーザーの MFA のセットアップ
B2B コラボレーション ユーザー用の MFA のセットアップがいかに簡単かについては、[このビデオ](https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup)を参照してください。

## <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B ユーザーがオファーに応じるための MFA エクスペリエンス
招待に応じるためのエクスペリエンスについては、[このビデオ](https://channel9.msdn.com/Blogs/Azure/MFA-redemption)のアニメーションを参照してください。

## <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの MFA リセット
現時点では、管理者は次の PowerShell コマンドレットを使用することによってのみ、B2B コラボレーション ユーザーにもう一度証明を要求できます。 そのため、B2B コラボレーション ユーザーの証明方法をリセットする場合は、以下の PowerShell コマンドレットを使用する必要があります。

> [!NOTE]
> 新しいコマンドレットを使用するには、Azure AD PowerShell V2 モジュールをインストールする必要があります。このモジュールは、https://www.powershellgallery.com/packages/AzureADPreview で入手できます。

1. Azure への接続

  ```
  Connect-MsolService and login
  ```
2. すべてのユーザーと証明方法を取得します。

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  たとえば次のようになります。

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 特定のユーザーの MFA 方法をリセットします。 次に、その UserPrincipalName を使用してリセット コマンドを実行し、B2B コラボレーション ユーザーにもう一度証明方法を設定するように要求することができます。 例:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->



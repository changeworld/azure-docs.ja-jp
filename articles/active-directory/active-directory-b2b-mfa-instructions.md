---
title: "Azure Active Directory B2B コラボレーション ユーザーの条件付きアクセス | Microsoft Docs"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: fac4657e9b78e900c052384e212aa9f3915f970d
ms.lasthandoff: 04/13/2017


---

# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの条件付きアクセス

## <a name="multi-factor-authentication-for-b2b-users"></a>B2B ユーザーの多要素認証
Azure AD B2B コラボレーションでは、組織は B2B ユーザーの多要素認証ポリシーを一意に適用することができます。 このポリシーは、組織のフルタイムの従業員とメンバーについてこのポリシーを有効にするのと同じ方法で、テナント レベル、アプリ レベル、または個々のユーザー レベルで適用できます。 MFA ポリシーは、リソース組織で適用します。

そのため、次のようになります。
1. 会社 A の管理者またはインフォメーション ワーカーが、会社 B のユーザーを会社 A のアプリケーション Foo に招待します。
2. 会社 A のアプリケーション *Foo* は、アクセス時に MFA を要求するように構成されています。
3. 会社 B のユーザーが会社 A テナントのアプリ Foo にアクセスしようとすると、会社 A の MFA ポリシーで要求されているように MFA チャレンジを完了するように求められます。
4. ユーザーは、会社 A で MFA をセットアップし、MFA オプションを選択することができます。
5. これは、どの ID でも機能します (会社 B のユーザーがソーシャル ID を使用して認証を行う場合は、たとえば Azure AD または MSA)。
6. 会社 A は、MFA をサポートしている十分な数の Premium Azure AD SKU を所持している必要があります。 会社 B のユーザーは、A 社のこのライセンスを使用します。

まとめると、パートナー組織自体ではなく招待側のテナントが、"*常に*" パートナー組織の B2B コラボレーション ユーザーの MFA に対する責任を持ちます (パートナー組織が MFA 機能を持っている場合でも)。

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザー用の MFA のセットアップ
B2B コラボレーション ユーザー用の MFA のセットアップがいかに簡単かについては、次のビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B ユーザーがオファーに応じるための MFA エクスペリエンス
招待に応じるためのエクスペリエンスについては、次のビデオのアニメーションをご覧ください。

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの MFA リセット
現時点では、管理者は次の PowerShell コマンドレットを使用することによってのみ、B2B コラボレーション ユーザーにもう一度証明を要求できます。 そのため、B2B コラボレーション ユーザーの証明方法をリセットする場合は、以下の PowerShell コマンドレットを使用する必要があります。

1. Azure への接続

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. すべてのユーザーと証明方法を取得します。

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  たとえば次のようになります。

  ```
  PS C:\Users\tjwasserGet-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 特定のユーザーの MFA 方法をリセットします。 次に、その UserPrincipalName を使用してリセット コマンドを実行し、B2B コラボレーション ユーザーにもう一度証明方法を設定するように要求することができます。 例:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>リソース テナントで MFA を実行する理由

現在のリリースでは、MFA は常にリソース テナントにあります。 その理由は予測可能性です。

たとえば、Contoso ユーザー (Sally) が Fabrikam に招待され、Fabrikam が B2B ユーザーの MFA を有効にしたとします。

ここで、Contoso の App1 については MFA ポリシーが有効になっていて、App2 については有効になっていない場合、Contoso の MFA 要求によってトークン内で Sally が Fabrikam で MFA を実行するかどうかを決定すると、次の問題が発生する可能性があります。

* 1 日目: Sally は App1 にアクセスするため Contoso で MFA を実行しましたが、その場合 Fabrikam では MFA 要求は表示されません。

* 2 日目: Sally は Contoso で App 2 にアクセスしたので、Fabrikam にアクセスする際、Fabrikam で MFA の登録を行う必要があります。

このため Sally はかなり混乱して、サインインを完了できなくなる可能性があります。

さらに、Contoso に MFA の機能があっても、Fabrikam が Contoso の MFA ポリシーを信頼するとは限りません。

最後に、リソース テナントの MFA は、MSA とソーシャル ID、および MFA をセットアップしていないパートナー組織でも機能します。

したがって、B2B ユーザーの MFA では、常にリソース MFA を要求することをお勧めします。 この場合、MFA が重複する可能性がありますが、いつリソース テナントにアクセスしてもエンドユーザーのエクスペリエンスは予測可能なので、Sally は リソース テナントで MFA の登録を行う必要があります。

### <a name="device-location-and-risk-based-conditional-access-for-b2b-users"></a>B2B ユーザー用のデバイス、場所、リスクに基づく条件付きアクセス

Contoso 組織が会社のデータに関してデバイス ベースの条件付きアクセス ポリシーを有効にした場合、非管理対象デバイス (つまり、Contoso 組織によって管理されておらず、Contoso のデバイス ポリシーに準拠していないデバイス) からのアクセスは禁止されます。

つまり、B2B ユーザーのデバイスが Contoso によって管理されていない場合、このポリシーが適用されるどのような環境でもパートナー組織の B2B ユーザーのアクセスはブロックされます。

別の組織のユーザーに、招待する側の組織にそのユーザーのデバイスを管理してもらうよう期待するのは困難です。 そのため、今後の更新プログラムでは、Contoso が特定のパートナーのデバイス コンプライアンス状態を信頼できるようにします。 これにより、Fabrikam のユーザーが Fabrikam によって管理されるデバイスを使用している場合、そのユーザーが Contoso のリソースにもアクセスできるポリシーを Contoso が適用できるようになります。

一方、Contoso は、デバイス ベースの条件付きアクセス ポリシーで特定のパートナー ユーザーを含む除外リストを作成できます。

#### <a name="location-based-conditional-access-for-b2b"></a>B2B 向けの場所ベースの条件付きアクセス

招待する側の組織 (たとえば、Contoso) がパートナー組織 (たとえば、Fabrikam) を定義する信頼されたネットワーク境界 (つまり、IP アドレスの範囲) を作成できる場合は、B2B ユーザー用の場所ベースの条件付きアクセス ポリシーを適用することができます。

#### <a name="risk-based-conditional-access-for-b2b"></a>B2B 向けのリスクベースの条件付きアクセス

現時点では、リスク評価は B2B ユーザーのホーム組織 (言い換えれば、B2B ユーザーの身元確認テナント) で実行されるため、B2B ユーザー用のサインイン リスクベース ポリシーを適用することはできません。

今後の更新では、既知のリスクだけでなく、他の場所で発生する可能性があるために知るすべのないリスクからも Contoso が外部共有のアプリとデータを保護できるように、パートナーからのリスク スコアのフェデレーション (パートナーの同意のもと) について検討中です。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)


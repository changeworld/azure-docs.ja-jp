---
title: Azure Active Directory アプリケーションの管理証明書に関してよく寄せられる質問
description: Azure Active Directory を ID プロバイダー (IdP) として使用するアプリの証明書の管理についてよく寄せられる質問 (FAQ) とその回答について説明します。
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: iangithinji
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 0868c942a023662a1a6d3053477d85b0245fef4b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376241"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory (Azure AD) アプリケーションの管理証明書に関してよく寄せられる質問

このページでは、Azure Active Directory (Azure AD) を ID プロバイダー (IdP) として使用するアプリの証明書管理に関してよく寄せられる質問に回答します。

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>間もなく期限が切れる SAML 署名証明書の一覧を生成する方法はありますか?

[PowerShell スクリプト](app-management-powershell-samples.md)を使用して、ご自分のディレクトリのアプリを指定して、間もなく期限が切れるシークレット、証明書、およびそれらの所有者を含め、すべてのアプリ登録を CSV ファイルにエクスポートします。 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>間もなく期限切れになる証明書の更新手順に関する情報はどこで確認できますか?

手順については、[こちら](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire)を参照してください。

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Azure AD によって発行された証明書の有効期限をカスタマイズするにはどうすればよいですか?

既定では、SAML シングル サインオンの構成中に証明書が自動的に作成されるときに 3 年後に失効するように Azure AD によって構成されます。 証明書の日付は保存した後は変更できないので、新しい証明書を作成する必要があります。 その方法の手順については、「[フェデレーション証明書の有効期限のカスタマイズと、新しい証明書へのロールオーバー](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)」を参照してください。

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>証明書の有効期限通知を自動化するにはどうすればよいですか?

Azure AD では、SAML 証明書の有効期限が切れる 60 日前、30 日前、7 日前に、通知がメールで送信されます。 通知を受信するメール アドレスを複数追加できます。 

> [!NOTE]
> 通知一覧に最大 5 つのメール アドレスを追加できます (アプリケーションを追加した管理者のメール アドレスを含む)。 もっと多くのユーザーに通知する必要がある場合は、配布リストのメール アドレスを使用します。 

通知送信先のメール アドレスを指定するには、「[証明書の有効期限のメール通知アドレスの追加](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)」を参照してください。

`aadnotification@microsoft.com` から受信したこれらの電子メール通知を編集またはカスタマイズするオプションはありません。 ただし、[PowerShell スクリプト](app-management-powershell-samples.md)を使用して、間もなく期限が切れるシークレットと証明書を含め、アプリ登録をエクスポートできます。

## <a name="who-can-update-the-certificates"></a>誰が証明書を更新できますか?

アプリケーションの所有者、全体管理者、またはアプリケーション管理者が、Azure portal UI、PowerShell、または Microsoft Graph を使用して証明書を更新できます。

## <a name="i-need-more-details-about-certificate-signing-options"></a>証明書署名オプションの詳細について教えてください。

Azure AD では、証明書署名オプションと証明書署名アルゴリズムを設定できます。 詳細については、[Azure Active Directory アプリ用の詳細な SAML トークンの証明書署名オプション](certificate-signing-options.md)に関するページを参照してください。

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Azure AD アプリケーション プロキシ アプリケーションの証明書を置き換える必要があります。手順について教えてください。

Azure AD アプリケーション プロキシ アプリケーションの証明書を置き換えるには、[PowerShell のサンプル - アプリケーション プロキシ アプリケーションでの証明書の置換](scripts/powershell-get-custom-domain-replace-cert.md)に関するページを参照してください。

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシでカスタム ドメインの証明書を管理するにはどうすればよいですか?

カスタム ドメインを使用するようにオンプレミス アプリを構成するには、検証済みの Azure Active Directory カスタム ドメイン、カスタム ドメインの PFX 証明書、および構成するオンプレミス アプリが必要です。 詳細については、[Azure AD アプリケーション プロキシのカスタム ドメイン](application-proxy-configure-custom-domain.md)に関するページを参照してください。 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>アプリケーション側でトークン署名証明書を更新する必要があります。 Azure AD 側のどこで入手できますか?

SAML x.509 証明書を更新できます。「[SAML 署名証明書](configure-saml-single-sign-on.md#saml-signing-certificate)」を参照してください。

## <a name="what-is-azure-ad-signing-key-rollover"></a>Azure AD 署名キーのロールオーバーとは何ですか?

詳細については、 [こちら](../develop/active-directory-signing-key-rollover.md)を参照してください。 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>アプリケーション トークン暗号化証明書を更新するにはどうすればよいですか?

アプリケーション トークン暗号化証明書を更新するには、[エンタープライズ アプリケーションのトークン暗号化証明書を更新する](howto-saml-token-encryption.md)方法に関するページを参照してください。 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>アプリケーション トークン署名証明書を更新するにはどうすればよいですか?

アプリケーション トークン署名証明書を更新するには、[エンタープライズ アプリケーションのトークン署名証明書を更新する方法](manage-certificates-for-federated-single-sign-on.md)に関するページを参照してください。

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>フェデレーション証明書を変更した後に Azure AD を更新するにはどうすればよいですか?

フェデレーション証明書を変更した後に Azure AD を更新するには、「[Microsoft 365 および Azure Active Directory 用のフェデレーション証明書の更新](../hybrid/how-to-connect-fed-o365-certs.md)」を参照してください。

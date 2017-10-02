---
title: "Azure Active Directory の事前統合アプリ用の SAML トークンの詳細な証明書署名オプション | Microsoft Docs"
description: "Azure Active Directory の事前統合アプリ用の SAML トークンの詳細な証明書署名オプションの使い方について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: ja-jp
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory のギャラリー アプリ用の SAML トークンの詳細な証明書署名オプション
現在 Azure Active Directory は、SAML 2.0 プロトコルを使ったシングル サインオンをサポートする 500 以上のアプリケーションを含め、何千もの事前統合済みのアプリケーションを Azure AD アプリケーション ギャラリーでサポートしています。 ユーザーが Azure AD によって SAML を使ってアプリケーションに対して認証されると、Azure AD は、アプリケーションにトークンを送信します (HTTP POST 経由)。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してユーザーをログオンします。 これらの SAML トークンは、Azure AD で生成された一意の証明書で署名されます。 この SAML トークンの署名には、特定の標準アルゴリズムが使用されます。

Azure Active Directory では、ギャラリー アプリケーションにいくつかの既定の設定が使用されます。 これらの既定値はアプリケーションの要件に応じて設定されます。

Azure Active Directory では、証明書署名の詳細設定をサポートしています。 これらのオプションを選択するには、まず、次に示すように、**SAML の証明書署名の詳細設定**のチェック ボックスをオンにします。

![証明書署名オプション][1]

このチェック ボックスをオンにすると、**証明書署名オプション**と**証明書署名アルゴリズム**を設定できます。

## <a name="certificate-signing-options"></a>証明書署名オプション

Azure AD でサポートされる 3 種類の証明書署名オプションを次に示します。

1. **[SAML アサーションへの署名]** - ほとんどのギャラリー アプリケーションで設定される既定のオプション。 このオプションを選択した場合、Azure AD が IDP として、アプリケーションの X509 証明書を使って SAML アサーションと証明書に署名します。 また、下のドロップダウンから選択された署名アルゴリズムが使用されます。

2. **[SAML 応答への署名]** - このオプションを選択した場合、Azure AD が IDP として、アプリケーションの X509 証明書を使って SAML 応答に署名します。 また、下のドロップダウンから選択された署名アルゴリズムが使用されます。

3. **[SAML 応答とアサーションへの署名]** - このオプションを選択した場合、Azure AD が IDP として、アプリケーションの X509 証明書を使って SAML トークン全体に署名します。 また、下のドロップダウンから選択された署名アルゴリズムが使用されます。

    ![証明書署名オプション][4]

## <a name="certificate-signing-algorithm"></a>証明書署名アルゴリズム

Azure Active Directory は、SAML 応答への署名に際して 2 種類の署名アルゴリズムをサポートしています。

1. SHA256 - Azure Active Directory が SAML 応答の署名に使用する既定のアルゴリズムです。 これは最新のアルゴリズムであり、SHA1 よりも安全として扱われます。 SHA256 アルゴリズムは、ほとんどのアプリケーションでサポートされます。 アプリケーションで署名アルゴリズムとしてサポートされるのが Sha1 のみの場合は、アルゴリズムを変更できます。 それ以外の場合、SAML 応答の署名には SHA256 アルゴリズムの使用をお勧めします。

    ![SHA256 証明書署名アルゴリズム][3]

2. SHA1 - 古いアルゴリズムであり、安全性が低いと考えられています。 アプリケーションでこの署名アルゴリズムしかサポートされていない場合は、このオプションをドロップダウンから選択してください。 その場合、SAML 応答の署名に Sha1 アルゴリズムが使用されます。

    ![SHA1 証明書署名アルゴリズム][2]

## <a name="next-steps"></a>次のステップ
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](active-directory-saas-custom-apps.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png

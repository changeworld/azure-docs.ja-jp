---
title: Azure Active Directory の事前統合アプリ用の SAML トークンの詳細な証明書署名オプション | Microsoft Docs
description: Azure Active Directory の事前統合アプリ用の SAML トークンの詳細な証明書署名オプションの使い方について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: e7488abb3e82f90f63fa338b84a6516202e504ec
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714532"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory のギャラリー アプリ用の SAML トークンの詳細な証明書署名オプション
現在、Azure Active Directory (Azure AD) では、Azure Active Directory アプリ ギャラリーにある数千の事前統合アプリケーションをサポートしています。 この数には、SAML 2.0 プロトコルを使用してシングル サインオンをサポートする 500 を超えるアプリケーションが含まれます。 ユーザーが Azure AD によって SAML を使ってアプリケーションに対して認証されると、Azure AD は、アプリケーションにトークンを送信します (HTTP POST 経由)。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してユーザーをログインします。 これらの SAML トークンは、Azure AD で、特定の標準的なアルゴリズムで生成された一意の証明書で署名されます。

Azure AD では、ギャラリー アプリケーションにいくつかの既定の設定が使用されます。 これらの既定値はアプリケーションの要件に応じて設定されます。

Azure AD では、高度な証明書署名設定をサポートします。 これらのオプションを選択するには、まず、次に示すように、**[証明書署名の詳細設定を表示する]** チェック ボックスをオンにします。

![証明書署名の詳細設定を表示する](./media/certificate-signing-options/saml-advance-certificate.png)

このチェック ボックスをオンにすると、証明書署名オプションと証明書署名アルゴリズムを設定できます。

## <a name="certificate-signing-options"></a>証明書署名オプション

Azure AD では、次の 3 つの証明書署名オプションがサポートされています。

* **SAML アサーションへの署名**。 この既定のオプションは、ギャラリーのアプリケーションのほとんどに設定されます。 このオプションを選択した場合、Azure AD が IdP として、アプリケーションの X509 証明書を使って SAML アサーションと証明書に署名します。 また、**[署名アルゴリズム]** ドロップダウンから選択された署名アルゴリズムが使用されます。

* **SAML 応答の署名**。 このオプションを選択した場合、Azure AD が IdP として、アプリケーションの X509 証明書を使って SAML 応答に署名します。 また、**[署名アルゴリズム]** ドロップダウンから選択された署名アルゴリズムが使用されます。

* **SAML 応答とアサーションへの署名**。 このオプションを選択した場合、Azure AD が IdP として、アプリケーションの X509 証明書を使って SAML トークン全体に署名します。 また、**[署名アルゴリズム]** ドロップダウンから選択された署名アルゴリズムが使用されます。

    ![証明書署名オプション](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>証明書署名アルゴリズム

Azure AD では、SAML 応答に署名するための 2 つの署名アルゴリズムがサポートされています。

* **SHA-256**。 Azure AD では、この既定のアルゴリズムを使用して、SAML 応答に署名します。 これは最新のアルゴリズムであり、SHA-1 よりも安全性が高いと考えられています。 ほとんどのアプリケーションで、SHA-256 アルゴリズムをサポートしています。 アプリケーションが署名アルゴリズムとして SHA-1 しかサポートしていない場合は、設定を変更することができます。 それ以外の場合、SAML 応答の署名には SHA-256 アルゴリズムの使用をお勧めします。

    ![SHA-256 証明書署名アルゴリズム](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **SHA-1**。 これは古いアルゴリズムであり、SH-256 より安全性が低いと考えられています。 アプリケーションでこの署名アルゴリズムしかサポートされていない場合は、このオプションを **[署名アルゴリズム]** ドロップダウン リストから選択してください。 これで、Azure AD は SHA-1 アルゴリズムを使用して SAML 応答に署名します。

    ![SHA-1 証明書署名アルゴリズム](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>次の手順
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](configure-federated-single-sign-on-non-gallery-applications.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](../develop/howto-v1-debug-saml-sso-issues.md)



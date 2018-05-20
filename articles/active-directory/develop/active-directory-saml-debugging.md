---
title: Azure Active Directory のアプリケーションに対す SAML に基づいたシングル サインオンをデバッグする方法 | Microsoft Docs
description: 'Azure Active Directory のアプリケーションに対す SAML に基づいたシングル サインオンをデバッグする方法について説明します  '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法

SAML ベースのアプリケーション統合をデバッグするときは、多くの場合、[Fiddler](http://www.telerik.com/fiddler) などのツールを使用して、アプリケーションに対して発行された SAML トークンを含む SAML 要求と SAML 応答を確認すると便利です。 

![Fiddler][1]

多くの場合、問題は Azure Active Directory やアプリケーション側の構成の誤りに関連しています。 エラーが表示される場所に応じて、SAML 要求または SAML 応答を確認する必要があります。

- 会社のサインイン ページにエラーが表示される [セクションへのリンク]
- サインイン後のアプリケーションのページにエラーが表示される [セクションへのリンク]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>会社のサインイン ページにエラーが表示される

エラー コードと解決手順の 1 対 1 のマッピングについては、「[フェデレーション シングル サインオン用に構成されたギャラリー アプリケーションへのサインインに関する問題](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)」を参照してください。

会社のサインイン ページにエラーが表示される場合、問題をデバッグするには、エラー メッセージと SAML 要求が必要です。

### <a name="how-can-i-get-the-error--message"></a>エラー メッセージを確認する方法

エラー メッセージを確認するには、ページの右下を見ます。 次の情報を含むエラーが表示されます。

- CorrelationId
- タイムスタンプ
- メッセージ

![エラー][2] 

 
CorrelationId とタイムスタンプは、サインイン エラーに関連するバックエンド ログを見つけるために使用できる一意の識別子になります。 これらの値があると、Microsoft のエンジニアが問題の解決策をより迅速に提供できるため、Microsoft とのサポート ケースを作成するときに重要です。

このメッセージは、サインインの問題の根本原因です。 


### <a name="how-can-i-review-the-saml-request"></a>SAML 要求を確認する方法

通常、アプリケーションから Azure Active Directory に送信される SAML 要求は、[https://login.microsoftonline.com](https://login.microsoftonline.com) からの最後の HTTP 200 応答です。
 
Fiddler を使用して、この行を選択し、右側のパネルで **[インスペクター]、[WebForms]** タブの順に選択することで、SAML 要求を確認できます。 **SAMLRequest** 値を右クリックし、**[TextWizard に送信]** を選択します。 次に、**[変換]** メニューの **[Base64 から]** を選択してトークンをデコードし、その内容を確認します。 

また、SAMLrequest の値をコピーし、別の Base64 デコーダーを使用することもできます。

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

デコードされた SAML 要求を使用して、以下を確認します。

1. SAML 要求の**送信先**は、Azure Active Directory から取得した SAML シングル サインオン サービス URL に対応します。
 
2. SAML 要求の**発行者**は、Azure Active Directory のアプリケーションに構成した**識別子**と同じです。 Azure AD は、発行者を使用してディレクトリ内のアプリケーションを検索します。

3. **AssertionConsumerServiceURL** は、アプリケーションが Azure Active Directory から SAML トークンを受け取ることができる場所です。 この値は Azure Active Directory で構成できますが、SAML 要求の一部として必須の値ではありません。


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>サインイン後のアプリケーションのページにエラーが表示される

このシナリオでは、アプリケーションが Azure AD から発行された応答を受け入れません。 SAML トークンを含む Azure AD からの応答をアプリケーション ベンダーに確認して、何が不足しているか、または間違っているかを把握することが重要です。 

### <a name="how-can-i-get-and-review-the-saml-response"></a>SAML 応答を取得して確認する方法

SAML トークンを含む Azure AD からの応答は、通常は、https://login.microsoftonline.com からの HTTP 302 リダイレクトの後に発生する応答であり、アプリケーションの構成済みの**応答 URL** に送信されます。 

SAML トークンは、行を選択し、右側のパネルで **[インスペクター]、[WebForms]** の順に選択することで表示できます。 そこから、**SAMLResponse** 値を右クリックし、**[TextWizard に送信]** を選択します。 次に、**[変換]** メニューの **[Base64 から]** を選択してトークンをデコードし、その内容を確認します。 

**SAMLrequest** の値をコピーし、別の Base64 デコーダーを使用することもできます。

SAML 応答に何が不足しているか、または間違っているかについては、[サインイン後のアプリケーションのページのエラー](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)に関するトラブルシューティング ガイドを参照してください。

SAML 応答を確認する方法については、「[シングル サインオンの SAML プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response)」を参照してください。


## <a name="related-articles"></a>関連記事
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png

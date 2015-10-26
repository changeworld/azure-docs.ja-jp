<properties
   pageTitle="Azure Active Directory SSO と既存のアプリケーションの統合 | Microsoft Azure"
   description="Azure Active Directory のシングル サインオン認証とユーザー プロビジョニングを有効にして、既に使用している SaaS アプリケーションを管理します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# Azure Active Directory SSO と既存のアプリケーションの統合

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## その他の考慮事項

既に使用しているアプリケーションの SSO の設定は、新しいアプリケーションでの新しいアカウントの作成とは別のプロセスです。アプリケーションを Azure AD に統合すると、管理者とユーザーのエクスペリエンスが変わります。

### 既に使用しているアプリケーションのシングル サインオンを設定するために、管理者が把握しておく必要があることは何でしょうか。

既存のアプリケーションの SSO を設定するには、Azure AD と SaaS アプリケーションの両方でグローバル管理者権限が必要です。

アプリケーションが既に使用されているので、ユーザーの確立されたアプリケーション ID をそれぞれの Azure AD ID にリンクする必要があります。電子メール アドレス、UPN (Universal Personal Name)、ユーザー名のどれであるかを問わず、対象のアプリケーションがサインオンの一意の識別子に使用しているものを把握しておくことが重要です。これが、Azure AD のユーザーの一意の識別子にリンクされます。アプリケーション ID と Azure AD ID のリンクの詳細については、[SAML トークンで発行されるクレームのカスタマイズに関するページ](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx)と、「[属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)」をご覧ください。

### エンド ユーザーへの影響

既に使用しているアプリケーションの SSO を統合するときは、ユーザー エクスペリエンスが影響を受けることを認識しておくことが重要です。すべてのアプリケーションで、ユーザーは Azure AD の資格情報を使用してサインインすることになります。また、アプリケーションにアクセスする際に別のポータルを使用することが必要な場合もあります。アプリケーションの独自の Web サイトで SSO を実行できるアプリケーションもあれば、ユーザーがアプリケーションの中央ポータル ([マイ アプリ](myapps.microsoft.com)や [Office365](portal.office.com/myapps)) からサインインする必要があるアプリケーションもあります。さまざまな種類の SSO とそのユーザー エクスペリエンスの詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## 次のステップ
- [新たに取得したアプリケーションと Azure Active Directory シングル サインオンを統合する](active-directory-sso-newly-acquired-saas-apps.md)方法を確認する
- [Azure Active Directory でのアプリケーション アクセスとシングル サインオン](active-directory-appssoaccess-whatis.md)の詳細を確認する
- [SaaS アプリケーションを統合する方法に関するチュートリアル](active-directory-saas-tutorial-list.md)を見つける
-	[Azure AD のセルフサービス SAML 構成](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)でカスタム アプリケーションを追加する

<!---HONumber=Oct15_HO3-->
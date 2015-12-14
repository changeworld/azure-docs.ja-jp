<properties
    pageTitle="SaaS アプリと Azure Active Directory シングル サインオンを統合する | Microsoft Azure"
    description="Azure Active Directory での SaaS アプリのシングル サインオン認証およびユーザー プロビジョニングによるアクセス管理の一元化を有効にします。Azure Active Directory を SaaS アプリに統合する方法の概要について説明します。"
    services="active-directory"
	  keywords="Azure AD と SaaS アプリの統合"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="12/01/2015"
      ms.author="curtand"/>

# SaaS アプリと Azure Active Directory シングル サインオンを統合する  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

組織に展開するアプリのためのシングル サインオンのセットアップ作業では、Azure Active Directory (Azure AD) 内にある既存のディレクトリを使用します。Microsoft Azure、Office 365、または Windows Intune を通じて取得した Azure AD ディレクトリを使用できます。これらのうち 2 つ以上を所有している場合は、「[Azure AD ディレクトリの管理](active-directory-administer.md)」を参照して、どれを使用するかを判断してください。

## 認証

プロトコル SAML 2.0、Ws-federation、または OpenID Connect をサポートしているアプリケーションは、Azure Active Directory を使用して署名証明書を信頼関係を確立できます。詳細については、「[フェデレーション シングル サインオンのための証明書の管理](active-directory-sso-certs.md)」を参照してください。

HTML フォーム ベースのサインインのみをサポートするアプリケーションの場合、Azure Active Directory では "パスワード保管" を使用して信頼関係を確立します。これにより、Azure AD は SaaS アプリケーションから取得したユーザー アカウント情報を使用して、組織のユーザーを SaaS アプリケーションに自動的にサインインします。ユーザー アカウント情報および関連するパスワードは Azure AD によって収集され、安全に格納されます。詳細については、「[パスワードベースのシングル サインオン](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)」を参照してください。

## 承認

プロビジョニングされたアカウントを使用すると、ユーザーがシングル サインオンを介して認証された後にアプリケーションの使用を許可できます。ユーザーのプロビジョニングは手動で実行できます。また、Azure Active Directory で行われた変更に基づいて、SaaS アプリケーションに対してユーザー情報の追加や削除を実行できる場合もあります。既存の Azure AD コネクタを使用して自動プロビジョニングを実現する方法の詳細については、「[SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)」を参照してください。

それ以外の場合は、アプリにユーザー情報を手動で追加するか、市販されている他のプロビジョニング ソリューションを使用できます。

## アクセス

Azure AD には、組織内のエンド ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法が複数用意されています。特定のデプロイ ソリューションやアクセス ソリューションに限定されているわけではありません。[ニーズに合わせて最適なソリューション](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)を使用できます。

## 使用中のアプリケーションに関する追加の考慮事項

組織が既に使用しているアプリケーションにシングル サインオンをセットアップするプロセスは、新しいアプリケーションに新しいアカウントを作成するプロセスとは異なります。いくつかの準備手順: アプリケーション内のユーザー ID を Azure AD の ID にマップする、統合後にユーザーがアプリケーションへのログインを経験する方法を把握する、など。

> [AZURE.NOTE]既存のアプリケーションの SSO を設定するには、Azure AD と SaaS アプリケーションの両方でグローバル管理者権限が必要です。

### ユーザー アカウントのマップ

ユーザー ID には、通常、一意の識別子が含まれます。それには電子メール アドレスまたは UPN (Universal Personal Name) が使用可能です。ユーザーの各アプリケーション ID は、ユーザーの各 Azure AD ID にリンク (マップ) する必要があります。アプリケーション認証の要件に応じて、これを実現する方法はいくつかあります。

アプリケーション ID と Azure AD ID のマッピングの詳細については、[SAML トークンで発行される要求のカスタマイズに関するページ](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx)と、「[プロビジョニングにおける属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)」をご覧ください。

### ユーザーのログイン エクスペリエンスの理解

既に使用しているアプリケーションの SSO を統合するときは、ユーザー エクスペリエンスが影響を受けることを認識しておくことが重要です。すべてのアプリケーションで、ユーザーは Azure AD の資格情報を使用してサインインすることになります。また、アプリケーションにアクセスする際に別のポータルを使用することが必要な場合もあります。

アプリケーションのサインイン インターフェイスで SSO を実行できるアプリケーションもあれば、ユーザーが中央ポータル ([マイ アプリ](http://myapps.microsoft.com)や [Office365](http://portal.office.com/myapps) など) からサインインする必要があるアプリケーションもあります。さまざまな種類の SSO とそのユーザー エクスペリエンスの詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

「[開発者ガイド](active-directory-applications-guiding-developers-for-lob-applications.md)」の「*ユーザーの同意の抑制*」も参照してください。

## 次のステップ


アプリケーション ギャラリーにある SaaS アプリについては、[SaaS アプリを統合する方法に関するチュートリアル](active-directory-saas-tutorial-list.md)が Azure AD 内に多数用意されています。

アプリケーション ギャラリーにないアプリは、[カスタム アプリケーションとして Azure AD アプリケーション ギャラリーに追加](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)できます。

Azure.com ライブラリには、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をはじめとして、ここで紹介した問題に関する詳細情報があります。

<!---HONumber=AcomDC_1203_2015-->
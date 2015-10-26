<properties
   pageTitle="新たに取得したアプリと Azure Active Directory シングル サインオンを統合する | Microsoft Azure"
   description="Azure Active Directory は、Azure ポータルでのアクセス管理を一元化するために、新しく取得した SaaS アプリのシングル サインオンをサポートしています。"
   services="active-directory"
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
      ms.date="10/09/2015"
      ms.author="curtand"/>

# 新しく取得したアプリと Azure Active Directory シングル サインオンを統合する  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

組織に展開するアプリのためのシングル サインオンのセットアップ作業では、Azure Active Directory 内にある既存のディレクトリを使用します。Microsoft Azure、Office 365、または Windows Intune を通じて取得した Azure AD ディレクトリを使用できます。これらのうち 2 つ以上を所有している場合は、「[Azure AD ディレクトリの管理](active-directory-administer.md)」を参照して、どれを使用するかを判断してください。

## その他の考慮事項

### 認証

プロトコル SAML 2.0、Ws-federation、または OpenID Connect をサポートしているアプリケーションは、Azure Active Directory を使用して署名証明書を信頼関係を確立できます。詳細については、「[フェデレーション シングル サインオンのための証明書の管理](active-directory-sso-certs.md)」を参照してください。

HTML フォーム ベースのサインインのみをサポートするアプリケーションの場合、Azure Active Directory では "パスワード保管" を使用して信頼関係を確立します。これにより、Azure AD は SaaS アプリケーションから取得したユーザー アカウント情報を使用して、組織のユーザーを SaaS アプリケーションに自動的にサインインします。ユーザー アカウント情報および関連するパスワードは Azure AD によって収集され、安全に格納されます。詳細については、「[パスワードベースのシングル サインオン](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)」を参照してください。

### 承認

プロビジョニングされたアカウントを使用すると、ユーザーがシングル サインオンを介して認証された後にアプリケーションの使用を許可できます。ユーザーのプロビジョニングは手動で実行できます。また、Azure Active Directory で行われた変更に基づいて、SaaS アプリケーションに対してユーザー情報の追加や削除を実行できる場合もあります。既存の Azure AD コネクタを使用して自動プロビジョニングを実現する方法の詳細については、「[SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)」を参照してください。

それ以外の場合は、アプリにユーザー情報を手動で追加するか、市販されている他のプロビジョニング ソリューションを使用できます。

### アクセス

Azure AD には、組織内のエンド ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法が複数用意されています。特定のデプロイ ソリューションやアクセス ソリューションに限定されているわけではありません。[ニーズに合わせて最適なソリューション](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)を使用できます。

## 次のステップ

組織内の既存の SaaS アプリでシングル サインオンを有効にする場合は、「[Azure Active Directory SSO と既存のアプリケーションの統合](active-directory-sso-integrate-existing-apps.md)」を参照してください。

アプリケーション ギャラリーにある SaaS アプリについては、[SaaS アプリを統合する方法に関するチュートリアル](active-directory-saas-tutorial-list.md)が Azure Active Directory 内に多数用意されています。

アプリケーション ギャラリーにないアプリは、[カスタム アプリケーションとして Azure Active Directory アプリケーション ギャラリーに追加](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)できます。

Azure.com ライブラリには、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をはじめとして、ここで紹介した問題に関する詳細情報があります。

<!---HONumber=Oct15_HO3-->
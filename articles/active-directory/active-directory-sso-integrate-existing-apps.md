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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# Azure Active Directory SSO と既存のアプリケーションの統合

## 概要

組織が既に使用しているアプリケーションにシングル サインオン (SSO) をセットアップするプロセスは、新しいアプリケーションに新しいアカウントを作成するプロセスとは異なります。いくつかの準備手順: アプリケーション内のユーザー ID を Azure Active Directory (AD) の ID にマップする、統合後にユーザーがアプリケーションへのログインを経験する方法を把握する、など。

> [AZURE.NOTE]既存のアプリケーションの SSO を設定するには、Azure AD と SaaS アプリケーションの両方でグローバル管理者権限が必要です。

## ユーザー アカウントのマップ

ユーザー ID には、通常、一意の識別子が含まれます。それには電子メール アドレスまたは UPN (Universal Personal Name) が使用可能です。ユーザーの各アプリケーション ID は、ユーザーの各 Azure AD ID にリンク (マップ) する必要があります。アプリケーション認証の要件に応じて、これを実現する方法はいくつかあります。

アプリケーション ID と Azure AD ID のマッピングの詳細については、[SAML トークンで発行される要求のカスタマイズに関するページ](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx)と、「[プロビジョニングにおける属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)」をご覧ください。

## ユーザーのログイン エクスペリエンスの理解

既に使用しているアプリケーションの SSO を統合するときは、ユーザー エクスペリエンスが影響を受けることを認識しておくことが重要です。すべてのアプリケーションで、ユーザーは Azure AD の資格情報を使用してサインインすることになります。また、アプリケーションにアクセスする際に別のポータルを使用することが必要な場合もあります。

アプリケーションのログイン インターフェイスで SSO を実行できるアプリケーションもあれば、ユーザーが中央ポータル ([マイ アプリ](http://myapps.microsoft.com)や [Office365](http://portal.office.com/myapps) など) からサインインする必要があるアプリケーションもあります。さまざまな種類の SSO とそのユーザー エクスペリエンスの詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

記事「[開発者ガイド](active-directory-applications-guiding-developers-for-lob-applications.md)」の「*ユーザーの同意の抑制*」も参照してください。

## 関連記事
「ユーザーの同意の抑制」および開発者ガイドのその他の項目へのリンク

<!---HONumber=Oct15_HO4-->
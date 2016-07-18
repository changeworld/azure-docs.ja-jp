<properties
   pageTitle="Azure Active Directory の AppSource 認定を取得する方法 | Microsoft Azure"
   description="Azure Active Directory の AppSource 認定を取得する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/05/2016"
   ms.author="skwan;bryanla"/>

#Azure Active Directory (AD) の AppSource 認定を取得する方法 

Azure AD の AppSource 認定を取得するには、アプリケーションで OpenID Connect、OAuth 2.0、または SAML 2.0 プロトコルを使用して、Azure AD にマルチテナント サインイン パターンを実装する必要があります。

Azure AD サインインまたはマルチテナント アプリケーションの開発に慣れていない場合は、次の手順に従ってください。

1. まず、[「Azure AD の認証シナリオ」のブラウザー対 Web App アプリケーションのシナリオ][AAD-Auth-Scenarios-Browser-To-WebApp]を読みます。
2. 次に、Azure AD の [Web アプリケーション クイックスタート ガイド][AAD-QuickStart-Web-Apps]を確認します。これらのガイドには、サインインの実装方法の説明とコード サンプルが記載されています。
3. Azure AD にマルチテナント サインインを実装する方法の詳細について、「[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法][AAD-Howto-Multitenant-Overview]」を確認します。

## 関連コンテンツ
Azure AD サインインがサポートされるアプリケーションの構築についての詳細、またはヘルプとサポートが必要な場合は、「[Azure Active Directory 開発者ガイド][AAD-Dev-Guide]」を参照してください。

Microsoft のコンテンツ改善のため、下部の Disqus コメント セクションよりご意見をお寄せください。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->

<!---HONumber=AcomDC_0706_2016-->
---
title: "Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示"
description: "シングル サインオンをサポートするアプリケーションを Azure Active Directory ギャラリーに表示する方法 | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示


##  <a name="what-is-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーとは

Azure AD は、クラウド ベースの ID サービスです。 [Azure AD アプリ ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/) は、シングル サインオンおよびユーザー プロビジョニング用のすべてのアプリケーション コネクタが公開されている共通ストアです。 ID プロバイダーとして Azure AD を使用している共通のお客様が、ここに公開されているさまざまな SaaS アプリケーション コネクタを検索します。 IT 管理者は、アプリ ギャラリーからコネクタを追加し、構成して、シングル サインオンおよびプロビジョニングに使用します。 Azure AD では、シングル サインオンに対応する SAML 2.0、OpenID Connect、OAuth、WS-Fed などの主要なフェデレーション プロトコルをすべてサポートします。 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>ギャラリーにアプリケーションを公開する利点とは

*  考えられる最良のシングル サインオン エクスペリエンスを顧客に提供します。

*  アプリケーションの構成をシンプルかつ単純にします。

*  顧客がアプリケーションをギャラリーで検索して見つけることができます。 

*  すべての顧客が、Azure AD SKU (Free、Basic、Premium) に関係なく、この統合を使用できます。

*  共通の顧客向けの詳細な構成手順チュートリアルがあります。

*  SCIM を使用している場合、同じアプリのユーザー プロビジョニングを有効にできます。


##  <a name="what-are-the-pre-requisites"></a>前提条件

Azure AD ギャラリー内のアプリケーションの一覧を表示するには、まず、アプリケーションに Azure AD でサポートされているフェデレーション プロトコルのいずれかを実装する必要があります。 ここから、Azure AD アプリケーション ギャラリーの使用条件をご覧ください。 使っているプロトコル: 

*   **OpenID Connect** - Azure AD でマルチ テナント アプリケーションを作成し、アプリケーションの [Azure AD の同意フレームワーク](active-directory-integrating-applications.md#overview-of-the-consent-framework)を実装します。 すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにログイン要求を送信します。 トークンで受信したテナント ID とユーザーの UPN に基づいて顧客のユーザー アクセスを制御できます。 アプリケーションを Azure AD と統合するには、[開発者向けの手順](active-directory-authentication-scenarios.md)に従ってください。

*   **SAML 2.0 または WS-Fed** - アプリケーションに、SP または IDP モードで SAML/WS-Fed SSO 統合を行う機能が必要です。 SAML 2.0 をサポートするすべてのアプリケーションは、[この手順](../active-directory-saas-custom-apps.md)を使用して Azure AD テナントと直接統合し、カスタム アプリケーションを追加できます。

*   [パスワード SSO](../active-directory-appssoaccess-whatis.md) - HTML サインイン ページがある Web アプリケーションを作成して、**パスワード ベースのシングル サインオン** を構成します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。 

## <a name="process-for-submitting-the-request-in-the-portal"></a>ポータルで要求を送信するためのプロセス

アプリケーションの統合が Azure AD で動作することをテストしたら、[アプリケーション ネットワーク ポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)へのアクセスを求める要求を送信する必要があります。 Office 365 アカウントがある場合は、これを使ってこのポータルにログインできます。ない場合は、Microsoft ID (Live ID、Outlook、Hotmail など) を使ってログインします。 アクセスを要求する次のページが表示されます。 テキスト ボックスに業務の妥当性を入力し、**[アクセスの要求]** をクリックします。 Microsoft のチームが、すべての詳細を確認し、適宜アクセスを付与します。 その後に、ポータルにログオンして、アプリケーションの詳細な要求を送信できるようになります。

アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

![SharePoint ポータルでのアクセス要求](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>タイムライン
    
*   ギャラリーに SAML 2.0 または WS-Fed アプリケーションを公開するプロセス - **7 営業日から 10 営業日**

   ![ギャラリーに SAML アプリケーションを公開するタイムライン](./media/active-directory-app-gallery-listing/timeline.png)

*   ギャラリーに OpenID Connect アプリケーションを公開するプロセス - **2 営業日から 5 営業日**

   ![ギャラリーに SAML アプリケーションを公開するタイムライン](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>エスカレーション

いずれのエスカレーションでも、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)に電子メールを送信するとすぐに Microsoft から連絡があります。


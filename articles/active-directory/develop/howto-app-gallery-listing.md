---
title: アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する | Microsoft Docs
description: シングル サインオンをサポートするアプリケーションを Azure Active Directory アプリ ギャラリーで公開する方法を説明します
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/14/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: 8c9d1ee51acdfff188e0d6483f723fbb08e17bd5
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601206"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する


##  <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーとは

Azure Active Directory (Azure AD) は、クラウド ベースの ID サービスです。 [Azure AD アプリケーション ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/)は、Azure Marketplace アプリ ストア内にあり、シングル サインオンおよびユーザー プロビジョニング用のすべてのアプリケーション コネクタが公開されています。 ID プロバイダーとして Azure AD を使うお客様は、ここで発行されているさまざまな SaaS アプリケーション コネクタを検索します。 IT 管理者は、アプリ ギャラリーからコネクタを追加した後、シングル サインオンおよびプロビジョニング用にコネクタを構成して使います。 Azure AD は、SAML 2.0、OpenID Connect、OAuth、WS-Fed など、シングル サインオン用の主要なフェデレーション プロトコルをすべてサポートします。

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>ギャラリーにアプリケーションを公開する利点とは

*  顧客は考えられる最良のシングル サインオン エクスペリエンスを探しています。

*  アプリケーションの構成は簡単で最小限です。

*  クイック検索でギャラリー内のアプリケーションが検索されます。

*  Free、Basic、Premium すべての Azure AD ユーザーがこの情報を使用できます。

*  共通の顧客向けの詳細な構成手順チュートリアルがあります。

*  SCIM を使っているユーザーは、同じアプリのプロビジョニングを使うことができます。

##  <a name="prerequisites-implement-federation-protocol"></a>前提条件: フェデレーション プロトコルを実装する

Azure AD アプリケーション ギャラリーにアプリケーションを一覧表示するには、まず、AzureAD でサポートされている、次のフェデレーション プロトコルのいずれかを実装し、Azure AD アプリケーション　ギャラリーの条件に同意する必要があります。 [こちら](https://azure.microsoft.com/en-us/support/legal/active-directory-app-gallery-terms/)から、Azure AD アプリケーション ギャラリーの使用条件を読み取ります。

*   **OpenID Connect**: Azure AD でマルチテナント アプリケーションを作成し、アプリケーションに [Azure AD 同意フレームワーク](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework)を実装します。 すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにログイン要求を送信します。 トークンで受け取ったテナント ID とユーザーの UPN に基づいてユーザー アクセスを制御できます。 アプリケーションを Azure AD と統合するには、[開発者向けの手順](authentication-scenarios.md)に従ってください。

    ![ギャラリーに OpenID Connect アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/openid.png)

    * OpenID Connect を使用してギャラリー内の一覧にアプリケーションを追加する場合は、上記の **[OpenID Connect & OAuth 2.0]** を選択します。

    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。 

*   **SAML 2.0** または **WS-Fed**: アプリケーションに、SP または IDP モードで SAML/WS-Fed SSO 統合を行う機能が必要です。 アプリが SAML 2.0 をサポートしている場合、[カスタム アプリケーションを追加する手順](../active-directory-saas-custom-apps.md)を使って、Azure AD テナントと直接統合できます。

    ![ギャラリーに SAML 2.0 または WS-Fed アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/saml.png)

    * **SAML 2.0** または **WS-Fed** を使用してギャラリー内の一覧にアプリケーションを追加する場合は、上記の **[SAMl 2.0/WS-Fed]** を選択します。

    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。 

*   [パスワード SSO](../manage-apps/what-is-single-sign-on.md): HTML サインイン ページがある Web アプリケーションを作成して、**パスワード ベースのシングル サインオン**を構成します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。

    ![ギャラリーに Password SSO アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/passwordsso.png)

    * Password SSO を使用してギャラリー内の一覧にアプリケーションを追加する場合は、上記の **[Password SSO]** を選択します。

    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

##  <a name="updateremove-existing-listing"></a>既存の公開を更新/削除する

Azure AD アプリ ギャラリーの既存のアプリケーションを更新または削除するには、最初に[アプリケーション ネットワーク ポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)で要求を送信する必要があります。 Office 365 アカウントがある場合は、それを使ってこのポータルにサインインします。 ない場合は、Microsoft アカウント (Outlook、Hotmail など) を使ってサインインします。

* 次の画面で適切なオプションを選びます

    ![ギャラリーに SAML アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/updateorremove.png)
    
    * 既存のアプリケーションを更新する場合は、**[Update existing application listing]\(既存のアプリケーション公開を更新する\)** を選びます。

    * Azure AD ギャラリーから既存のアプリケーションを削除する場合は、**[Remove existing application listing]\(既存のアプリケーション公開を削除する\)** を選びます。

    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。 

## <a name="submit-the-request-in-the-portal"></a>ポータルで要求を送信する

アプリケーションの統合が Azure AD で動作することをテストした後は、[アプリケーション ネットワーク ポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)へのアクセスを求める要求を送信します。 Office 365 アカウントがある場合は、それを使ってこのポータルにサインインします。 ない場合は、Microsoft アカウント (Outlook、Hotmail など) を使ってサインインします。

サインインすると、次のページが表示されます。 ビジネスでアクセスが必要な正当な理由をテキスト ボックスに入力し、**[アクセスの要求]** を選びます。 Microsoft のチームが詳細をレビューし、それに応じてアクセスを提供します。 その後、ポータルにサインインして、アプリケーションの詳細な要求を送信できます。

アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

![SharePoint ポータルでのアクセス要求](./media/howto-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>タイムライン
    
SAML 2.0 または WS-Fed アプリケーションをギャラリーに公開するプロセスのタイムラインは、7 ～ 10 営業日です。

   ![ギャラリーに SAML アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/timeline.png)

OpenID Connect アプリケーションをギャラリーに公開するプロセスのタイムラインは、2 ～ 5 営業日です。

   ![ギャラリーに SAML アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/timeline2.png)

ユーザー プロビジョニング サポートを使用してアプリケーションをギャラリーに公開するプロセスのタイムラインは、40 ～ 45 営業日です。

   ![ギャラリーに SAML アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>エスカレーション

すべてのエスカレーションについては、[Azure AD SSO 統合チーム](mailto:SaaSApplicationIntegrations@service.microsoft.com) (SaaSApplicationIntegrations@service.microsoft.com) にメールでご連絡いただけば、可能な限り早く対応します。
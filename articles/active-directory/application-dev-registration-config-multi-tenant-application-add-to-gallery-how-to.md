---
title: "Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションを追加する方法 | Microsoft Docs"
description: "独自に開発したマルチテナント アプリケーションを Azure AD アプリケーション ギャラリーで表示する方法について説明します"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションを追加する方法

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーとは

Azure AD は、クラウド ベースの ID サービスです。 [Azure AD アプリ ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/) は、シングル サインオンおよびユーザー プロビジョニング用のすべてのアプリケーション コネクタが公開されている共通ストアです。 ID プロバイダーとして Azure AD を使用している共通のお客様が、ここに公開されているさまざまな SaaS アプリケーション コネクタを検索します。 IT 管理者は、アプリ ギャラリーからコネクタを追加し、構成して、シングル サインオンおよびプロビジョニングに使用します。 Azure AD では、シングル サインオンに対応する SAML 2.0、OpenID Connect、OAuth、WS-Fed などの主要なフェデレーション プロトコルをすべてサポートします。 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされている場合
Azure AD アプリケーション ギャラリーで表示したいマルチテナント アプリケーションがある場合、次のいずれかのシングル サインオン テクノロジがそのアプリケーションでサポートされていることを最初に確認する必要があります。

1. **OpenID Connect** - Azure AD でマルチ テナント アプリケーションを作成し、アプリケーションの [Azure AD の同意フレームワーク](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework)を実装します。 すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにログイン要求を送信します。 トークンで受信したテナント ID とユーザーの UPN に基づいて顧客のユーザー アクセスを制御できます。 こちらの[記事](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)の説明に従って、アプリケーションを送信してください。

2. **SAML** - アプリケーションが SAML 2.0 をサポートしている場合は、ギャラリーにアプリケーションの一覧を表示できます。手順については、[こちら](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)を参照してください

Azure AD アプリケーション ギャラリーで表示したいマルチテナント アプリケーションで、こうしたシングル サインオン モードのいずれかがサポートされている場合は、[こちら](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)の記事の手順に従うことができます。 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされていない場合
これらのモードがどちらもアプリケーションでサポートされていない場合も、Microsoft のパスワード シングル サインオン テクノロジを使用してギャラリーに統合する方法があります。

[パスワード SSO](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis) - HTML サインイン ページがある Web アプリケーションを作成して、**パスワード ベースのシングル サインオン** を構成します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。 

このテクノロジを使用してアプリケーションの一覧を表示するには、[こちらの](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)記事の説明に従って、要求を送信してください。

## <a name="escalations"></a>エスカレーション

いずれのエスカレーションでも、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)に電子メールを送信するとすぐに Microsoft から連絡があります。

## <a name="next-steps"></a>次の手順
[Azure Active Directory アプリケーション ギャラリーでアプリケーションを表示する方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

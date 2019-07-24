---
title: .NET 用 Microsoft Authentication Library での AD FS サポート | Azure
description: .NET 用 Microsoft Authentication Library (MSAL.NET) での Active Directory フェデレーション サービス (AD FS) サポートについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576641"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET での Active Directory フェデレーション サービスのサポート
Windows Server での Active Directory フェデレーション サービス (AD FS) により、自分が開発しているアプリケーションに OpenID Connect および OAuth 2.0 ベースの認証と認可を追加して、それらのアプリケーションで AD FS に対して直接ユーザーを認証させることができます。 詳細については、「[開発者向けの AD FS のシナリオ](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)」をご覧ください。

.NET 用 Microsoft Authentication Library (MSAL.NET) では、AD FS に対する認証について 2 つのシナリオがサポートされています。

- MSAL.NET は、それ自体が AD FS と "*フェデレーションされている*" Azure Active Directory と通信します。
- MSAL.NET は、AD FS のバージョンが OpenID Connect に準拠している (AD FS 2019 以降) AD FS 機関と "*直接*" 通信します。 AD FS に直接接続すると、MSAL.NET は [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) で実行されているアプリで認証することができます。

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>AD FS とフェデレーションされている Azure AD への MSAL の接続
MSAL.NET では、管理対象ユーザー (Azure AD で管理されているユーザー) またはフェデレーション ユーザー (AD FS などの別の ID プロバイダーによって管理されているユーザー) にサインインする Azure AD への接続がサポートされています。 MSAL.NET は、ユーザーがフェデレーションされていることを認識しません。 それが関係している限りでは、Azure AD と通信します。

この場合に使用する[機関](msal-client-application-configuration.md#authority)は、通常の機関 (機関のホスト名 + テナント、common、または organizations) です。

### <a name="acquiring-a-token-interactively"></a>トークンを対話形式で取得する
`AcquireTokenInteractive` メソッドを呼び出すと、ユーザー エクスペリエンスは通常、次のようになります。

1. ユーザーは自分のアカウント ID を入力します。
2. Azure AD で、"Taking you to your organization's page" (組織のページに移動します) というメッセージ が一時的に表示されます。
3. ユーザーは、ID プロバイダーのサインイン ページにリダイレクトされます。 サインイン ページは、通常、組織のロゴでカスタマイズされます。

このフェデレーション シナリオでサポートされている AD FS バージョンは、AD FS v2、AD FS v3 (Windows Server 2012 R2)、および AD FS v4 (AD FS 2016) です。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>AcquireTokenByIntegratedAuthentication または AcquireTokenByUsernamePassword を使用してトークンを取得する
`AcquireTokenByIntegratedAuthentication` または `AcquireTokenByUsernamePassword` メソッドを使用してトークンを取得するときに、MSAL.NET はユーザー名に基づいて ID プロバイダーと通信します。  その ID プロバイダーと通信した後、MSAL.NET は [SAML 1.1 トークン](reference-saml-tokens.md)を受け取ります。  その後、MSAL.NET は、SAML トークンを Azure AD にユーザー アサーション ([On-Behalf-Of フロー](msal-authentication-flows.md#on-behalf-of)と同様のもの) として提供し、JWT を取得します。

## <a name="msal-connects-directly-to-ad-fs"></a>AD FS への MSAL の直接接続
MSAL.NET では、Open ID Connect に準拠している AD FS 2019 への接続がサポートされています。 AD FS に直接接続する場合、アプリケーションの構築に使用する機関は `https://mysite.contoso.com/adfs/` のようになります。

現在、AD FS 2016 および AD FS v2 (これらは OpenID Connect に準拠していません) への直接接続をサポートする予定はありません。 AD FS 2016 への直接接続が必要なシナリオをサポートする必要がある場合は、最新バージョンの [Azure Active Directory 認証ライブラリ](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)を使用してください。 オンプレミス システムを AD FS 2019 にアップグレードすると、MSAL.NET を使用できるようになります。

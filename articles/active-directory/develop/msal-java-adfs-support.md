---
title: AD FS のサポート (MSAL for Java)
titleSuffix: Microsoft identity platform
description: Java 用 Microsoft Authentication Library (MSAL4j) での Active Directory フェデレーション サービス (AD FS) サポートについて説明します。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696216"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>MSAL for Java での Active Directory フェデレーション サービスのサポート

Windows Server で Active Directory フェデレーション サービス (AD FS) を使用すると、Microsoft Authentication Library for Java (MSAL for Java) アプリに OpenID Connect と OAuth 2.0 ベースの認証と承認を追加できます。 統合後、アプリでは、Azure AD 経由でフェデレーションされた AD FS でユーザーを認証できます。 シナリオの詳細については、「[開発者向けの AD FS のシナリオ](/windows-server/identity/ad-fs/ad-fs-development)」をご覧ください。

MSAL for Java を使用するアプリでは、Azure Active Directory (Azure AD) と通信し、その後、AD FS とフェデレーションします。

MSAL for Java が Azure AD に接続されると、Azure AD で管理されているユーザー (管理対象ユーザー) や、AD FS など、別の ID プロバイダーで管理されているユーザー (フェデレーション ユーザー) のサインインが処理されます。 MSAL for Java では、ユーザーがフェデレーションされていることを認識されません。 単に Azure AD と通信します。

この場合に使用する[機関](msal-client-application-configuration.md#authority)は、通常の機関 (機関のホスト名 + テナント、common、または organizations) です。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>フェデレーション ユーザーのトークンを対話形式で取得する

`ConfidentialClientApplication.AcquireToken()` または `PublicClientApplication.AcquireToken()` で `AuthorizationCodeParameters` または `DeviceCodeParameters` を呼び出すときのユーザー エクスペリエンスは通常、次のようになります。

1. ユーザーは自分のアカウント ID を入力します。
2. Azure AD に "組織のページに移動します" と短い時間だけ表示された後、ユーザーは ID プロバイダーのサインイン ページにリダイレクトされます。 サインイン ページは、通常、組織のロゴでカスタマイズされます。

このフェデレーション シナリオでサポートされている AD FS バージョンは次のとおりです。
- Active Directory フェデレーション サービス FS v2
- Active Directory フェデレーション サービス v3 (Windows Server 2012 R2)
- Active Directory フェデレーション サービス v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>ユーザー名とパスワードを使用してトークンを取得する

`ConfidentialClientApplication.AcquireToken()` または `PublicClientApplication.AcquireToken()` と共に `IntegratedWindowsAuthenticationParameters` または `UsernamePasswordParameters` を使用してトークンを取得するとき、MSAL for Java では、ユーザー名に基づいて ID プロバイダーと通信します。 MSAL for Java では、ID プロバイダーから [SAML 1.1 トークン](reference-saml-tokens.md) が取得されます。このトークンはその後、Azure AD に与えられ、JSON Web トークン (JWT) が返されます。

## <a name="next-steps"></a>次のステップ

フェデレーション シナリオについては、「[ホーム領域検出ポリシーを使用して、アプリケーションの Azure Active Directory サインイン動作を構成する](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)」を参照してください。
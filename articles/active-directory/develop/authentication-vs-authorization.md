---
title: 認証と承認 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォーム (v2.0) の認証と承認の基本について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584163"
---
# <a name="authentication-vs-authorization"></a>認証と承認

この記事では、認証と承認を定義し、Microsoft ID プラットフォームを使用して、Web アプリ、Web API、または保護された Web API を呼び出すアプリのユーザーを認証および承認する方法について簡単に説明します。 よく知らない用語がある場合は、[用語集](developer-glossary.md)または基本的な概念を説明する [Microsoft ID プラットフォームの動画](identity-videos.md)をご覧ください。

## <a name="authentication"></a>認証

**認証**は、ユーザーが身元を証明するプロセスです。 認証は AuthN と短縮される場合があります。 Microsoft ID プラットフォームは、認証を処理するための [OpenID Connect](https://openid.net/connect/) プロトコルを実装しています。

## <a name="authorization"></a>承認

**承認**は、認証された利用者に対し、何かを実行する権限を付与する行為です。 アクセスが許可されるデータと、そのデータで実行できる操作を指定します。 承認は AuthZ と短縮される場合があります。 Microsoft ID プラットフォームは、承認を処理するための [OAuth 2.0](https://oauth.net/2/) プロトコルを実装しています。

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームを使用した認証と承認

それぞれ独自のユーザー名とパスワードの情報を保持するアプリを作成する (これにより、複数のアプリにまたがってユーザーを追加または削除する必要があるときに管理上の負担が大きくなります) 代わりに、アプリで、一元化された ID プロバイダーに責任を委任することができます。

Azure Active Directory (Azure AD) は、クラウド内の一元化された ID プロバイダーです。 認証と承認を委任することにより、ユーザーが特定の場所にいることを要求する条件付きアクセス ポリシー、多要素認証の使用のほか、ユーザーが 1 回サインインすると、同じ一元化されたディレクトリを共有するすべての Web アプリに自動的にサインインできるようにするなどのシナリオを実現できます。 この機能は、**シングル サインオン (SSO)** と呼ばれます。

Microsoft ID プラットフォームでは、サービスとしての ID を提供することでアプリケーション開発者による認証と承認を容易にしています。また、コーディングをすばやく開始できるように、OAuth 2.0 や OpenID Connect などの業界標準プロトコルや、さまざまなプラットフォーム向けのオープン ソース ライブラリをサポートしています。 そのため、開発者はすべての Microsoft ID にサインインし、[Microsoft Graph](https://developer.microsoft.com/graph/)、その他の Microsoft API、または開発者が作成した API を呼び出すトークンを取得できます。 詳細については、「[Microsoft ID プラットフォームの進化](about-microsoft-identity-platform.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

認証と承認の基本に関するその他のトピックは、次のとおりです。

* 認証と承認でアクセス トークン、更新トークン、ID トークンがどのように使用されるかについては、[セキュリティ トークン](security-tokens.md)に関する記事を参照してください。
* Microsoft ID プラットフォームと統合できるようにアプリケーションを登録するプロセスについては、[アプリケーション モデル](application-model.md)に関する記事を参照してください。
* Microsoft ID プラットフォームでの Web アプリ、デスクトップ アプリ、モバイル アプリのサインイン フローの詳細については、[アプリのサインイン フロー](app-sign-in-flow.md)に関するページを参照してください。

Microsoft ID プラットフォームが実装するプロトコルの詳細については、次を参照してください。

* OpenID Connect と OAuth 2.0 標準の詳細については、「[Microsoft ID プラットフォームにおける OAuth 2.0 プロトコルと OpenID Connect プロトコル](active-directory-v2-protocols.md)」を参照してください。
* Microsoft ID プラットフォーム でシングル サインオンをサポートする方法の詳細については、「[シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)」を参照してください。

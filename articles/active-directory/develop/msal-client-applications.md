---
title: クライアント アプリケーション (Microsoft Authentication Library) | Azure
description: Microsoft Authentication Library (MSAL) でのパブリック クライアント アプリケーションと機密クライアント アプリケーションについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080403"
---
# <a name="public-client-and-confidential-client-applications"></a>パブリック クライアント アプリケーションと機密クライアント アプリケーション
Microsoft Authentication Library (MSAL) には、2 種類のクライアントが定義されています。パブリック クライアントと機密クライアントです。 その 2 種類のクライアントは、認証サーバーを使用してセキュリティを確保した認証を行い、クライアント資格情報の機密性を維持する能力によって区別されます。  これに対し、Azure AD Authentication ライブラリ (ADAL) には認証コンテキスト (Azure AD への接続) という概念があります。

- **機密クライアント アプリケーション**は、サーバー上で実行されるアプリケーション (Web Apps、Web API、さらにはサービス/デーモン アプリケーション) です。 それらはアクセスが困難であると考えられているため、アプリケーション シークレットを保持することができます。 機密クライアントは、構成時シークレットを保持することができます。 クライアントの各インスタンスに個別の構成 (clientId とシークレットを含む) があります。 これらの値を、エンド ユーザーが抽出することは困難です。 Web アプリは最も一般的な機密クライアントです。 クライアント ID は Web ブラウザーを介して公開されますが、シークレットはバック チャネルでのみ渡され、直接公開されることはありません。

    機密クライアント アプリ: <BR>
    ![Web アプリ](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![デーモン/サービス](media/msal-client-applications/daemon-service.png)

- **パブリック クライアント アプリケーション**は、デバイスまたはデスクトップ コンピューターあるいは Web ブラウザーで実行されるアプリケーションです。 それらにはアプリケーション シークレットを安全に保持するという信頼がないため、ユーザーに代わって Web API にアクセスすることだけが行われます (パブリック クライアント フローのみがサポートされます)。 パブリック クライアントでは構成時シークレットを保持できず、結果としてクライアント シークレットはありません。

    パブリック クライアント アプリケーション: <BR>
    ![デスクトップ アプリ](media/msal-client-applications/desktop-app.png) ![ブラウザーレス API](media/msal-client-applications/browserless-app.png) ![モバイル アプリ](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js では、パブリック クライアント アプリと機密クライアント アプリは分離されません。  MSAL.js ではクライアント アプリをユーザー エージェント ベースのアプリ、つまりクライアント コードが Web ブラウザーなどのユーザー エージェント内で実行されるパブリック クライアントとして表します。  これらのクライアントでは、シークレットは格納されません。ブラウザーのコンテキストが公開されアクセス可能であるからです。

## <a name="comparing-the-client-types"></a>クライアントの種類の比較
パブリック クライアント アプリケーションと機密クライアント アプリケーションには、いくつかの共通点と相違点があります。

- どちらの種類のアプリケーションもユーザー トークン キャッシュを保持しており、トークンを暗黙的に取得できます (トークンが既にトークン キャッシュ内にある場合)。 機密クライアント アプリケーションには、アプリ自体のためのトークン用のアプリ トークン キャッシュもあります。
- どちらでもユーザー アカウントを管理し、ユーザー トークン キャッシュからアカウントを取得することや、識別子からアカウントを取得すること、またはアカウントを削除することができます。
- パブリック クライアント アプリケーションでは 4 つのトークン取得方法 (4 つの認証フロー) があります。一方、機密クライアント アプリケーションでは 3 つです (また、ID プロバイダー承認エンドポイントの URL を計算する方法は 1 つです)。 詳細については、「Scenarios」(シナリオ) と「Acquiring tokens」(トークンの取得) を参照してください。

以前に ADAL を使用したことがある場合はお気付きかもしれませんが、ADAL の認証コンテキストとは反対に、MSAL ではクライアント ID (および名前付きアプリケーション ID またはアプリ ID) がアプリケーションの構築時に一度渡され、トークン取得時にはもう繰り返す必要がありません。 これは、パブリック クライアント アプリケーションと機密クライアント アプリケーションの両方に当てはまります。 機密クライアント アプリケーションのコンストラクターにも、ID プロバイダーと共有するシークレットであるクライアント資格情報が渡されます。

## <a name="next-steps"></a>次の手順
内容は次のとおりです。
- [クライアント アプリケーション構成オプション](msal-client-application-configuration.md)
- [MSAL.NET を使用したクライアント アプリケーションのインスタンス化](msal-net-initializing-client-applications.md)
- [MSAL.js を使用したクライアント アプリケーションのインスタンス化](msal-js-initializing-client-applications.md)

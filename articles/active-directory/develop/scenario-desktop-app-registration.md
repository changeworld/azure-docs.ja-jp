---
title: Web API を呼び出すデスクトップ アプリを登録する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデスクトップ アプリを構築する方法について説明します (アプリの登録)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 263397aa2cd09ba24fa750131b76047801869a65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798937"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Web API を呼び出すデスクトップ アプリ:アプリの登録

この記事では、デスクトップ アプリケーションのアプリ登録の詳細について説明しています。

## <a name="supported-account-types"></a>サポートされているアカウントの種類

デスクトップ アプリケーションでサポートされるアカウントの種類は、利用するエクスペリエンスによって異なります。 この関係により、サポートされるアカウントの種類は、使用するフローによって決まります。

### <a name="audience-for-interactive-token-acquisition"></a>対話型トークン取得の対象ユーザー

デスクトップ アプリケーションで対話型認証を使用する場合は、任意の[アカウントの種類](quickstart-register-app.md)からユーザーをサインインできます。

### <a name="audience-for-desktop-app-silent-flows"></a>デスクトップ アプリのサイレント フローの対象ユーザー

- 統合 Windows 認証またはユーザー名とパスワードを使用するには、たとえば、自分が基幹業務 (LOB) 開発者である場合、アプリケーションでは自分のテナントのユーザーをサインインする必要があります。 また、Azure Active Directory 組織では、ISV のシナリオの場合、アプリケーションでは自分のテナントのユーザーをサインインする必要があります。 これらの認証フローは、Microsoft の個人用アカウントではサポートされていません。
- B2C (business-to-commerce) 機関とポリシーを渡すソーシャル ID を使用してユーザーをサインインする場合は、ユーザー名とパスワードの対話型認証のみを使用できます。

## <a name="redirect-uris"></a>リダイレクト URI

デスクトップ アプリケーションで使用するリダイレクト URI は、使用するフローによって決まります。

Azure portal の **[アプリの登録]** でアプリの[プラットフォーム設定を構成](quickstart-register-app.md#add-a-redirect-uri)して、アプリのリダイレクト URI を指定します。

- 対話型認証を使用するアプリの場合:
  - 埋め込みブラウザーを使用するアプリ: `https://login.microsoftonline.com/common/oauth2/nativeclient`
  - システム ブラウザーを使用するアプリ: `http://localhost`

  > [!IMPORTANT]
  > セキュリティのベスト プラクティスとして、`https://login.microsoftonline.com/common/oauth2/nativeclient` または `http://localhost` をリダイレクト URI として明示的に設定することをお勧めします。 MSAL.NET のような一部の認証ライブラリでは、他のリダイレクト URI が指定されていない場合、推奨されていない既定値の `urn:ietf:wg:oauth:2.0:oob` が使用されます。 この既定値は、次のメジャー リリースで破壊的変更として更新されます。

- macOS 用のネイティブ Objective-C または Swift アプリを構築する場合は、アプリケーションのバンドル識別子に基づいて、`msauth.<your.app.bundle.id>://auth` の形式でリダイレクト URI を登録します。 `<your.app.bundle.id>` をご使用のアプリケーションのバンドル ID に置き換えます。
- Node.js Electron アプリを作成する場合は、認可フローのリダイレクト手順を処理するために、通常の web (https://) リダイレクト URI ではなくカスタム ファイル プロトコルを使用します (`msal://redirect` など)。 カスタム ファイル プロトコル名はすぐに推測できるものにすべきではなく、[ネイティブ アプリに関する Oauth 2.0 仕様](https://tools.ietf.org/html/rfc8252#section-7.1)に記載されている勧告に従う必要があります。
- アプリで統合 Windows 認証またはユーザー名とパスワードのみを使用する場合は、アプリケーションのリダイレクト URI を登録する必要はありません。 これらのフローは、Microsoft ID プラットフォーム v2.0 エンドポイントへのラウンドトリップを実行します。 アプリケーションが特定の URI でコールバックされることはありません。
- [デバイス コード フロー](scenario-desktop-acquire-token.md#device-code-flow)、[統合 Windows 認証](scenario-desktop-acquire-token.md#integrated-windows-authentication)、および[ユーザー名とパスワード](scenario-desktop-acquire-token.md#username-and-password)を、リダイレクト URI を必要としない[デーモン アプリケーション](scenario-daemon-overview.md)で使用されるクライアント資格証明フローを使用した機密クライアント アプリケーションと区別するには、それをパブリック クライアント アプリケーションとして構成します。 この構成を実現するには:

    1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> の **[アプリの登録]** でアプリを選択し、 **[認証]** を選択します。
    1. **[詳細設定]**  >  **[Allow public client flows]\(パブリック クライアント フローを許可する\)**  >  **[Enable the following mobile and desktop flows:]\(次のモバイルとデスクトップのフローを有効にする\)** で **[はい]** を選択します。

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Azure portal の [認証] ウィンドウでパブリック クライアント設定を有効にする":::

## <a name="api-permissions"></a>API のアクセス許可

デスクトップ アプリケーションは、サインインしたユーザーのために API を呼び出します。 委任されたアクセス許可を要求する必要があります。 アプリケーションのアクセス許可を要求することはできません。これは、[デーモン アプリケーション](scenario-daemon-overview.md)でのみ処理されます。

## <a name="next-steps"></a>次の手順

このシナリオの次の記事である[アプリ コードの構成](scenario-desktop-app-configuration.md)に関する記事に進みます。

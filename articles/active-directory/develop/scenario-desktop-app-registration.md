---
title: Web API を呼び出すデスクトップ アプリを登録する - Microsoft ID プラットフォーム | Azure
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
ms.openlocfilehash: 787380dbf6f739d4b88f18a836da2146b06024c3
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443145"
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

- 対話型認証またはデバイス コード フローを使用する場合は、`https://login.microsoftonline.com/common/oauth2/nativeclient` を使用します。 この構成を実現するには、アプリケーションの **[認証]** セクションで対応する URL を選択します。

  > [!IMPORTANT]
  > 現在、MSAL.NET は、Windows で実行されるデスクトップ アプリケーションでは、既定で別のリダイレクト URI を使用しています (`urn:ietf:wg:oauth:2.0:oob`)。 今後、この既定を変更することを予定しているため、`https://login.microsoftonline.com/common/oauth2/nativeclient` を使用することをお勧めします。

- macOS 用のネイティブ Objective-C または Swift アプリを構築する場合は、アプリケーションのバンドル識別子に基づいて、`msauth.<your.app.bundle.id>://auth` の形式でリダイレクト URI を登録します。 `<your.app.bundle.id>` をご使用のアプリケーションのバンドル ID に置き換えます。
- アプリで統合 Windows 認証またはユーザー名とパスワードのみを使用する場合は、アプリケーションのリダイレクト URI を登録する必要はありません。 これらのフローは、Microsoft ID プラットフォーム v2.0 エンドポイントへのラウンドトリップを実行します。 アプリケーションが特定の URI でコールバックされることはありません。
- [デバイス コード フロー](scenario-desktop-acquire-token.md#device-code-flow)、[統合 Windows 認証](scenario-desktop-acquire-token.md#integrated-windows-authentication)、[ユーザー名とパスワード](scenario-desktop-acquire-token.md#username-and-password)を、リダイレクト URI を必要としない[デーモン アプリケーション](scenario-daemon-overview.md)で使用されるクライアント資格証明フローを使用する機密クライアント アプリケーションと区別するには、そのアプリケーションをパブリック クライアント アプリケーションとして構成する必要があります。 この構成を実現するには:

    1. [Azure portal](https://portal.azure.com) の **[アプリの登録]** でアプリを選択し、 **[認証]** を選択します。
    1. **[詳細設定]**  >  **[既定のクライアントの種類]**  >  **[アプリケーションは、パブリック クライアントとして扱います]** で、 **[はい]** を選択します。

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Azure portal の [認証] ウィンドウでパブリック クライアント設定を有効にする":::

## <a name="api-permissions"></a>API のアクセス許可

デスクトップ アプリケーションは、サインインしたユーザーのために API を呼び出します。 委任されたアクセス許可を要求する必要があります。 アプリケーションのアクセス許可を要求することはできません。これは、[デーモン アプリケーション](scenario-daemon-overview.md)でのみ処理されます。

## <a name="next-steps"></a>次の手順

このシナリオの次の記事である[アプリ コードの構成](scenario-desktop-app-configuration.md)に関する記事に進みます。

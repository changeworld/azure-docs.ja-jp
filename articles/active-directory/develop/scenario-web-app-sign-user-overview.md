---
title: ユーザーをサインインさせる Web アプリ (概要) - Microsoft ID プラットフォーム
description: ユーザーをサインインさせる Web アプリを構築する方法 (概要) について説明します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ce534c6eeecba220fd829be829caa679df52055
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833098"
---
# <a name="scenario-web-app-that-signs-in-users"></a>シナリオ: ユーザーをサインインさせる Web アプリ

Microsoft ID プラットフォームを使用してユーザーをサインインさせる Web アプリを構築するために必要なすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用の開始

ユーザーをサインインさせる最初のポータブル (ASP.NET Core) Web アプリを作成する場合は、このクイック スタートに従います。

> [!div class="nextstepaction"]
> [クイック スタート:ユーザーをサインインさせる ASP.NET Core Web アプリ](quickstart-v2-aspnet-core-webapp.md)

ASP.NET を引き続き使用する場合は、次のチュートリアルをお試しください。

> [!div class="nextstepaction"]
> [クイック スタート:ユーザーをサインインさせる ASP.NET Web アプリ](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>概要

お使いの Web アプリに認証を追加して、ユーザーがサインインできるようにします。 認証を追加すると、お使いの Web アプリで、制限されたプロファイル情報にアクセスできるようになります。たとえば、そのユーザーに提供するエクスペリエンスをカスタマイズできます。 Web アプリでは、Web ブラウザーでユーザーを認証します。 このシナリオでは、Web アプリケーションが、ユーザーを Azure AD にサインインさせるよう、ユーザーのブラウザーに指示します。 Azure AD は、ユーザーのブラウザーを介してサインイン応答を返します。この応答のセキュリティ トークンには、ユーザーに関する要求が含まれています。 ユーザーのサインインでは、ミドルウェアの[ライブラリ](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)を使用して簡素化された [Open ID Connect](./v2-protocols-oidc.md) の標準プロトコル自体を利用します。

![Web アプリ サインイン ユーザー](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

第 2 フェーズとして、お使いのアプリで、サインインしたユーザーの代わりに Web API を呼び出すこともできます。 この第 2 フェーズは別のシナリオであり、[Web アプリで Web API を呼び出す](scenario-web-app-call-api-overview.md)に関するページでご確認いただけます

> [!NOTE]
> Web アプリにサインインを追加することは、Web アプリの保護と、**ミドルウェア**のライブラリが行うユーザー トークンの検証に関連します。 このシナリオでは、保護された API を呼び出すためのトークンを取得することに関連する Microsoft 認証ライブラリ (MSAL) はまだ必要ありません。 認証ライブラリは、Web アプリで Web API を呼び出す必要がある場合のみ、フォローアップ シナリオで導入されます。

## <a name="specifics"></a>詳細

- アプリケーションの登録時、1 つまたは複数 (お使いのアプリを複数の場所にデプロイする場合) の応答 URI を提供する必要があります。 一部のケース (ASP.NET/ASP.NET Core) では、IDToken を有効にする必要があります。 最終的に、お使いのアプリケーションがユーザーのサインアウトに反応できるように、サインアウト URI を設定する必要が生じることがあります。
- お使いのアプリケーションのコードでは、お使いの Web アプリでサインインを委任する権限を提供する必要があります。 トークンの検証をカスタマイズする必要が生じる場合があります (ISV シナリオでは特に)。
- Web アプリケーションでは、さまざまなアカウントの種類をサポートしています。 詳細については、「[サポートされているアカウントの種類](v2-supported-account-types.md)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリの登録](scenario-web-app-sign-user-app-registration.md)

---
title: シングルページ アプリケーション (アプリの登録) - Microsoft ID プラットフォーム
description: シングルページ アプリケーション を構築する方法 (アプリの登録) について説明します
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080095"
---
# <a name="single-page-application---app-registration"></a>シングルページ アプリケーション - アプリの登録

このページでは、シングルページ アプリケーション (SPA) におけるアプリの登録の特性について説明します。

「[Microsoft ID プラットフォームに新しいアプリケーションを登録する](quickstart-register-app.md)」の手順に従って、お使いのアプリケーションでサポートされているアカウントを選択します。 SPA のシナリオでは、組織のアカウント、または任意の組織もしくは個人の Microsoft アカウントでの認証をサポートできます。

次に、シングルページ アプリケーションに適用されるアプリケーションの登録の特定の側面について説明します。

## <a name="register-a-redirect-uri"></a>リダイレクト URI の登録

暗黙的フローでは、Web ブラウザーで実行されているシングルページ アプリケーションにリダイレクトでトークンを送信します。 そのため、お使いのアプリケーションでトークンを受信できるリダイレクト URI を登録することが重要な要件になります。 リダイレクト URI が、お使いのアプリケーションの URI と完全に一致することを確認してください。

[Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) で、登録されたアプリケーションに移動し、アプリケーションの「**認証**」ページで **Web** プラットフォームを選択して、"**リダイレクト URI**" フィールドにアプリケーションのリダイレクト URI の値を入力します。

## <a name="enable-the-implicit-flow"></a>暗黙的フローの有効化

同じ「**認証**」ページ上の **[詳細設定]** で、 **[暗黙的な許可]** も有効にする必要があります。 お使いのアプリケーションでユーザーのサインインの実行と ID トークンの取得のみを行う場合は、 **[ID トークン]** チェック ボックスを有効にするだけで十分です。

お使いのアプリケーションで、API を呼び出すためにアクセス トークンも取得する必要がある場合は、必ず **[アクセス トークン]** チェック ボックスも有効にしてください。 詳細については、「[ID トークン](./id-tokens.md)」と「[アクセス トークン](./access-tokens.md)」を参照してください。

## <a name="api-permissions"></a>API のアクセス許可

シングルページ アプリケーションでは、サインインしたユーザーの代わりに API を呼び出すことができます。 委任されたアクセス許可を要求する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-spa-app-configuration.md)

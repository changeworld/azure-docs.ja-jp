---
title: シングルページ アプリケーション (運用環境への移行) - Microsoft ID プラットフォーム
description: シングルページ アプリケーションを構築する方法について説明します (運用環境への移行)
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
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080119"
---
# <a name="single-page-application---move-to-production"></a>シングルページ アプリケーション - 運用環境への移行

Web API を呼び出すトークンの取得方法が分かったので、次に運用環境に移行する方法について説明します。

## <a name="improve-your-app"></a>アプリの改善

アプリを運用環境に移行するために必要な手順に従います。

- アプリケーションで[ログを有効にします](msal-logging.md)。

## <a name="test-your-integration"></a>統合のテスト

- [Microsoft ID プラットフォームの統合チェックリスト](identity-platform-integration-checklist.md)に従って、統合をテストします。

## <a name="next-steps"></a>次の手順

その他のいくつかのサンプルとチュートリアルを次に示します。

- ユーザーをサインインさせ、MSAL.js を使用して MS Graph API を呼び出すためのアクセス トークンを取得する方法のコードを説明するクイックスタート サンプルの詳細について

    > [!div class="nextstepaction"]
    > [JavaScript SPA チュートリアル](./tutorial-v2-javascript-spa.md)

- MSAL.js を使用して独自のバックエンド Web API のトークンを取得する方法を示すサンプル

     > [!div class="nextstepaction"]
     > [ASP.NET バックエンドを使用する SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- MSAL.js を使用して、Azure AD B2C に登録されているアプリにユーザーをサインインさせる方法を示すサンプル

    > [!div class="nextstepaction"]
    > [Azure AD B2C を使用する SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

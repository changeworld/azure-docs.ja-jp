---
title: ユーザーをサインインさせる Web アプリを運用環境に移行する - Microsoft ID プラットフォーム | Azure
description: ユーザーをサインインさせる Web アプリをビルドする方法について説明します (運用環境への移行)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768100"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>ユーザーをサインインさせる Web アプリ:運用環境に移行する

Web API を呼び出すトークンの取得方法がわかったので、次にそれを運用環境に移行する方法について説明します。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

### <a name="same-site"></a>SameSite

新しいバージョンの Chrome ブラウザーで起きる可能性がある問題について理解していることを確認します。

> [!div class="nextstepaction"]
> [Chrome ブラウザーにおける SameSite Cookie の変更を処理する方法](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Web API を呼び出すシナリオ

Web アプリはユーザーをサインインさせると、サインインしているユーザーの代わりに Web API を呼び出すことができます。 Web アプリから Web API を呼び出すことは、次のシナリオの目標です。

> [!div class="nextstepaction"]
> [Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>詳細情報: ASP.NET Core Web アプリのチュートリアル

この ASP.NET Core チュートリアルでは、ユーザーをサインインさせるための他の方法について説明しています。 

> [!div class="nextstepaction"]
> [開発者向け Microsoft ID プラットフォームを利用することで、Web アプリでユーザーのサインインを処理したり、API を呼び出したりできます。](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

このプログレッシブ チュートリアルには、以下のアカウントでのサインインを追加する方法など、Web アプリの運用環境対応コードが含まれています。

- 自分の組織
- 複数の組織
- 職場または学校アカウント、または個人用 Microsoft アカウント
- [Azure AD B2C](https://aka.ms/aadb2c)
- 各国のクラウド

## <a name="sample-code-java-web-app"></a>サンプル コード:Java Web アプリ

GitHub のこのサンプルで Java Web アプリの詳細を学習してください。 

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームを使用してユーザーをサインインさせ、Microsoft Graph を呼び出す Java Web アプリケーション](https://github.com/Azure-Samples/ms-identity-java-webapp)

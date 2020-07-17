---
title: ユーザーをサインインさせる Web アプリを運用環境に移行する - Microsoft ID プラットフォーム | Azure
description: ユーザーをサインインさせる Web アプリをビルドする方法について説明します (運用環境への移行)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181632"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>ユーザーをサインインさせる Web アプリ:運用環境に移行する

Web API を呼び出すトークンの取得方法がわかったので、次にそれを運用環境に移行する方法について説明します。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

### <a name="troubleshooting"></a>トラブルシューティング

> [!NOTE]
> ユーザーが初めて Web アプリケーションにサインインするときは、同意する必要があります。 しかし、組織によっては、次のようなメッセージがユーザーに表示される場合があります。
>
> *AppName では、組織内のリソースにアクセスするために、管理者しか付与できないアクセス許可が必要になります。使用するには、まず、このアプリへのアクセス許可の付与を管理者に依頼してください。*
>
> これは、テナント管理者によって、ユーザーが同意する機能が**無効化されている**ためです。 その場合は、アプリケーションから要求されるスコープに対して管理者の同意を得るために、テナント管理者に連絡する必要があります。

### <a name="same-site"></a>SameSite

新しいバージョンの Chrome ブラウザーで起きる可能性がある問題について理解していることを確認します。

> [!div class="nextstepaction"]
> [Chrome ブラウザーにおける SameSite Cookie の変更を処理する方法](howto-handle-samesite-cookie-changes-chrome-browser.md)

Microsoft.Identity.Web NuGet パッケージでは、最も一般的な SameSite 問題が処理されます。

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

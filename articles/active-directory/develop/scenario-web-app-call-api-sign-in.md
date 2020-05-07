---
title: サインアウト時にトークン キャッシュからアカウントを削除する - Microsoft ID プラットフォーム | Azure
description: サインアウト時にトークン キャッシュからアカウントを削除する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181649"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Web API を呼び出す Web アプリ:グローバル サインアウト時にトークン キャッシュからアカウントを削除する

[ユーザーをサインインさせる Web アプリ:サインインとサインアウト](scenario-web-app-sign-user-sign-in.md) で、Web アプリにサインインを追加する方法を学びました。

サインアウトは、Web API を呼び出す Web アプリでは異なります。 ユーザーがアプリケーションまたは任意のアプリケーションからサインアウトするときに、トークン キャッシュからそのユーザーに関連付けられているトークンを削除する必要があります。

## <a name="intercept-the-callback-after-single-sign-out"></a>シングル サインアウト後にコールバックをインターセプトする

サインアウトしたアカウントに関連付けられているトークン キャッシュ エントリをクリアするには、アプリケーションで `logout` 後のイベントを受け取ることができます。 Web アプリは、各ユーザーのアクセス トークンをトークン キャッシュに格納します。 Web アプリケーションは、`logout` 後のコールバックをインターセプトすることにより、キャッシュからユーザーを削除できます。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web では、サインアウトの実装が自動処理されます。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません。

# <a name="java"></a>[Java](#tab/java)

Java のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません。

# <a name="python"></a>[Python](#tab/python)

Python のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません。

---

## <a name="next-steps"></a>次のステップ

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---

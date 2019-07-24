---
title: Web API を呼び出す Web アプリ (サインイン) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリを構築する方法について説明します (サインイン)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080059"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Web API を呼び出す Web アプリ - サインイン

Web アプリにサインインを追加する方法については既に学習しました。 これについては、「[ユーザーをサインインさせる Web アプリ - サインインの追加](scenario-web-app-sign-user-sign-in.md)」でご説明しました。

ここで異なるのは、ユーザーがこのアプリケーションから、または任意のアプリケーションからサインアウトしたときに、トークン キャッシュ (ユーザーに関連付けられているトークン) から削除することです。

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>サインアウト後にコールバックをインターセプトする - シングル サインアウト

アプリケーションは、`logout` 後のイベントをインターセプトして、たとえば、サインアウトしたアカウントに関連付けられたトークン キャッシュのエントリをクリアすることができます。このチュートリアルの第 2 部 (Web API を呼び出す Web アプリについて) で、Web アプリがユーザーのアクセス トークンをキャッシュに格納することを確認します。 `logout` 後のコールバックをインターセプトすることにより、Web アプリケーションはトークン キャッシュからユーザーを削除することができます。 このメカニズムについては、[StartupHelper.cs L137-143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143) の `AddMsal()` メソッドで説明しています。

アプリケーション用に登録した **Logout Url** により、シングル サインアウトを実装することができます。Microsoft ID プラットフォームの `logout` エンドポイントは、アプリケーションに登録されている **Logout URL** を呼び出します。 この呼び出しは、サインアウトがユーザーの Web アプリから開始された場合、または別の Web アプリやブラウザーから開始された場合に行われます。 詳細については、概念に関するドキュメントの「[シングル サインアウト](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out)」を参照してください。

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Webアプリのトークンの取得](scenario-web-app-call-api-acquire-token.md)

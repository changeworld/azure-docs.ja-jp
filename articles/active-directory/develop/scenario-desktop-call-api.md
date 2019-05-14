---
title: Web API を呼び出すデスクトップ アプリ (Web API の呼び出し) - Microsoft ID プラットフォーム
description: Web API を呼び出すデスクトップアプリの構築方法について説明します(Web API の呼び出し)
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
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080241"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Web API を呼び出すデスクトップ アプリ - Web API の呼び出し

トークンを取得すると、保護された Web API を呼び出せます。

## <a name="calling-a-web-api-from-net"></a>.NET からの Web API の呼び出し

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>複数の API の呼び出し - 増分同意と条件付きアクセス

同じユーザーに対して複数の API を呼び出す必要がある場合には、最初の API のトークンを取得すると、`AcquireTokenSilent` を呼び出すだけで、ほとんどの場合、他の API のトークンをサイレントに取得できます。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

対話が必要になるのは、以下のようなケースです。

- ユーザーが最初の API については同意したが、より多くのスコープ (増分同意) について同意する必要が生じた。
- 最初の API は多要素認証を必要としなかったが、次の API は多要素認証を必要とする。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-desktop-production.md)

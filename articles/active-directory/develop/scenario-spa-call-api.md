---
title: シングルページ アプリケーション (Web API の呼び出し) - Microsoft ID プラットフォーム
description: シングルページ アプリケーションを構築する方法について説明します (Web API の呼び出し)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545611"
---
# <a name="single-page-application---call-a-web-api"></a>シングルページ アプリケーション - Web API の呼び出し

Web API を呼び出す前に、`acquireTokenSilent` メソッドを呼び出してアクセス トークンを取得または更新することをお勧めします。 トークンを取得すると、保護された Web API を呼び出せます。

## <a name="call-a-web-api"></a>Web API を呼び出す

### <a name="javascript"></a>JavaScript

取得したアクセス トークンを HTTP 要求のベアラーとして使用して、Microsoft Graph API などの任意の Web API を呼び出します。 例:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

### <a name="angular"></a>Angular

[トークンの取得セクション](scenario-spa-acquire-token.md)で説明されているように、MSAL Angular ラッパーは HTTP インターセプターを利用して自動的にアクセス トークンをサイレントに取得し、API への HTTP 要求に添付します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-spa-production.md)

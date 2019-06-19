---
title: 認証要求内でカスタムの状態を渡す (JavaScript 用 Microsoft Authentication Library) | Azure
description: JavaScript (MSAL.js) 用 Microsoft 認証ライブラリを使用して認証要求内でカスタムの状態パラメーターの値を渡す方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420409"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>MSAL.js を使用して認証要求内でカスタムの状態を渡す
OAuth 2.0 で定義されているように、"*状態*" パラメーターは認証要求に含まれ、クロスサイト リクエスト フォージェリ攻撃を防ぐためにもトークン応答で返されます。 既定では、JavaScript (MSAL.js) 用 Microsoft Authentication Library により、ランダムに生成された一意の "*状態*" パラメーター値が認証要求で渡されます。

状態パラメーターは、リダイレクト前に、アプリの状態情報をエンコードするために使用することもできます。 アプリ内のユーザーの状態 (ユーザーがいたページまたはビューなど) を、このパラメーターへの入力として渡すことができます。 MSAL.js ライブラリを使用すると、カスタムの状態を `Request` オブジェクトの状態パラメーターとして渡すことができます。

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
};
```

例:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

渡された状態は、要求の送信時に、MSAL.js によって設定される一意の GUID に追加されます。 応答が返されると、MSAL.js により状態が一致しているかどうか確認され、`Response` オブジェクトでカスタムの渡された状態が `accountState` として返されます。

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

詳細については、MSAL.js を使用した[シングル ページ アプリケーション (SPA) のビルド](scenario-spa-overview.md)に関する記事をご覧ください。
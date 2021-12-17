---
title: シングルページ アプリの構成 | Azure
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーション (アプリのコード構成) を構築する方法について説明します
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 7654178d9f3393fd7ff5a0a79da5d1f71c741bbf
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222176"
---
# <a name="single-page-application-code-configuration"></a>シングルページ アプリケーション：コード構成

ご利用のシングルページ アプリケーション (SPA) のコードを構成する方法について説明します。

## <a name="microsoft-libraries-supporting-single-page-apps"></a>シングルページ アプリをサポートする Microsoft ライブラリ

次の Microsoft ライブラリはシングルページ アプリをサポートしています。

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>アプリケーションのコード構成

MSAL ライブラリでは、ライブラリの初期化中にアプリケーションの登録情報が構成として渡されます。

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
import * as Msal from "@azure/msal-browser"; // if using CDN, 'Msal' will be available in global scope

// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create PublicClientApplication instance
const publicClientApplication = new Msal.PublicClientApplication(config);
```

構成可能なオプションの詳細については、[MSAL.js を使用したアプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript
import * as Msal from "msal"; // if using CDN, 'Msal' will be available in global scope

// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new Msal.UserAgentApplication(config);
```

構成可能なオプションの詳細については、[MSAL.js を使用したアプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
import { MsalModule } from '@azure/msal-angular';
import { PublicClientApplication } from '@azure/msal-browser';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            }
        }), null, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})
export class AppModule { }
```

# <a name="react"></a>[React](#tab/react)

```javascript
import { PublicClientApplication } from "@azure/msal-browser";
import { MsalProvider } from "@azure/msal-react";

// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create PublicClientApplication instance
const publicClientApplication = new PublicClientApplication(config);

// Wrap your app component tree in the MsalProvider component
ReactDOM.render(
    <React.StrictMode>
        <MsalProvider instance={publicClientApplication}>
            <App />
        </ MsalProvider>
    </React.StrictMode>,
    document.getElementById('root')
);
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[サインインとサインアウト](scenario-spa-sign-in.md)に関する記事に進みます。

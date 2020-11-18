---
title: シングルページ アプリを構成する - Microsoft ID プラットフォーム | Azure
description: シングルページ アプリケーション (アプリのコード構成) を構築する方法について説明します
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 30a9b710ffbf98ebc523217a3b8a7fd9a2640c49
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443043"
---
# <a name="single-page-application-code-configuration"></a>シングルページ アプリケーション：コード構成

ご利用のシングルページ アプリケーション (SPA) のコードを構成する方法について説明します。

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>SPA 用の MSAL ライブラリとサポートされている認証フロー

Microsoft ID プラットフォームには、次の JavaScript 用 Microsoft Authentication Library (MSAL.js) が用意されており、業界で推奨されているセキュリティ プラクティスを使用して暗黙的なフローと PKCE を使用した認可コード フローがサポートされます。

| MSAL ライブラリ | Flow | 説明 |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | 認可コード フロー (PKCE) | JavaScript または SPA フレームワーク (Angular、Vue.js、React.js) を使用して構築されたクライアント側 Web アプリで使用するためのプレーンな JavaScript ライブラリ |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | 暗黙的なフロー | JavaScript または SPA フレームワーク (Angular、Vue.js、React.js) を使用して構築されたクライアント側 Web アプリで使用するためのプレーンな JavaScript ライブラリ |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 暗黙的なフロー | Angular フレームワークで構築されたシングル ページ アプリでの使用を簡素化するためのコア MSAL.js ライブラリのラッパー。 |

## <a name="application-code-configuration"></a>アプリケーションのコード構成

MSAL ライブラリでは、ライブラリの初期化中にアプリケーションの登録情報が構成として渡されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

構成可能なオプションの詳細については、[MSAL.js を使用したアプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

# <a name="angular"></a>[Angular](#tab/angular)

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

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[サインインとサインアウト](scenario-spa-sign-in.md)に関する記事に進みます。

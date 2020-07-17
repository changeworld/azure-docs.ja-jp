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
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882337"
---
# <a name="single-page-application-code-configuration"></a>シングルページ アプリケーション：コード構成

ご利用のシングルページ アプリケーション (SPA) のコードを構成する方法について説明します。

## <a name="msal-libraries-that-support-implicit-flow"></a>暗黙的なフローをサポートする MSAL ライブラリ

Microsoft ID プラットフォームは、次の Microsoft Authentication Library (MSAL) ライブラリを提供し、業界で推奨されているセキュリティ プラクティスを使用して暗黙的なフローをサポートします。

| MSAL ライブラリ | 説明 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | JavaScript または SPA フレームワーク (Angular、Vue.js、React.js) を使用して構築されたクライアント側 Web アプリで使用するためのプレーンな JavaScript ライブラリ |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Angular フレームワークで構築されたシングル ページ アプリでの使用を簡素化するためのコア MSAL.js ライブラリのラッパー。 |

## <a name="application-code-configuration"></a>アプリケーションのコード構成

MSAL ライブラリでは、ライブラリの初期化中にアプリケーションの登録情報が構成として渡されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

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

> [!div class="nextstepaction"]
> [サインインとサインアウト](scenario-spa-sign-in.md)

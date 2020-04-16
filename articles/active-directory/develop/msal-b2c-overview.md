---
title: Azure Active Directory B2CLearn で MSAL を使用する | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用すると、アプリケーションは Azure AD B2C と連携し、セキュリティで保護された Web API を呼び出すためのトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、他の開発者の Web API、または自分の Web API が可能です。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875964"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>JavaScript 用 Microsoft Authentication Library を使用して Azure Active Directory B2C と連携する

[JavaScript 用 Microsoft Authentication Library (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) を使用すると、JavaScript 開発者は [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) を使用して、ソーシャルおよびローカル ID でユーザーを認証できます。 ID 管理サービスとして Azure AD B2C を使用すると、顧客がアプリケーションを使用するときにサインアップ、サインイン、自分のプロファイルの管理を行う方法をカスタマイズして制御できます。

Azure AD B2C ではまた、顧客にシームレスなエクスペリエンスを提供するために、認証プロセス中のアプリケーションの UI をブランド化してカスタマイズすることもできます。

この記事では、MSAL.js を使用して Azure AD B2C と連携する方法を示すと共に、注意する必要がある重要な点を要約しています。 詳細な説明とチュートリアルについては、[Azure AD B2C のドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/overview)を参照してください。

## <a name="prerequisites"></a>前提条件

独自の [Azure AD B2C テナント](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)をまだ作成していない場合は、まずここでそれを作成してください (既に存在する場合は、既存の Azure AD B2C テナントを使用することもできます)。

このデモには、次の 2 つの部分が含まれています。

- Web API を保護する方法。
- *その* Web API を認証して呼び出すためのシングルページ アプリケーションを登録する方法。

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> 現時点で、MSAL.js for Node はまだ開発中です ([ロードマップ](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)を参照)。 その間は、Microsoft によって開発およびサポートされている Node.js 用の認証ライブラリである [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) を使用することをお勧めします。

次の手順は、**Web API** が Azure AD B2C を使用して自身を保護し、選択されたスコープをクライアント アプリケーションに公開する方法を示しています。

### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録

Azure AD B2C を使用して Web API を保護するには、まずそれを登録する必要があります。 詳細な手順については、[Register your application (アプリケーションの登録)](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) に関する記事をご覧ください。

### <a name="step-2-download-the-sample-application"></a>手順 2:サンプル アプリケーションのダウンロード

サンプルを ZIP ファイルとしてダウンロードするか、GitHub から複製します。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>手順 3:認証を構成する。

1. サンプルの `config.js` ファイルを開きます。

2. 以前にアプリケーションの登録中に取得したアプリケーション資格情報を使用してサンプルを構成します。 次のコード行を、その各値をクライアント ID、ホスト、テナント ID、ポリシー名の名前に置き換えることによって変更します。

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

詳細については、この [Node.js B2C Web API のサンプル](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)を確認してください。

---

## <a name="javascript-spa"></a>JavaScript SPA

次の手順は、**シングルページ アプリケーション**が Azure AD B2C を使用して、サインアップ、サインイン、保護された Web API の呼び出しを行う方法を示しています。

### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録

認証を実装するには、まず、アプリケーションを登録する必要があります。 詳細な手順については、[Register your application (アプリケーションの登録)](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) に関する記事をご覧ください。

### <a name="step-2-download-the-sample-application"></a>手順 2:サンプル アプリケーションのダウンロード

サンプルを ZIP ファイルとしてダウンロードするか、GitHub から複製します。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>手順 3:認証を構成する。

アプリケーションを構成する場合の関心ポイントとして、次の 2 つがあります。

- API エンドポイントと公開されるスコープを構成する
- 認証パラメーターとトークン スコープを構成する

1. サンプルの `apiConfig.js` ファイルを開きます。

2. 以前に Web API の登録中に取得したパラメーターを使用してサンプルを構成します。 次のコード行を、その各値を Web API と公開されるスコープのアドレスに置き換えることによって変更します。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello" 
    };
   ```

3. サンプルの `authConfig.js` ファイルを開きます。

4. 以前にシングルページ アプリケーションの登録中に取得したパラメーターを使用してサンプルを構成します。 次のコード行を、その各値をクライアント ID、権限メタデータ、トークン要求スコープに置き換えることによって変更します。

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

詳細については、この [JavaScript B2C シングルページ アプリケーションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)を確認してください。

---

## <a name="next-steps"></a>次のステップ

各項目の詳細情報
- [ユーザー フロー](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [カスタム ポリシー](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX のカスタマイズ](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)

---
title: Azure AD v2 JavaScript クイック スタート | Microsoft Docs
description: JavaScript アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2.0 エンドポイントから呼び出す方法を説明します
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 1b884571707aab71e8a8d124ba68f938e5a63a43
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063746"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>クイック スタート: ユーザーをサインインし、JavaScript アプリケーションからアクセス トークンを取得する

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

このクイック スタートでは、JavaScript シングル ページ アプリケーション (SPA) がどのようにして個人アカウントや職場および学校アカウントにサインインし、Microsoft Graph API や任意の Web API を呼び出すためのアクセス トークンを取得することができるかを示すサンプル コードの使用方法について説明します。

![このクイック スタートで生成されたサンプル アプリの動作](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>アプリケーションの登録とクイック スタート アプリのダウンロード
>
> ### <a name="step-1-register-your-application"></a>手順 1: アプリケーションの登録
>
> 1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します。
> 1. **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。
> 1. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオフになっていることを確認し、**[作成]** を選択します。
> 1. **[プラットフォームの追加]** をクリックし、**[Web]** を選択します。
> 1. **[暗黙的フローを許可する]** が "*オンになっている*" ことを確認します。
> 1. **[リダイレクト URL]** で、`http://localhost:30662/` を追加入力します。
> 1. **[Save]** をクリックします。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>手順 1: Azure portal でのアプリケーションの構成
> このクイック スタートのコード サンプルを動作させるには、リダイレクト URI として `http://localhost:30662/` を追加し、**[暗黙の付与]** を有効にします。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています

#### <a name="step-2-download-the-project"></a>手順 2: プロジェクトのダウンロード

次のオプションのうち、ご自身の開発環境に適したものを選択します。
* [コア プロジェクト ファイルのダウンロード - Node.js などの Web サーバー向け](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Visual Studio プロジェクトのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

ローカル フォルダー (例: **C:\Azure-Samples**) に zip ファイルを解凍します。

#### <a name="step-3-configure-your-javascript-app"></a>手順 3: ご自身の JavaScript アプリの構成

> [!div renderon="docs"]
> `index.html` を編集し、`applicationConfig` の下の `Enter_the_Application_Id_here` を今登録したアプリのアプリケーション ID に置き換えます。

> [!div class="sxs-lookup" renderon="portal"]
> `index.html` を編集し、`applicationConfig` を次に置き換えます。

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!NOTE]
>[Node.js](https://nodejs.org/en/download/) を使用する場合、*server.js* ファイルは、サーバーがポート 30662 のリッスンを開始するように構成されます。
> [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) を使用する場合、サンプル コードの *.csproj* ファイルは、サーバーがポート 30662 のリッスンを開始するように構成されます。
>

#### <a name="step-4-run-the-project"></a>手順 4: プロジェクトの実行

Node.js を使用している場合は、コマンド ラインでプロジェクトのディレクトリから次を実行して、サーバーを起動します。
 ```batch
 npm install
 node server.js
 ```
Web ブラウザーを開き、`http://localhost:30662/` に移動します。 **[サインイン]** ボタンをクリックしてサインインを開始してから、Microsoft Graph API を呼び出します。

Visual Studio を使用している場合は、プロジェクト ソリューションを選択したことを確認し、**F5** キーを押してプロジェクトを実行します。

## <a name="more-information"></a>詳細情報

### <a name="msaljs"></a>*msal.js*

MSAL はユーザーをサインインし、Microsoft Azure Active Directory (Azure AD) によって保護されている API にアクセスするトークンを要求するときに使用するライブラリです。 クイック スタートの *index.html* にはライブラリへの参照が含まれています。

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

また、Node がインストールされている場合は、npm を介してダウンロードできます。

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL の初期化

クイック スタートのコードには、ライブラリを初期化する方法も示されています。

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |Azure portal に登録されているアプリケーションのアプリケーション ID|
> |`authority`    |機関の URL です。 *null* を渡すと、既定の機関が `https://login.microsoftonline.com/common` に設定されます。 お使いのアプリがシングル テナント (1 つのディレクトリのアカウントのみが対象) の場合、この値は `https://login.microsoftonline.com/<tenant name or ID>` に設定します|
> |`tokenReceivedCallback`| 認証後に呼び出されたコールバック メソッドがアプリにリダイレクトされます。 ここで `acquireTokenRedirectCallBack` が渡されます。 loginPopup を使用している場合、これは null です。|
> |`options`  |省略可能なパラメーターのコレクション。 この場合、`storeAuthStateInCookie` と `cacheLocation` はオプションの構成です。 オプションの詳細については、[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) を参照してください。 |

### <a name="sign-in-users"></a>ユーザーのサインイン

次のコード スニペットは、ユーザーをサインインする方法を示しています。

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (省略可能) ログイン時にユーザーの同意が要求されるスコープが含まれています (例: Microsoft Graph の場合は `[ "user.read" ]`、カスタム Web API の場合は `[ "<Application ID URL>/scope" ]` (つまり、`api://<Application ID>/access_as_user`))。 ここで `applicationConfig.graphScopes` が渡されます。 |

> [!TIP]
> あるいは、`loginRedirect` メソッドを使用して、ポップアップ ウィンドウの代わりに現在のページをサインイン ページにリダイレクトすることもできます。


### <a name="request-tokens"></a>トークンの要求

MSAL では `acquireTokenRedirect`、`acquireTokenPopup`、および `acquireTokenSilent` の 3 つのメソッドを使用して、トークンを取得します。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginRedirect` または `loginPopup` メソッドが実行され、その後の呼び出しでは、保護されたリソースにアクセスするトークンを取得するために `acquireTokenSilent` メソッドが通常使用されます。 トークンを要求または更新するための呼び出しは自動的に行われます。

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | API へのアクセス トークンで返されるように要求されるスコープが含まれています (例: Microsoft Graph の場合は `[ "user.read" ]`、カスタム Web API の場合は `[ "<Application ID URL>/scope" ]` (つまり、`api://<Application ID>/access_as_user`))。 ここで `applicationConfig.graphScopes` が渡されます。|

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

 ユーザーに Azure AD v2.0 エンドポイントとのやり取りを強制しなければならない場合があります。 例: 
* パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある
* お使いのアプリケーションが、ユーザーによる同意が必要な追加のリソース スコープへのアクセスを要求している
* 2 要素認証が必須である

アプリケーションのほとんどについて、通常は、最初に `acquireTokenSilent` を呼び出し、例外をキャッチしてから、`acquireTokenRedirect` (または `acquireTokenPopup`) を呼び出して、対話型要求を開始するパターンをお勧めします。

`acquireTokenPopup(scope)` を呼び出すと、サインインするためのポップアップ ウィンドウが表示されます (または、`acquireTokenRedirect(scope)` を呼び出すと、ユーザーが Azure AD v2.0 エンドポイントにリダイレクトされます)。ここで、ユーザーは自分の資格情報を確認するか、必要なリソースへの同意を示すか、または 2 要素認証を完了するかのいずれかの操作を行う必要があります。

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> このクイック スタートでは、使用されるブラウザーが Internet Explorer である場合、Internet Explorer ブラウザーによるポップアップ ウィンドウの処理に関連した[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)のために `loginRedirect` および `acquireTokenRedirect` メソッドを使用します。

## <a name="next-steps"></a>次の手順

このクイック スタート用のアプリケーションをビルドする方法が詳しく説明されているステップ バイ ステップ ガイドについては、以下の JavaScript チュートリアルをお試しください。

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>このクイック スタート用のアプリケーションを作成する手順を確認する

> [!div class="nextstepaction"]
> [Graph API 呼び出しチュートリアル](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>MSAL リポジトリでドキュメント、FAQ、問題などを確認する

> [!div class="nextstepaction"]
> [msal.js GitHub のリポジトリ](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

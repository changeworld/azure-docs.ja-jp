---
title: Microsoft ID プラットフォーム JavaScript のクイックスタート | Azure
description: JavaScript アプリケーションで、アクセス トークンを必要とする API を Microsoft ID プラットフォーム エンドポイントから呼び出す方法を説明します。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9deaf610696f676610f589168426ac24be692c99
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823515"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>クイック スタート:ユーザーをサインインし、JavaScript のシングルページ アプリケーション (SPA) からアクセス トークンを取得する

このクイック スタートでは、JavaScript シングル ページ アプリケーション (SPA) が個人用アカウントや職場および学校アカウントをサインインし、Microsoft Graph API や任意の Web API を呼び出すためのアクセス トークンを取得する方法を示すサンプル コードの使用方法について説明します。

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、次の設定が必要になります。
* node.js server でプロジェクトを実行するために:
    *  [Node.js](https://nodejs.org/en/download/)
    * プロジェクト ファイルを編集するために [Visual Studio Code](https://code.visualstudio.com/download) をインストールする
* プロジェクトを Visual Studio ソリューションとして実行するために、[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールする。

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>クイック スタート アプリケーションを登録してダウンロードする
> クイック スタート アプリケーションを開始する方法としては、次の 2 つの選択肢があります。
> * [簡易] [選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選択肢 2: アプリケーションを登録し、コード サンプルを手動で構成する](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
> 1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
> 1. 新しい [Azure portal の [アプリの登録 (プレビュー)]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ウィンドウに移動します。
> 1. アプリケーションの名前を入力して **[登録]** をクリックします。
> 1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選択肢 2: アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-your-application"></a>手順 1: アプリケーションの登録
>
> 1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
> 1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
> 1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
> 1. **[新規登録]** を選択します。
> 1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
> 1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
> 1. **[リダイレクト URI]** セクションで **[Web]** プラットフォームを選択し、値を `http://localhost:30662/` に設定します。
> 1. 終了したら、 **[登録]** を選択します。  アプリの **[概要]** ページで、 **[Application (client) ID]\(アプリケーション (クライアント) ID\)** の値を書き留めます。
> 1. このクイック スタートでは、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。 登録済みのアプリケーションの左側のナビゲーション ウィンドウで、 **[認証]** を選択します。
> 1. **[詳細設定]** の **[暗黙的な許可]** で、 **[ID トークン]** と **[アクセス トークン]** の両方のチェック ボックスをオンにします。 このアプリではユーザーをサインインし、API を呼び出す必要があるため、ID トークンとアクセス トークンが必要になります。
> 1. **[保存]** を選択します。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1: Azure portal でのアプリケーションの構成
> このクイック スタートのコード サンプルを動作させるには、リダイレクト URI として `http://localhost:30662/` を追加し、 **[暗黙の付与]** を有効にします。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

次のオプションのうち、ご自身の開発環境に適したものを選択します。
* [コア プロジェクト ファイルのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip): Node.js を使用する Web サーバーでの実行用。 ファイルを開くには、[Visual Studio Code](https://code.visualstudio.com/) などのエディターを使用します。

* (省略可能) [Visual Studio プロジェクトのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip): IIS サーバーでの実行用。 ローカル フォルダー (例: **C:\Azure-Samples**) に ZIP ファイルを解凍します。



#### <a name="step-3-configure-your-javascript-app"></a>手順 3:JavaScript アプリの構成

> [!div renderon="docs"]
> *JavaScriptSPA* フォルダーの `index.html` を編集します。`msalConfig` の `clientID` と `authority` の値を設定してください。

> [!div class="sxs-lookup" renderon="portal"]
> *JavaScriptSPA* フォルダーの `index.html` を編集します。`msalConfig` を次の内容に置き換えてください。

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="docs"]
>
> 各値の説明:
> - `Enter_the_Application_Id_here` - 登録したアプリケーションの**アプリケーション (クライアント) ID**。
> - `Enter_the_Tenant_Info_Here` - 次のいずれかのオプションに設定します。
>   - アプリケーションで**この組織のディレクトリ内のアカウント**をサポートする場合は、この値を**テナント ID** または**テナント名**に置き換えます (たとえば、contoso.microsoft.com)
>   - アプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされる場合は、この値を `organizations` に置き換えます。
>   - アプリケーションにおいて**任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント**をサポートする場合は、この値を `common` に置き換えます。 *個人用の Microsoft アカウントのみ*にサポートを制限する場合は、この値を `consumers` に置き換えます。
>
> > [!TIP]
> > **[アプリケーション (クライアント) ID]** 、 **[ディレクトリ (テナント) ID]** 、 **[サポートされているアカウントの種類]** の値を見つけるには、Azure portal でアプリの **[概要]** ページに移動します。
>

#### <a name="step-4-run-the-project"></a>手順 4:プロジェクトを実行する

* [Node.js](https://nodejs.org/en/download/) を使用している場合:

    1. プロジェクトのディレクトリから次のコマンドを実行して、サーバーを起動します。

        ```batch
        npm install
        node server.js
        ```

    1. Web ブラウザーを開き、`http://localhost:30662/` に移動します。
    1. **[サインイン]** ボタンをクリックしてサインインを開始してから、Microsoft Graph API を呼び出します。


* [Visual Studio](https://visualstudio.microsoft.com/downloads/) を使用している場合は、プロジェクト ソリューションを選択したことを確認し、**F5** キーを押してプロジェクトを実行します。

ブラウザーにアプリケーションが読み込まれたら、 **[サインイン]** をクリックします。  初回サインイン時に、アプリケーションがユーザーのプロファイルにアクセスし、ユーザーをサインインすることへの同意を求められます。 正常にサインインすると、ページにユーザー プロファイル情報が表示されます。

## <a name="more-information"></a>詳細情報

### <a name="msaljs"></a>*msal.js*

MSAL はユーザーをサインインし、Microsoft ID プラットフォームによって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 クイック スタートの *index.html* にはライブラリへの参照が含まれています。

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> 上記のバージョンを [MSAL.js リリース](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)の最新のリリース バージョンで置き換えることができます。


別の方法としては、Node がインストール済みの場合に、次のようにして最新バージョンを npm を介してダウンロードできます。

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL の初期化

クイック スタートのコードには、ライブラリを初期化する方法も示されています。

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |Azure portal に登録されているアプリケーションのアプリケーション ID|
> |`authority`    | (省略可能) 上記の構成セクションで説明されている、アカウント タイプをサポートするための機関の URL。 既定の機関は `https://login.microsoftonline.com/common`です。 |
> |`cacheLocation`  | (省略可能) これにより、認証状態のブラウザー ストレージが設定されます。 既定では sessionStorage です。   |
> |`storeAuthStateInCookie`  | (省略可能) ライブラリは、ブラウザーの cookie の認証フローを検証するために必要な認証要求の状態を保存します。 これは、特定の[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)に対処するために、IE および Edge ブラウザー用に設定されます。 |

 使用できる構成可能なオプションの詳細については、[クライアント アプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

### <a name="sign-in-users"></a>ユーザーのサインイン

次のコード スニペットは、ユーザーをサインインする方法を示しています。

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (省略可能) ログイン時にユーザーの同意が要求されるスコープを含めます。 たとえば、Microsoft Graph の場合は `[ "user.read" ]`、カスタム Web API (つまり、`api://<Application ID>/access_as_user`) の場合は `[ "<Application ID URL>/scope" ]` になります。 |

> [!TIP]
> あるいは、`loginRedirect` メソッドを使用して、ポップアップ ウィンドウの代わりに現在のページをサインイン ページにリダイレクトすることもできます。

### <a name="request-tokens"></a>トークンの要求

MSAL では、`acquireTokenRedirect`、`acquireTokenPopup`、`acquireTokenSilent` の 3 つのメソッドを使用してトークンを取得します。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginRedirect` または `loginPopup` メソッドが実行され、その後の呼び出しでは、保護されたリソースにアクセスするトークンを取得するために `acquireTokenSilent` メソッドが通常使用されます。 トークンを要求または更新するための呼び出しは自動的に行われます。

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | API のアクセス トークンで返されるように要求されているスコープを含めます。 たとえば、Microsoft Graph の場合は `[ "user.read" ]`、カスタム Web API (つまり、`api://<Application ID>/access_as_user`) の場合は `[ "<Application ID URL>/scope" ]` になります。|

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

ユーザーに Microsoft ID プラットフォーム エンドポイントとのやり取りを強制しなければならない場合があります。 例: 
* パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある
* お使いのアプリケーションが、ユーザーによる同意が必要な追加のリソース スコープへのアクセスを要求している
* 2 要素認証が必須である

アプリケーションのほとんどについて、通常は、最初に `acquireTokenSilent` を呼び出し、例外をキャッチしてから、`acquireTokenPopup` (または `acquireTokenRedirect`) を呼び出して、対話型要求を開始するパターンをお勧めします。

`acquireTokenPopup` を呼び出すと、サインインするためのポップアップ ウィンドウが表示されます (または、`acquireTokenRedirect` を呼び出すと、ユーザーが Microsoft ID プラットフォーム エンドポイントにリダイレクトされます)。ここで、ユーザーは自分の資格情報を確認するか、必要なリソースへの同意を示すか、または 2 要素認証を完了するかのいずれかの操作を行う必要があります。

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> [!NOTE]
> このクイックスタートでは、使用されるブラウザーが Internet Explorer である場合、Internet Explorer ブラウザーによるポップアップ ウィンドウの処理に関連した[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)のために `loginRedirect` および `acquireTokenRedirect` メソッドを使用します。

## <a name="next-steps"></a>次の手順

このクイック スタート用のアプリケーションをビルドする方法が詳しく説明されているステップ バイ ステップ ガイドについては、以下の JavaScript チュートリアルをお試しください。

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>このクイック スタート用のアプリケーションを作成する手順を確認する

> [!div class="nextstepaction"]
> [サインインして MS Graph を呼び出すチュートリアル](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>MSAL リポジトリでドキュメント、FAQ、問題などを確認する

> [!div class="nextstepaction"]
> [MSAL.js GitHub リポジトリ](https://github.com/AzureAD/microsoft-authentication-library-for-js)

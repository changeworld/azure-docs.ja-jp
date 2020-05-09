---
title: 承認コードを使用して JavaScript シングルページ アプリでユーザーをサインインさせる | Azure
titleSuffix: Microsoft identity platform
description: アクセス トークンが必要な API を JavaScript アプリから Microsoft ID プラットフォームを使用して呼び出す方法について説明します。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871512"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>クイック スタート:承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する 

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。


このクイックスタートでは、承認コード フローで MSAL.js 2.0 を使用します。 暗黙的なフローで MSAL.js 1.0 を使用するには、[このクイックスタート](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript)を参照してください。

このクイックスタートでは、コード サンプルを使用して、JavaScript シングルページ アプリケーション (SPA) で個人アカウント、職場アカウント、学校アカウントのユーザーをサインインさせる方法について学習します。 JavaScript SPA では、Microsoft Graph API または任意の Web API を呼び出すためのアクセス トークンを取得することもできます。 図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [Azure サブスクリプションを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (プロジェクト ファイルの編集に使用)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>クイック スタート アプリケーションを登録してダウンロードする
> クイックスタートのアプリケーションを起動するには、次のオプションのいずれかを使用します。
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1 (簡易): アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. [Azure portal](https://portal.azure.com) にサインインします。
> 1. そのアカウントで複数のテナントにアクセスできる場合は、右上でアカウントを選択してから、ポータルのセッションを、使用したい Azure Active Directory (Azure AD) テナントに設定します。
> 1. [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) を選択します。
> 1. アプリケーションの名前を入力します。
> 1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
> 1. **[登録]** を選択します。
> 1. クイックスタート ペインに移動し、指示に従って新しいアプリケーションをダウンロードして自動的に構成します。
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2 (手動): アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
>
> 1. [Azure portal](https://portal.azure.com) にサインインします。
>
> 1. そのアカウントで複数のテナントにアクセスできる場合は、右上でアカウントを選択してから、ポータルのセッションを、使用したい Azure AD テナントに設定します。
> 1. [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) を選択します。
> 1. **[新規登録]** を選択します。
> 1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
> 1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
> 1. **[登録]** を選択します。 後で使用するために、アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。
> 1. 登録済みのアプリケーションの左側のウィンドウで、 **[認証]** を選択します。
> 1. **[プラットフォーム構成]** で **[プラットフォームを追加]** を選択します。 左側のパネルが開きます。 そこで、 **[シングルページ アプリケーション]** リージョンを選択します。
> 1. 引き続き左側で、 **[リダイレクト URI]** の値を `http://localhost:3000/` に設定します。 
> 1. **[構成]** をクリックします。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタートのコード サンプルを動作させるには、`redirectUri` を `http://localhost:3000/` として追加する必要があります。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

> [!div renderon="docs"]
> Node.js を使用して Web サーバーでプロジェクトを実行するために、[コア プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)します。

> [!div renderon="portal" class="sxs-lookup"]
> Node.js を使用して Web サーバーでプロジェクトを実行する

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>手順 3:JavaScript アプリの構成
>
> *app* フォルダー内の *authConfig.js* を編集し、`msalConfig` の下にある `clientID`、`authority`、および `redirectUri` の値を設定します。
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> 各値の説明:
> - *\<Enter_the_Application_Id_Here>* は、登録したアプリケーションの**アプリケーション (クライアント) ID** です。
> - *\<Enter_the_Cloud_Instance_Id_Here>* は、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、単に「 *https://login.microsoftonline.com/* 」と入力します。 **各国**のクラウド (中国など) の場合は、「[各国のクラウド](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)」を参照してください。
> - *\<Enter_the_Tenant_info_here>* は、次のオプションのいずれかに設定されます。
>    - アプリケーションで "*この組織のディレクトリ内のアカウントのみ*" がサポートされる場合は、この値を**テナント ID** または**テナント名** (例: *contoso.microsoft.com*) に置き換えます。
>    - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます。
>    - アプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。
> - *\<Enter_the_Redirect_Uri_Here>* の値は `http://localhost:3000` です
> > [!TIP]
> > **[アプリケーション (クライアント) ID]** 、 **[ディレクトリ (テナント) ID]** 、 **[サポートされているアカウントの種類]** の値を見つけるには、Azure portal でアプリ登録の **[概要]** ページに移動します。
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
> アプリのプロパティの値を使用してプロジェクトを構成しました。

> [!div renderon="docs"]
>
> 次に、引き続き同じフォルダー内の *graphConfig.js* ファイルを編集して、`apiConfig` オブジェクトの `graphMeEndpoint` と `graphMailEndpoint` を設定します。
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> *\<Enter_the_Graph_Endpoint_Here>* は、API 呼び出しの対象となるエンドポイントです。 メインまたはグローバル Microsoft Graph API サービスの場合は、「`https://graph.microsoft.com`」と入力します。 詳細については、「[国内クラウドのデプロイ](https://docs.microsoft.com/graph/deployments)」をご覧ください。
>
> #### <a name="step-4-run-the-project"></a>手順 4:プロジェクトを実行する

[Node.js](https://nodejs.org/en/download/) を使用して Web サーバーでプロジェクトを実行する

1. サーバーを起動するために、プロジェクト ディレクトリ内から次のコマンドを実行します。
    ```bash
    npm install
    npm start
    ```
1. `http://localhost:3000/` を参照します。

1. **[サインイン]** を選択してサインイン プロセスを開始してから、Microsoft Graph API を呼び出します。

    初回サインイン時に、自分のプロファイルにアプリケーションがアクセスして自分をサインインさせることについて同意を求められます。 正常にサインインした後は、ユーザー プロファイル情報がページに表示されている必要があります。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ

![サンプル JavaScript SPA のしくみは次のとおりです。1. SPA によってサインインが開始されます。 2. SPA が Microsoft ID プラットフォームから ID トークンを取得します。 3. SPA 呼び出しによってトークンが取得されます。 4. Microsoft ID プラットフォームから SPA にアクセス トークンが返されます。 5. SPA が、アクセス トークンを使用して、Microsoft Graph API に対する HTTP GET 要求を行います。 6. Graph API が HTTP 応答を SPA に返します。](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL.js ライブラリは、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API へのアクセスに使用されるトークンを要求します。 サンプルの *index.html* ファイルにはこのライブラリへの参照が含まれています。

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> 上記のバージョンは、[MSAL.js のリリース](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)に関するページにある最新のリリース バージョンに置き換えることができます。

別の方法として、Node.js がインストール済みの場合は、次のようにして最新バージョンを Node.js Package Manager (npm) を使用してダウンロードすることもできます。

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>次のステップ

[MSAL.js GitHub リポジトリ](https://github.com/AzureAD/microsoft-authentication-library-for-js)には、追加のライブラリ ドキュメント、FAQ、および問題のサポートが含まれています。

このクイックスタート用アプリケーションの構築に関する詳細なステップ バイ ステップ ガイドについては、次を参照してください。

> [!div class="nextstepaction"]
> [サインインして MS Graph を呼び出すチュートリアル](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)

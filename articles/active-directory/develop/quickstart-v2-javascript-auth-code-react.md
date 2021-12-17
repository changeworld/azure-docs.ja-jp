---
title: 'クイックスタート: 認可コードを使用して JavaScript React シングルページ アプリ (SPA) でユーザーをサインインさせて Microsoft Graph を呼び出す | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、JavaScript React シングルページ アプリケーション (SPA) で認可コード フローを使用して、個人アカウント、職場アカウント、学校アカウントのユーザーをサインインさせ、Microsoft Graph を呼び出す方法について説明します。
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/12/2021
ROBOTS: NOINDEX
ms.author: jamesmantu
ms.custom: aaddev, "scenarios:getting-started", "languages:JavaScript", devx-track-js, mode-other
ms.openlocfilehash: 2cdbab9976e4df00012597ec1f8c865c945e3e9e
ms.sourcegitcommit: ee9bae378f0b2b63b356a3ef3131640572f8c795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2021
ms.locfileid: "133790696"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-a-react-spa-using-the-auth-code-flow"></a>クイックスタート: 認可コード フローを使用して React SPA 内でユーザーをサインインさせ、アクセス トークンを取得する

このクイックスタートでは、認可コード フローを使用して、JavaScript React シングルページ アプリケーション (SPA) でユーザーをサインインさせ、Microsoft Graph を呼び出す方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、Microsoft Graph API または任意の Web API を呼び出すためのアクセス トークンを取得する方法を示します。

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [Azure サブスクリプションを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成

このコード サンプルでは、 **リダイレクト URI** `http://localhost:3000/` が必要 です。
> [!div class="nextstepaction"]
> [これらの変更を行います]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

Node.js を使用して Web サーバーでプロジェクトを実行する

> [!div class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip)

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
アプリのプロパティの値を使用してプロジェクトを構成しました。

#### <a name="step-4-run-the-project"></a>手順 4:プロジェクトを実行する

Node.js を使用して Web サーバーでプロジェクトを実行します。

1. サーバーを起動するために、プロジェクト ディレクトリ内から次のコマンドを実行します。
    ```console
    npm install
    npm start
    ```
1. `http://localhost:3000/` を参照します。

1. **[サインイン]** を選択してサインイン プロセスを開始してから、Microsoft Graph API を呼び出します。

    初回サインイン時に、自分のプロファイルにアプリケーションがアクセスして自分をサインインさせることについて同意を求められます。 正常にサインインした後、 **[Request Profile Information]\(プロファイル情報をリクエスト\)** をクリックして、自分のプロファイル情報をページに表示します。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ

![シングルページ アプリケーションの認証コード フローを示す図。](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

MSAL.js ライブラリは、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API へのアクセスに使用されるトークンを要求します。

Node.js がインストール済みの場合は、次のように Node.js Package Manager (npm) を使用して最新バージョンをダウンロードできます。

```console
npm install @azure/msal-browser @azure/msal-react
```

## <a name="next-steps"></a>次のステップ

次に、ユーザーをサインインさせ、Microsoft Graph API を呼び出してユーザー プロファイル データを取得する React SPA をゼロから作成する方法を学習するステップバイステップ チュートリアルを試してみましょう。

> [!div class="nextstepaction"]
> [チュートリアル: ユーザーをサインインさせて Microsoft Graph を呼び出す](tutorial-v2-react.md)

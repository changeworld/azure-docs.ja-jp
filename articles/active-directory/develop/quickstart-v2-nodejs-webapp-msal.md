---
title: 'クイックスタート: MSAL Node を使用して Node.js Web アプリに認証を追加する | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Node.js Web アプリと Node.js 用 Microsoft Authentication Library (MSAL) を使用して認証を実装する方法について学習します。
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:js", devx-track-js, mode-api
ms.openlocfilehash: 9eb5b0e60eeddc0c4eba1f21043bf8d3c222e3b4
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803857"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-nodejs-web-app-using-the-auth-code-flow"></a>クイックスタート: 認証コード フローを使用して Node.js Web アプリ内でユーザーをサインインさせ、アクセス トークンを取得する

このクイックスタートでは、Node.js Web アプリで認可コード フローを使用してユーザーのサインインを処理する方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、Microsoft Graph API を呼び出すためのアクセス トークンを取得する方法も示します。

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

このクイックスタートでは、承認コード フローで Node.js 用 Microsoft Authentication Library (MSAL Node) を使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure サブスクリプションを無料で作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)します。
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

#### <a name="step-1-configure-the-application-in-azure-portal"></a>手順 1: Azure portal でのアプリケーションの構成
このクイックスタートのサンプル コードを動作させるには、クライアント シークレットを作成して、応答 URL として `http://localhost:3000/redirect` を追加する必要があります。
> [!div class="nextstepaction"]
> [この変更を行う]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-windows-desktop/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

Node.js を使用して Web サーバーでプロジェクトを実行します。

> [!div class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる

Node.js を使用してプロジェクトを実行します。

1. サーバーを起動するために、プロジェクト ディレクトリ内から次のコマンドを実行します。

    ```console
    npm install
    npm start
    ```

1. 「 `http://localhost:3000/` 」を参照してください。

1. **[Sign In]\(サインイン\)** を選択して、サインイン プロセスを開始します。

    初回サインイン時に、自分のプロファイルにアプリケーションがアクセスして自分をサインインさせることについて同意を求められます。 正常にサインインすると、コマンド ラインにログ メッセージが表示されます。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ

このサンプルは、localhost のポート 3000 で Web サーバーをホストします。 Web ブラウザーがこのサイトにアクセスすると、ユーザーはすぐに Microsoft 認証ページにリダイレクトされます。 このため、このサンプルには HTML や表示要素は一切含まれていません。 認証に成功すると、"OK" というメッセージが表示されます。

### <a name="msal-node"></a>MSAL Node

MSAL Node ライブラリは、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API へのアクセスに使用されるトークンを要求します。 次のように Node.js Package Manager (npm) を使用して、最新バージョンをダウンロードできます。

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [既存の Web アプリに認証を追加する - GitHub コード サンプル >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)

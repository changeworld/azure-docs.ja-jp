---
title: 'クイックスタート: 承認コードを使用して JavaScript シングルページ アプリ (SPA) でユーザーをサインインさせる | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、JavaScript シングルページ アプリケーション (SPA) で認証コード フローを使用して、個人アカウント、職場アカウント、学校アカウントのユーザーをサインインさせる方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/12/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:JavaScript", devx-track-js, mode-other
ms.openlocfilehash: 3d7133e5cec400ae1b4e0af9b608ecc5d8e077b9
ms.sourcegitcommit: ee9bae378f0b2b63b356a3ef3131640572f8c795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2021
ms.locfileid: "133793733"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow-with-pkce"></a>クイックスタート: PKCE 対応の承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する

このクイックスタートでは、PKCE (Proof Key for Code Exchange) 対応の承認コード フローを使用して、JavaScript シングルページ アプリケーション (SPA) でユーザーをサインインし、Microsoft Graph を呼び出す方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、Microsoft Graph API または任意の Web API を呼び出すためのアクセス トークンを取得する方法を示します。

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [Azure サブスクリプションを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター


#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
このクイックスタートのコード サンプルを動作させるには、**リダイレクト URI** (`http://localhost:3000/`) を追加します。
> [!div class="nextstepaction"]
> [これらの変更を行います]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-javascript/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

Node.js を使用して Web サーバーでプロジェクトを実行する

> [!div class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる

アプリのプロパティの値を使用してプロジェクトを構成しました。

Node.js を使用して Web サーバーでプロジェクトを実行します。

1. サーバーを起動するために、プロジェクト ディレクトリ内から次のコマンドを実行します。

    ```console
    npm install
    npm start
    ```

1. 「 `http://localhost:3000/` 」を参照してください。

1. **[サインイン]** を選択してサインイン プロセスを開始してから、Microsoft Graph API を呼び出します。

    初回サインイン時に、自分のプロファイルにアプリケーションがアクセスして自分をサインインさせることについて同意を求められます。 正常にサインインすると、ユーザー プロファイル情報がページに表示されます。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ

![シングルページ アプリケーションの認証コード フローを示す図。](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>MSAL.js

MSAL.js ライブラリは、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API へのアクセスに使用されるトークンを要求します。 サンプルの *index.html* ファイルにはこのライブラリへの参照が含まれています。

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Node.js がインストール済みの場合は、次のように Node.js Package Manager (npm) を使用して最新バージョンをダウンロードできます。

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>次のステップ

このクイック スタートで使用されるアプリケーションの構築に関する詳細なステップ バイ ステップ ガイドについては、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [サインインして MS Graph を呼び出すチュートリアル](./tutorial-v2-javascript-auth-code.md)

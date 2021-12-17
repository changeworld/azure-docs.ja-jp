---
title: Azure Functions を使用して Azure Static Web Apps に API を追加する
description: Azure Functions を使用して、静的 Web アプリにサーバーレス API を追加することで、Azure Static Web Apps の使用を開始します。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/14/2021
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: be664a5bf29c95143e7706b2df2b49648959f584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013671"
---
<<<<<<< HEAD
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions を使用して Azure Static Web Apps に API を追加する
=======
# <a name="add-an-api-to-azure-static-web-apps-with-azure-functions"></a>Azure Functions を使用して Azure Static Web Apps に API を追加する
>>>>>>> repo_sync_working_branch

Azure Functions が提供する Azure Static Web Apps に、サーバーレス API を追加することができます。 この記事では、Azure Static Web Apps サイトに API を追加してデプロイする方法について説明します。

> [!NOTE]
> Static Web Apps で既定で提供されている機能は、セキュリティで保護された API エンドポイントを提供するようにあらかじめ構成されており、HTTP トリガーの機能のみをサポートしています。 スタンドアロンの Azure Functions アプリとの違いについては、[Azure Functions での API のサポート](apis.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。
  - アカウントがない場合は、[アカウントを無料で作成](https://azure.microsoft.com/free)できます。
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code 用 [Azure Static Web Apps 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- フロントエンド アプリと API を実行するための [Node.js](https://nodejs.org/download/)

## <a name="create-the-static-web-app"></a>静的 Web アプリの作成

<<<<<<< HEAD
次の手順では、新しいリポジトリを作成し、お使いのコンピューターにファイルをクローンする方法を示します。
=======
API を追加する前に、フロントエンド アプリケーションを作成して Azure Static Web Apps にデプロイします。 デプロイ済みの既存のアプリを使用するか、「[Azure Static Web Apps を使用して静的サイトを初めて構築する](getting-started.md)」クイックスタートに従って作成します。
>>>>>>> repo_sync_working_branch

Visual Studio Code で、アプリのリポジトリのルートを開きます。 フォルダ構造には、フロントエンド アプリのソースと、Static Web Apps の GitHub ワークフローが _.github/workflows_ フォルダに入っています。

```Files
├── .github
│   └── workflows
│       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
│
└── (folders and files from your static web app)
```

<<<<<<< HEAD
プロジェクトが作成されたら、ブラウザー内の新しいリポジトリの URL をコピーします。 この URL を Visual Studio Code で使用して、Git リポジトリをクローンします。
=======
## <a name="create-the-api"></a>API の作成
>>>>>>> repo_sync_working_branch

静的 Web アプリの API 用に Azure Functions プロジェクトを作成します。 既定では、Static Web Apps Visual Studio Code 拡張機能によって、リポジトリのルートにある _api_ という名前のフォルダーにプロジェクトが作成されます。

<<<<<<< HEAD
   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Visual Studio Code を使用して GitHub プロジェクトをクローンする":::

    画面の指示に従って、プロジェクトをクローンするリポジトリの場所を選択します。
=======
1. <kbd>F1</kbd> キーを押して、コマンド パレットを開きます。

1. **[Azure Static Web Apps: Create HTTP Function...]** を選択します。Azure Functions 拡張機能のインストールを求めるメッセージが表示されたら、インストールしてから、このコマンドを再実行します。
>>>>>>> repo_sync_working_branch

1. 確認を求められたら、次の値を入力します。

    | Prompt | 値 |
    | --- | --- |
    | 言語を選択する | **JavaScript** |
    | Provide a function name (関数名を指定してください) | **message** |

    HTTP トリガー関数によって、Azure Functions プロジェクトが生成されます。 これで、アプリのプロジェクト構造が次の例のようになります。

    ```Files
    ├── .github
    │   └── workflows
    │       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
    │
    ├── api
    │   ├── message
    │   │   ├── function.json
    │   │   └── index.js
    │   ├── host.json
    │   ├── local.settings.json
    │   └── package.json
    │
    └── (folders and files from your static web app)
    ```

1. 次に、メッセージをフロントエンドに返すように `message` 関数を変更します。 以下のコードを使用して、_api/message/index.js_ の下の関数を更新します。

    ```javascript
    module.exports = async function (context, req) {
        context.res.json({
            text: "Hello from the API"
        });
    };
    ```

> [!TIP]
> API 関数をさらに追加するには、**Azure Static Web Apps: Create HTTP Function...** コマンドを再度実行します。

## <a name="update-the-frontend-app-to-call-the-api"></a>API を呼び出すフロントエンド アプリを更新する

フロントエンド アプリを更新して、`/api/message` の API を呼び出し、応答メッセージを表示します。

クイックスタートを使用してアプリを作成した場合は、次の手順に従って更新プログラムを適用します。

# <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

以下のコードを使用して _src/index.html_ ファイルの内容を更新し、API 関数からテキストをフェッチして画面に表示します。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Vanilla JavaScript App</title>
</head>

<body>
    <main>
    <h1>Vanilla JavaScript App</h1>
    <p>Loading content from the API: <b id="name">...</b></p>
    </main>

    <script>
    (async function() {
        const { text } = await( await fetch(`/api/message`)).json();
        document.querySelector('#name').textContent = text;
    }())
    </script>
</body>

</html>
```

# <a name="angular"></a>[Angular](#tab/angular)

1. 次のコードを使用して _src/app/app.module.ts_ の内容を更新し、アプリで `HttpClient` を有効にします。

    ```typescript
    import { BrowserModule } from "@angular/platform-browser";
    import { NgModule } from "@angular/core";
    import { HttpClientModule } from '@angular/common/http';
    
    import { AppComponent } from "./app.component";
    
    @NgModule({
      declarations: [AppComponent],
      imports: [BrowserModule, HttpClientModule],
      bootstrap: [AppComponent]
    })
    export class AppModule {}
    ```

1. 以下のコードを使用して _src/app/app.component.ts_ ファイルの内容を更新し、API 関数からテキストをフェッチして画面に表示します。

    ```typescript
    import { HttpClient } from '@angular/common/http';
    import { Component } from '@angular/core';
    
    @Component({
      selector: 'app-root',
      template: `<div>{{message}}</div>`,
    })
    export class AppComponent {
      message = '';
    
      constructor(private http: HttpClient) {
        this.http.get('/api/message')
          .subscribe((resp: any) => this.message = resp.text);
      }
    }
    ```

# <a name="react"></a>[React](#tab/react)

以下のコードを使用して _src/App.js_ ファイルの内容を更新し、API 関数からテキストをフェッチして画面に表示します。

```javascript
import React, { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState('');

  useEffect(() => {
    (async function () {
      const { text } = await( await fetch(`/api/message`)).json();
      setData(text);
    })();
  });

  return <div>{data}</div>;
}

export default App;
```

# <a name="vue"></a>[Vue](#tab/vue)

以下のコードを使用して _src/App.vue_ ファイルの内容を更新し、API 関数からテキストをフェッチして画面に表示します。

```javascript
<template>
  <div>{{ message }}</div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      message: ""
    };
  },
  async mounted() {
    const { text } = await (await fetch("/api/message")).json();
    this.message = text;
  }
};
</script>
```

---

## <a name="run-the-frontend-and-api-locally"></a>フロントエンドと API をローカルで実行する

フロントエンド アプリと API を一緒にローカルで実行するために、Azure Static Web Apps はクラウド環境をエミュレートする CLI を提供します。 CLI は、API を実行するために Azure Functions Core Tools を活用します。

### <a name="install-command-line-tools"></a>コマンド ライン ツールをインストールする

必要なコマンド ライン ツールがインストールされていることを確認します。

1. Azure Static Web Apps CLI をインストールします。
    ```bash
    npm install -g @azure/static-web-apps-cli
    ```

1. Azure Functions Core Tools V3 をインストールします。
    ```bash
    npm install -g azure-functions-core-tools@3
    ```

### <a name="build-frontend-app"></a>フロントエンド アプリをビルドする

アプリでフレームワークを使用する場合は、アプリをビルドして出力を生成してから、Static Web Apps CLI を実行します。

# <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

アプリをビルドする必要はありません。

# <a name="angular"></a>[Angular](#tab/angular)

_dist/angular-basic_ フォルダーにアプリをビルドします。

```bash
npm run build --prod
```

# <a name="react"></a>[React](#tab/react)

_build_ フォルダーにアプリをビルドします。

```bash
npm run build
```

# <a name="vue"></a>[Vue](#tab/vue)

_dist_ フォルダーにアプリをビルドします。

```bash
npm run build
```

---

### <a name="start-the-cli"></a>CLI を起動する

アプリを Static Web Apps CLI で起動して、フロントエンド アプリと API を一緒に実行します。 この方法でアプリケーションの 2 つの部分を実行すると、CLI によってフロントエンドのビルド出力がフォルダから提供され、実行中のアプリから API にアクセスできるようになります。

1. リポジトリのルートで、`start` コマンドを使用して Static Web Apps CLI を起動します。 アプリのフォルダー構造が異なる場合は、引数を調整します。

    # <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

    現在のフォルダー (`src`) と API フォルダー (`api`) を CLI に渡します。
     
    ```bash
    swa start src --api-location api
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ビルドの出力フォルダー (`dist/angular-basic`) と API フォルダー (`api`) を CLI に渡します。

    ```bash
    swa start dist/angular-basic --api-location api
    ```

    # <a name="react"></a>[React](#tab/react)

    ビルドの出力フォルダー (`build`) と API フォルダー (`api`) を CLI に渡します。

    ```bash
    swa start build --api-location api
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ビルドの出力フォルダー (`dist`) と API フォルダー (`api`) を CLI に渡します。

    ```bash
    swa start dist --api-location api
    ```

    ---

1. CLI プロセスが開始したら、`http://localhost:4280/` からアプリにアクセスします。 ページで API が呼び出され、その出力 (`Hello from the API`) が表示されていることを確認します。

1. CLI を停止するには、<kbd>Ctrl + C</kbd> キーを押します。

## <a name="add-api-location-to-workflow"></a>ワークフローに API の場所を追加する

アプリを Azure にデプロイする前に、リポジトリの GitHub Actions ワークフローを更新して、API フォルダの正しい場所を指定します。

1. _.github/workflows/azure-static-web-apps-\<DEFAULT-HOSTNAME>.yml_ でワークフローを開きます。

1. プロパティ (`api_location`) を追加し、値を `api` に設定します。
1. ファイルを保存します。

## <a name="deploy-changes"></a>変更をデプロイする

静的 Web アプリの変更を Azure で公開するには、リモートの GitHub リポジトリにコードをコミットしてプッシュします。

1. <kbd>F1</kbd> キーを押して、コマンド パレットを開きます。

1. **Git: Commit All** コマンドを選択します。

1. コミット メッセージが表示されたら、「**add API**」と入力し、すべての変更をローカルの git リポジトリにコミットします。

1. <kbd>F1</kbd> キーを押して、コマンド パレットを開きます。

1. **Git: push** コマンドを選択します。

    変更は GitHub のリモート リポジトリにプッシュされ、Static Web Apps GitHub Actions ワークフローがトリガーされ、アプリがビルドおよびデプロイされます。

1. ワークフロー実行の状態を監視するには、GitHub でリポジトリを開きます。

1. ワークフロー実行が完了したら、静的 Web アプリにアクセスして変更を確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの設定の構成](./application-settings.md)

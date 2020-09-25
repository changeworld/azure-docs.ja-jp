---
title: クイックスタート - Azure Communication Services を使用して Web アプリに VOIP 通話を追加する
description: このチュートリアルでは、Javascript 用の Azure Communication Services 通話クライアント ライブラリを使用する方法について説明します
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d58b4d86936c56a08f27bef59edc1d3cc4ce4617
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945999"
---
このクイックスタートでは、JavaScript 用の Azure Communication Services 通話クライアント ライブラリを使用して、通話を開始する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
- アクティブな Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントをインスタンス化するためのユーザー アクセス トークン。 [ユーザー アクセス トークンを作成して管理する](../../access-tokens.md)方法を参照してください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir calling-quickstart && cd calling-quickstart
```

既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話クライアント ライブラリをインストールします。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

このクイックスタートでは、webpack を使用してアプリケーション資産をバンドルします。 次のコマンドを実行して、webpack、webpack-cli、および webpack-dev-server npm パッケージをインストールし、**package.json** 内の開発依存関係として表示します。

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

自分のプロジェクトのルート ディレクトリに、**index.html** ファイルを作成します。 このファイルを使用して、ユーザーが Azure 通信ボットに電話をかけることができるようにする基本的なレイアウトを構成します。

次にコードを示します。

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

このクイックスタートのアプリケーション ロジックを格納するために、**client.js** という名前のファイルを自分のプロジェクトのルート ディレクトリに作成します。 次のコードを追加して、通話クライアントをインポートし、ビジネス ロジックをアタッチできるように DOM 要素への参照を取得します。 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");

// quickstart code goes here
```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services 通話クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                             | 説明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient は、通話クライアント ライブラリへのメイン エントリ ポイントです。                                                                       |
| CallAgent                        | CallAgent は、通話を開始および管理するために使用します。                                                                                            |
| AzureCommunicationUserCredential | AzureCommunicationUserCredential クラスによって、CallAgent のインスタンス化に使用する CommunicationUserCredential インターフェイスが実装されます。 |


## <a name="authenticate-the-client"></a>クライアントを認証する

`<USER_ACCESS_TOKEN>` を、リソース用の有効なユーザー アクセス トークンで置き換える必要があります。 まだトークンを入手していない場合は、[ユーザー アクセス トークン](../../access-tokens.md)に関するドキュメントを参照してください。 `CallClient` を使用して、`CallAgent` インスタンスを `CommunicationUserCredential` で初期化します。これにより、電話をかりたり受けたりすることができるようになります。 次のコードを **client.js** に追加します。

```javascript
const callClient = new CallClient();
const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
let callAgent;

callClient.createCallAgent(tokenCredential).then(agent => {
  callAgent = agent;
  callButton.disabled = false;
});
```

## <a name="start-a-call"></a>通話を開始する

`callButton` がクリックされたときに通話を開始するイベント ハンドラーを追加します。

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>通話を終了する

`hangUpButton` がクリックされたときに現在の通話を終了するイベント リスナーを追加します。

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

`forEveryone` プロパティは、すべての通話参加者の通話を終了します。

## <a name="run-the-code"></a>コードの実行

アプリをビルドして実行するには、`webpack-dev-server` を使用します。 次のコマンドを実行して、ローカルの Web サーバーにアプリケーション ホストをバンドルします。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="完成した JavaScript アプリケーションのスクリーンショット。":::

発信 VOIP 通話を行うには、テキスト フィールドにユーザー ID を指定し、 **[Start Call]\(通話の開始\)** ボタンをクリックします。 `8:echo123` を呼び出すとエコー ボットに接続されます。これは、オーディオ デバイスを起動し、デバイスが機能していることを確認する場合に役立ちます。

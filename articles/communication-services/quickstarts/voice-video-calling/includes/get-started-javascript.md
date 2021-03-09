---
title: クイックスタート - Azure Communication Services を使用して Web アプリに VOIP 通話を追加する
description: このチュートリアルでは、JavaScript 用 Azure Communication Services 通話クライアント ライブラリの使用方法について説明します。
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d27a79e180a0219773a3094fb85f842773d75183
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656618"
---
このクイックスタートでは、JavaScript 用の Azure Communication Services 通話クライアント ライブラリを使用して、通話を開始する方法について説明します。
このドキュメントでは、呼び出し元ライブラリのバージョン 1.0.0-beta.5 の型を参照しています。

> [!NOTE]
> このドキュメントでは、呼び出し元のクライアント ライブラリのバージョン 1.0.0-beta.6 を使用します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
- アクティブな Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントをインスタンス化するためのユーザー アクセス トークン。 [ユーザー アクセス トークンを作成して管理する](../../access-tokens.md)方法を参照してください。


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

コードは次のとおりです。

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
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services 通話クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                             | 説明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient は、通話クライアント ライブラリへのメイン エントリ ポイントです。                                                                       |
| CallAgent                        | CallAgent は、通話を開始および管理するために使用します。                                                                                            |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential クラスによって、CallAgent のインスタンス化に使用する CommunicationTokenCredential インターフェイスが実装されます。 |


## <a name="authenticate-the-client"></a>クライアントを認証する

`<USER_ACCESS_TOKEN>` を、リソース用の有効なユーザー アクセス トークンで置き換える必要があります。 まだトークンを入手していない場合は、[ユーザー アクセス トークン](../../access-tokens.md)に関するドキュメントを参照してください。 `CallClient` を使用して、`CallAgent` インスタンスを `CommunicationTokenCredential` で初期化します。これにより、電話をかりたり受けたりすることができるようになります。 次のコードを **client.js** に追加します。

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>通話を開始する

`callButton` がクリックされたときに通話を開始するイベント ハンドラーを追加します。

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
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

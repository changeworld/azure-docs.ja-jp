---
title: クイックスタート - Azure Communication Services を使用して Web アプリに VOIP 通話を追加する
description: このチュートリアルでは、JavaScript 用 Azure Communication Services Calling SDK の使用方法について説明します
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: bd9a607b46c87ca590bcefef310f822d6f23483c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386786"
---
このクイックスタートでは、JavaScript 用の Azure Communication Services Calling SDK を使用して、通話を開始する方法について説明します。

> [!NOTE]
> このドキュメントでは、Calling SDK のバージョン 1.0.0-beta.10 を使用します。


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
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
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
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services Calling SDK の主な機能のいくつかは、次のクラスとインターフェイスによって処理されます。

| 名前                             | 説明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient は、Calling SDK へのメイン エントリ ポイントです。                                                                       |
| CallAgent                        | CallAgent は、通話を開始および管理するために使用します。                                                                                            |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential クラスによって、CallAgent のインスタンス化に使用する CommunicationTokenCredential インターフェイスが実装されます。 |


## <a name="authenticate-the-client"></a>クライアントを認証する

リソースの有効なユーザー アクセス トークンをテキスト フィールドに入力し、[Submit]\(送信\) をクリックする必要があります。 まだトークンを入手していない場合は、[ユーザー アクセス トークン](../../access-tokens.md)に関するドキュメントを参照してください。 `CallClient` を使用して、`CallAgent` インスタンスを `CommunicationTokenCredential` で初期化します。これにより、電話をかりたり受けたりすることができるようになります。 次のコードを **client.js** に追加します。

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>通話を開始する

`callButton` がクリックされたときに通話を開始するイベント ハンドラーを追加します。

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
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
  submitToken.disabled = false;
});
```

`forEveryone` プロパティは、すべての通話参加者の通話を終了します。

## <a name="run-the-code"></a>コードの実行

アプリをビルドして実行するには、`webpack-dev-server` を使用します。 次のコマンドを実行して、ローカルの Web サーバーにアプリケーション ホストをバンドルします。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="完成した JavaScript アプリケーションのスクリーンショット。":::

発信 VOIP 通話を行うには、有効なユーザー アクセス トークンとユーザー ID を対応するテキスト フィールドに指定し、 **[Start Call]\(通話の開始\)** ボタンをクリックします。 `8:echo123` を呼び出すとエコー ボットに接続されます。これは、オーディオ デバイスを起動し、デバイスが機能していることを確認する場合に役立ちます。

## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling) からダウンロードできます。

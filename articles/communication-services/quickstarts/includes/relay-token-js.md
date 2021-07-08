---
title: インクルード ファイル
description: インクルード ファイル
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 05/20/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: 118aa3bfcc35b0250b13ea3446e41e8f4b9f06d8
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026936"
---
## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成し、そこに移動します。

```console
mkdir relay-token-quickstart && cd relay-token-quickstart
```
既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、Azure Communication Services ID クライアント ライブラリと JavaScript 用の Network Traversal ライブラリをインストールします。

```console
npm install @azure/communication-identity --save
npm install @azure/communication-network-traversal --save
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. コード エディターで新しいテキスト ファイルを開きます
2. `require` ディレクティブをファイルの一番上に追加して、Azure Communication ID SDK と Network Traversal SDK を使用します
3. 基本的な例外処理を含め、プログラムの構造を作成します

   次のコードを使用して開始します。

   ```javascript
   const { CommunicationIdentityClient } = require("@azure/communication-identity");
   const { CommunicationRelayClient } = require("@azure/communication-network-traversal");;

   const main = async () => {
     console.log("Azure Communication Services - Relay Token Quickstart")
  
     // Quickstart code goes here
   };

   main().catch((error) => {
     console.log("Encountered and error");
     console.log(error);
   })
   ```

4. 新しいファイルを **relay-token.js** として *user-tokens-quickstart* ディレクトリに保存します。

### <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `CommunicationIdentityClient` をインスタンス化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

`main` メソッドに次のコードを追加します。

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>ID の作成

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `createUser` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 受け取った ID を、アプリケーションのユーザーへのマッピングと共に格納します。 これらは、アプリケーション サーバーのデータベースなどに格納します。 ID は、後でアクセス トークンを発行するために必要になります。

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>ユーザー アクセス トークンをリレー トークンと交換する

Azure Communication トークン サービスを呼び出して、ユーザー アクセス トークンを TURN サービス トークンと交換する

```javascript
    const relayClient = new CommunicationRelayClient(connectionString);
    console.log("Getting relay configuration");

    const config = await relayClient.getRelayConfiguration(identityResponse);
    console.log("RelayConfig", config);
```

### <a name="use-the-token-on-the-client-as-an-ice-candidate"></a>ICE 候補としてクライアントでトークンを使用する

これで、トークンを逆シリアル化して、クライアント ブラウザーで WebRTC を使用して ICE 候補を追加できます。

```javascript  
var configuration = { iceServers: iceServers };
var peerConnection = new RTCPeerConnection(configuration);
```

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、*issue-token.js* ファイルが格納されているディレクトリに移動し、次の `node` コマンドを実行してアプリを実行します。

```console
node ./relay-token.js
```

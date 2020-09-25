---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945758"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成し、そこに移動します。

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

`npm init -y` を実行して、既定の設定で **package.json** ファイルを作成します。

```console
npm init -y
```

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 管理クライアント ライブラリをインストールします。

```console

npm install @azure/communication-administration --save

```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

## <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. コード エディターで新しいテキスト ファイルを開きます
1. `CommunicationIdentityClient` を読み込むための `require` 呼び出しを追加します
1. 基本的な例外処理を含め、プログラムの構造を作成します

次のコードを使用して開始します。

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. 新しいファイルを **issue-token.js** として *user-tokens-quickstart* ディレクトリに保存します。

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `CommunicationIdentityClient` をインスタンス化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

`main` メソッドに次のコードを追加します。

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>ユーザーを作成する

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `createUser` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 お使いのアプリケーションのユーザーと、Communication Services で生成された ID の間のマッピングを (アプリケーション サーバーのデータベースにこれらを格納するなどによって) 維持する必要があります。

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>ユーザー アクセス トークンを発行する

Communication Services ユーザーのアクセス トークンを発行するには、`issueToken` メソッドを使用します。 オプションの `user` パラメーターを指定しない場合は、新しいユーザーが作成され、トークンと共に返されます。

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

ユーザー アクセス トークンは、ユーザーのサービス中断を防ぐために再発行する必要がある、有効期間の短い資格情報です。 `expiresOn` 応答プロパティは、トークンの有効期間を示します。

## <a name="revoke-user-access-tokens"></a>ユーザー アクセス トークンを取り消す

場合によっては、ユーザーがサービスへの認証に使用するパスワードを変更したときなどに、ユーザー アクセス トークンを明示的に取り消す必要があります。 これには `revokeTokens` メソッドを使用して、すべてのユーザーのアクセス トークンを無効にします。

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>ユーザーの削除

ユーザーを削除すると、すべてのアクティブなトークンが取り消され、その ID についての後続のトークンを発行できなくなります。 また、ユーザーに関連付けられているすべての永続化されたコンテンツも削除されます。

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、*issue-token.js* ファイルが格納されているディレクトリに移動し、次の `node` コマンドを実行してアプリを実行します。

```console
node ./issue-token.js
```

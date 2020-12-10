---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: a64d26ad11911e2cb9dcdec027b3ab3e4d22984b
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584533"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成し、そこに移動します。

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
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
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. 新しいファイルを **issue-access-token.js** として *access-tokens-quickstart* ディレクトリに保存します。

## <a name="authenticate-the-client"></a>クライアントを認証する

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

## <a name="issue-access-tokens"></a>アクセス トークンを発行する

既存の Communication Services ID のアクセス トークンを発行するには、`issueToken` メソッドを使用します。 パラメーター `scopes` によって、このアクセス トークンを承認するプリミティブのセットが定義されます。 [サポートされているアクションの一覧](../../concepts/authentication.md)を参照してください。 パラメーター `communicationUser` の新しいインスタンスは、Azure Communication Services ID の文字列表現に基づいて作成できます。

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

アクセス トークンは有効期間の短い資格情報であるため、再発行が必要になります。 そうしないと、アプリケーションのユーザー エクスペリエンスが中断される可能性があります。 `expiresOn` 応答プロパティは、アクセス トークンの有効期間を示します。


## <a name="refresh-access-tokens"></a>アクセス トークンの更新

アクセス トークンの更新は、トークンの発行に使用されたものと同じ ID を使用して `issueToken` を呼び出すだけです。 また、更新されたトークンの `scopes` も指定する必要があります。 

```javascript
// // Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>アクセス トークンの取り消し

場合によっては、明示的にアクセス トークンを取り消すことがあります。 たとえば、アプリケーションのユーザーが、サービスに対する認証に使用するパスワードを変更するような場合です。 `revokeTokens` メソッドを使用すると、ID に対して発行されたすべてのアクティブなアクセス トークンを無効にできます。

```javascript  
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>ID の削除

ID を削除すると、すべてのアクティブなアクセス トークンが取り消され、その ID に対してアクセス トークンを発行できなくなります。 また、ID に関連付けられているすべての永続化されたコンテンツも削除されます。

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、*issue-access-token.js* ファイルが格納されているディレクトリに移動し、次の `node` コマンドを実行してアプリを実行します。

```console
node ./issue-access-token.js
```

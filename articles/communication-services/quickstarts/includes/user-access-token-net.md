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
ms.openlocfilehash: 2213da7b9c6e4776a0e463e6a43d0cc645a1e911
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750343"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- お使いのオペレーティング システムに対応した最新バージョンの [.NET Core クライアント ライブラリ](https://dotnet.microsoft.com/download/dotnet-core)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`AccessTokensQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o AccessTokensQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`dotnet add package` コマンドを使用して、.NET 用の Azure Communication Services ID ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Communication.Identity
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. テキスト エディターで、**Program.cs** ファイルを開きます
1. `Azure.Communication.Identity` 名前空間を含めるように `using` ディレクティブを追加します
1. 非同期コードをサポートするように `Main` メソッドの宣言を更新します

次のコードを使用して開始します。

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `CommunicationIdentityClient` を初期化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

`Main` メソッドに次のコードを追加します。

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>ID の作成

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `createUser` メソッドを使用して、一意の `Id` が指定された新しいエントリをディレクトリ内に作成します。受け取った ID を、アプリケーションのユーザーへのマッピングと共に格納します。 これらは、アプリケーション サーバーのデータベースなどに格納します。 ID は、後でアクセス トークンを発行するために必要になります。

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>ID のアクセス トークンを発行する

既存の Communication Services ID のアクセス トークンを発行するには、`issueToken` メソッドを使用します。 パラメーター `scopes` によって、このアクセス トークンを承認するプリミティブのセットが定義されます。 [サポートされているアクションの一覧](../../concepts/authentication.md)を参照してください。 パラメーター `communicationUser` の新しいインスタンスは、Azure Communication Services ID の文字列表現に基づいて作成できます。

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

アクセス トークンは有効期間の短い資格情報であるため、再発行が必要になります。 そうしないと、アプリケーションのユーザー エクスペリエンスが中断される可能性があります。 `expiresOn` 応答プロパティは、アクセス トークンの有効期間を示します。 

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>ID を作成し、同じ要求内でアクセス トークンを発行する

`createUserWithToken` メソッドを使用して、Communication Services ID を作成し、そのアクセス トークンを発行します。 パラメーター `scopes` によって、このアクセス トークンを承認するプリミティブのセットが定義されます。 [サポートされているアクションの一覧](../../concepts/authentication.md)を参照してください。

```csharp  
// Issue an identity and an access token with the "voip" scope for the new identity
var identityWithTokenResponse = await client.CreateUserWithTokenAsync(scopes: new[] { CommunicationTokenScope.VoIP });
var identity = identityWithTokenResponse.Value.user.Id;
var token = identityWithTokenResponse.Value.token.Token;
var expiresOn = identityWithTokenResponse.Value.token.ExpiresOn;
```

## <a name="refresh-access-tokens"></a>アクセス トークンの更新

アクセス トークンを更新するには、`CommunicationUser` オブジェクトのインスタンスを `IssueTokenAsync` に渡します。 この `Id` を保存済みであり、新しい `CommunicationUser` を作成する必要がある場合、これを行うには、次のように保存済みの `Id` を `CommunicationUser` コンストラクターに渡します。

```csharp  
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
identityToRefresh = new CommunicationUser(userId);
tokenResponse = await client.IssueTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>アクセス トークンの取り消し

場合によっては、明示的にアクセス トークンを取り消すことがあります。 たとえば、アプリケーションのユーザーが、サービスに対する認証に使用するパスワードを変更するような場合です。 `RevokeTokensAsync` メソッドを使用すると、ID に対して発行されたすべてのアクティブなアクセス トークンを無効にできます。

```csharp  
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>ID の削除

ID を削除すると、すべてのアクティブなアクセス トークンが取り消され、その ID に対してアクセス トークンを発行できなくなります。 また、ID に関連付けられているすべての永続化されたコンテンツも削除されます。

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

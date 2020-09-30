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
ms.openlocfilehash: e4a52095f7fc1c8252ffbbbbedf4520773d3efc1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377083"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- お使いのオペレーティング システムに対応した最新バージョンの [.NET Core クライアント ライブラリ](https://dotnet.microsoft.com/download/dotnet-core)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`UserAccessTokensQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o UserAccessTokensQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`dotnet add package` コマンドを使用して、.NET 用の Azure Communication Services 管理ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.1
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. テキスト エディターで、**Program.cs** ファイルを開きます
1. `Azure.Communication.Administration` 名前空間を含めるように `using` ディレクティブを追加します
1. 非同期コードをサポートするように `Main` メソッドの宣言を更新します

次のコードを使用して開始します。

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `CommunicationIdentityClient` を初期化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

`Main` メソッドに次のコードを追加します。

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>ユーザーを作成する

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `createUser` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 お使いのアプリケーションのユーザーと、Communication Services で生成された ID の間のマッピングを (アプリケーション サーバーのデータベースにこれらを格納するなどによって) 維持する必要があります。

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>ユーザー アクセス トークンを発行する

Communication Services ユーザーのアクセス トークンを発行するには、`issueToken` メソッドを使用します。 オプションの `user` パラメーターを指定しない場合は、新しいユーザーが作成され、トークンと共に返されます。

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

ユーザー アクセス トークンは、ユーザーのサービス中断を防ぐために再発行する必要がある、有効期間の短い資格情報です。 `expiresOn` 応答プロパティは、トークンの有効期間を示します。

## <a name="revoke-user-access-tokens"></a>ユーザー アクセス トークンを取り消す

場合によっては、ユーザーがサービスへの認証に使用するパスワードを変更したときなどに、ユーザー アクセス トークンを明示的に取り消す必要があります。 この機能は、Azure Communication Services 管理クライアント ライブラリを通じて利用できます。

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>ユーザーの削除

ID を削除すると、すべてのアクティブなトークンが取り消され、その ID についての後続のトークンを発行できなくなります。 また、ユーザーに関連付けられているすべての永続化されたコンテンツも削除されます。

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

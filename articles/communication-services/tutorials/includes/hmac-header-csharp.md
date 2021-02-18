---
title: C# で HTTP 要求に署名する
description: これは、Communication Services の HMAC 署名を使用して HTTP 要求に署名する方法の C# バージョンです。
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3c1b56f81e5164bbdfa94fdaeca5f5f1f55b3b51
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552349"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。
- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 
- [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../quickstarts/create-communication-resource.md)に関するページを参照してください。このチュートリアル用に、自分の **resourceEndpoint** と **resourceAccessKey** を記録する必要があります。



## <a name="sign-an-http-request-with-c"></a>C# で HTTP 要求に署名する
アクセス キー認証では、共有秘密鍵を使用して、HTTP 要求ごとに HMAC 署名が生成されます。 この署名は SHA256 アルゴリズムを使用して生成され、`HMAC-SHA256` スキームを使用して `Authorization` ヘッダーで送信されます。 次に例を示します。

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature` は次の要素で構成されます。 

- HTTP 動詞 (例: `GET`、`PUT`)
- HTTP 要求パス
- Date
- Host
- x-ms-content-sha256

## <a name="setting-up"></a>設定
以下の手順で、Authorization ヘッダーを作成する方法を示します。

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`SignHmacTutorial` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o SignHmacTutorial
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>パッケージをインストールする

本文のシリアル化に使用する `Newtonsoft.Json` パッケージをインストールします。

```console
dotnet add package Newtonsoft.Json
```

非同期コードをサポートするように `Main` メソッドの宣言を更新します。 次のコードを使用して開始します。

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>要求メッセージを作成する

この例では、Communication Services Authentication API (バージョン `2021-03-07`) を使用して新しい ID を作成するために、要求に署名します

`Main` メソッドに次のコードを追加します。

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

`resourceEndpoint` を実際のリソース エンドポイント値に置き換えます。

## <a name="create-content-hash"></a>コンテンツ ハッシュを作成する

コンテンツ ハッシュは、HMAC 署名の一部です。 コンテンツ ハッシュを計算するには、次のコードを使用します。 このメソッドは、`Progam.cs` の `Main` メソッドの下に追加できます。

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>署名を計算する
次のコードを使用して、HMAC 署名を計算するためのメソッドを作成します。

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

`resourceAccessKey` は実際の Azure Communication Services リソースのアクセス キーに置き換えてください。

## <a name="create-an-authorization-header-string"></a>Authorization ヘッダー文字列を作成する

次に、Authorization ヘッダーに追加する文字列を作成します。

1. コンテンツ ハッシュを計算します
2. 協定世界時 (UTC) のタイムスタンプを指定します
3. 署名する文字列を準備します
4. 署名を計算します
5. Authorization ヘッダーで使用される文字列を連結します
 
`Main` メソッドに次のコードを追加します。

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>requestMessage にヘッダーを追加する

次のコードを使用して、必要なヘッダーを `requestMessage` に追加します。

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>クライアントのテスト
`HttpClient` を使用してエンドポイントを呼び出し、応答を確認します。

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```

---
title: クイック スタート - Azure SignalR サービス REST API | Microsoft Docs
description: Azure SignalR サービスの REST API を使用するためのクイック スタート。
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972761"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>クイック スタート: コンソール アプリからのリアルタイム メッセージのブロードキャスト

Azure SignalR サービスは、ブロードキャストなどのサーバーとクライアントの間の通信シナリオをサポートする [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) を提供します。 REST API 呼び出しが可能な任意のプログラミング言語を選択できます。 接続されているすべてのクライアント、名前を指定したクライアント、またはクライアントのグループにメッセージを投稿することができます。

このクイック スタートでは、コマンド ライン アプリから接続されたクライアント アプリに C# でメッセージを送信する方法について学習します。

## <a name="prerequisites"></a>前提条件

このクイック スタートは、macOS、Windows、または Linux で実行できます。
* [.NET コア SDK](https://www.microsoft.com/net/download/core)
* 任意のテキスト エディターまたはコード エディター。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

サービスのデプロイ中、コードの作成に切り替えましょう。 [GitHub からサンプル アプリ](https://github.com/aspnet/AzureSignalR-samples.git)を複製し、SignalR Service 接続文字列を設定し、アプリケーションをローカルで実行します。

1. git ターミナル ウィンドウを開きます。 サンプル プロジェクトを複製するフォルダーに移動します。

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

このサンプルは、Azure SignalR サービスの使用方法を示すコンソール アプリです。 これには次の 2 つのモードがあります。

- サーバー モード: 単純なコマンドを使用して、Azure SignalR サービスの REST API を呼び出します。
- クライアント モード: Azure SignalR サービスに接続し、サーバーからメッセージを受信します。

また、Azure SignalR サービスで認証するアクセス トークンを生成する方法も記載されています。

### <a name="build-the-executable-file"></a>実行可能ファイルの作成

例として MacOS の osx.10.13 x64 を使用します。 他のプラットフォームで作成する方法については、[こちらを参照](https://docs.microsoft.com/dotnet/core/rid-catalog)してください。
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>クライアントを起動する

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>サーバーを起動する

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>公開せずにサンプルを実行する

次のコマンドを実行して、サーバーまたはクライアントを開始することもできます

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>user-secrets を使用して接続文字列を指定する

サンプルのルート ディレクトリで`dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"`を実行することができます。 その後は、`-c "<ConnectionString>"` オプションが不要になります。

## <a name="usage"></a>使用法

サーバーが起動した後、コマンドを使用してメッセージを送信します

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

別のクライアント名を持つ複数のクライアントを開始できます。


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

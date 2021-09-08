---
title: リファレンス - Azure Web PubSub サービス用 .NET サーバー SDK
description: このリファレンスでは、Azure Web PubSub サービス用 .NET サーバー SDK について説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4bb4c5d90958a31e26c39ce9d0fe7e22bec769e2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114069"
---
# <a name="net-server-sdk-for-azure-web-pubsub-service"></a>Azure Web PubSub サービス用 .NET サーバー SDK

このライブラリを使用すると、次のアクションが行えます。 ここで使われている用語の詳細は、[主な概念](#key-concepts)のセクションを参照してください。

- ハブとグループにメッセージを送信します。 
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、確認します

[ソース コード][code] |
[パッケージ][package] |
[API リファレンス ドキュメント][api] |
[製品ドキュメント](https://aka.ms/awps/doc) |
[サンプル][samples_ref]

## <a name="getting-started"></a>作業の開始
### <a name="install-the-package"></a>パッケージをインストールする

[NuGet](https://www.nuget.org/) からクライアント ライブラリをインストールします:

```PowerShell
dotnet add package Azure.Messaging.WebPubSub --prerelease
```

### <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub サービス インスタンス。

### <a name="authenticate-the-client"></a>クライアントを認証する

サービスと対話するには、WebPubSubServiceClient クラスのインスタンスを作成する必要があります。 これを可能にするには、Microsoft Azure portal でアクセスできる接続文字列またはキーが必要です。

### <a name="create-a-webpubsubserviceclient"></a>認証要求の処理に使用する `WebPubSubServiceClient`

```csharp
var serviceClient = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", new AzureKeyCredential("<access-key>"));
```

## <a name="key-concepts"></a>主要な概念

[!INCLUDE [Termsc](includes/terms.md)]

## <a name="examples"></a>例

### <a name="broadcast-a-text-message-to-all-clients"></a>すべてのクライアントにテキスト メッセージをブロードキャストする

```C# Snippet:WebPubSubHelloWorld
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll("Hello World!");
```

### <a name="broadcast-a-json-message-to-all-clients"></a>すべてのクライアントに JSON メッセージをブロードキャストする

```C# Snippet:WebPubSubSendJson
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll(RequestContent.Create(
        new
        {
            Foo = "Hello World!",
            Bar = 42
        }),
        ContentType.ApplicationJson);
```

### <a name="broadcast-a-binary-message-to-all-clients"></a>すべてのクライアントにバイナリ メッセージをブロードキャストする

```C# Snippet:WebPubSubSendBinary
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

Stream stream = BinaryData.FromString("Hello World!").ToStream();
serviceClient.SendToAll(RequestContent.Create(stream), ContentType.ApplicationOctetStream);
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="setting-up-console-logging"></a>コンソール ログの設定
また、サービスに対して行う要求の詳細を確認する場合は、単に[コンソールのログ記録を有効にする](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)こともできます。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
[code]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/Azure.Messaging.WebPubSub/src
[package]: https://www.nuget.org/packages/Azure.Messaging.WebPubSub
[api]: /dotnet/api/azure.messaging.webpubsub

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]

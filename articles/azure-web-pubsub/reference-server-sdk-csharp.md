---
title: リファレンス - Azure Web PubSub 用 .NET SDK
description: このリファレンスでは、Azure Web PubSub サービス用 .NET SDK について説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: ac940b7760ce94beaca5148bfc4e33a59e14b626
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709787"
---
# <a name="azure-web-pubsub-service-client-library-for-net"></a>.NET 用 Azure Web PubSub サービス クライアント ライブラリ

[Azure Web PubSub サービス](./index.yml)は、開発者がリアルタイムの機能とパブリッシュ-サブスクライブ パターンを使って Web アプリケーションを簡単に作成できるようにするための Azure マネージド サービスです。 サーバーとクライアント間、またはクライアント間で、リアルタイムのパブリッシュ-サブスクライブ メッセージングを必要とするあらゆるシナリオに、Azure Web PubSub サービスを使用できます。 従来のリアルタイム機能は、多くの場合、サーバーからのポーリングや HTTP 要求の送信を必要としますが、そのようなリアルタイム機能にも Azure Web PubSub サービスを使用できます。

次の図に示すように、このライブラリをアプリ サーバー側で使用して、WebSocket クライアント接続を管理できます。

![このオーバーフロー図は、サービス クライアント ライブラリを使用するときのオーバーフローを示しています。](media/sdk-reference/service-client-overflow.png)

このライブラリは次のことに使うことができます。
- ハブとグループにメッセージを送信します。 
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、確認します

ここで使われている用語の詳細は、[主な概念](#key-concepts)のセクションを参照してください。
 
[ソース コード](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/src) |
[パッケージ](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) |
[API リファレンス ドキュメント]() |
[製品ドキュメント](./index.yml) |
[サンプル][samples_ref]

## <a name="getting-started"></a>作業の開始

### <a name="install-the-package"></a>パッケージをインストールする

[NuGet](https://www.nuget.org/) からクライアント ライブラリをインストールします:

```dotnetcli
dotnet add package Azure.Messaging.WebPubSub
```

### <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub サービス インスタンス。

### <a name="create-and-authenticate-a-webpubsubserviceclient"></a>`WebPubSubServiceClient` を作成して認証する

サービスと対話するには、`WebPubSubServiceClient` クラスのインスタンスを作成する必要があります。 これを可能にするには、Microsoft Azure portal でアクセスできる接続文字列またはキーが必要です。

```C# Snippet:WebPubSubAuthenticate
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));
```

## <a name="key-concepts"></a>主要な概念

### <a name="connection"></a>Connection

接続は、クライアントまたはクライアント接続とも呼ばれ、Web PubSub サービスに接続されている個々の WebSocket 接続を表します。 正常に接続されると、一意の接続 ID が Web PubSub サービスによってこの接続に割り当てられます。

### <a name="hub"></a>ハブ

ハブは、一連のクライアント接続の論理的な概念です。 通常、"チャット" ハブや "通知" ハブなど、1 つの目的に 1 つのハブを使います。 クライアント接続は、作成されると 1 つのハブに接続され、その有効期間中はそのハブに属します。 異なるアプリケーションでは、異なるハブ名を使用して、1 つの Azure Web PubSub サービスを共有できます。

### <a name="group"></a>グループ

グループはハブへの接続のサブセットです。 必要な場合はいつでも、クライアント接続をグループに追加したり、グループからクライアント接続を削除したりできます。 たとえば、クライアントがチャット ルームに参加したり、クライアントがチャット ルームを離れたりするときに、このチャット ルームをグループと見なすことができます。 クライアントは複数のグループに参加できます。また、1 つのグループに複数のクライアントを含めることもできます。

### <a name="user"></a>User

Web PubSub への接続を 1 人のユーザーに属させることができます。 1 人のユーザーが複数のデバイスまたは複数のブラウザー タブに接続されている場合など、ユーザーが複数の接続を持つ場合があります。

### <a name="message"></a>Message

クライアントが接続されている場合は、WebSocket 接続を介して、アップストリーム アプリケーションにメッセージを送信したり、アップストリーム アプリケーションからメッセージを受信したりできます。

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
また、サービスに対して行う要求の詳細を確認する場合は、[コンソールのログ記録を簡単に有効にする](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/core/Azure.Core/samples/Diagnostics.md#logging)こともできます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]


[azure_sub]: https://azure.microsoft.com/free/dotnet/
[samples_ref]: https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/tests/Samples/
[awps_sample]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
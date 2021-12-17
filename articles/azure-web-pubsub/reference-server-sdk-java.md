---
title: リファレンス - Azure Web PubSub 用の Java SDK
description: このリファレンスでは、Azure Web PubSub サービス用 Java SDK について説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: f0e1017cacf2d556ed9b61a90a9881653a7ca336
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714791"
---
# <a name="azure-web-pubsub-service-client-library-for-java"></a>Java 用 Azure Web PubSub サービス クライアント ライブラリ

[Azure Web PubSub](./index.yml) サービスは、開発者がリアルタイムの機能と発行-サブスクライブ パターンを利用して Web アプリケーションを簡単に作成できるようにするための Azure マネージド サービスです。 サーバーとクライアント間、またはクライアント間で、リアルタイムのパブリッシュ-サブスクライブ メッセージングを必要とするあらゆるシナリオに、Azure Web PubSub サービスを使用できます。 従来のリアルタイム機能は、多くの場合、サーバーからのポーリングや HTTP 要求の送信を必要としますが、そのようなリアルタイム機能にも Azure Web PubSub サービスを使用できます。

次の図に示すように、このライブラリをアプリ サーバー側で使用して、WebSocket クライアント接続を管理できます。

![このオーバーフロー図は、サービス クライアント ライブラリを使用したのオーバーフローを示しています。](media/sdk-reference/service-client-overflow.png)

このライブラリは次のことに使うことができます。
- ハブとグループにメッセージを送信します。 
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、確認します

ここで使われている用語の詳細は、[主な概念](#key-concepts)のセクションを参照してください。

[ソース コード][source_code] | [API リファレンス ドキュメント][api] | [製品ドキュメント][product_documentation] | [サンプル][samples_readme]

## <a name="getting-started"></a>作業の開始

### <a name="prerequisites"></a>前提条件

- [Java Development Kit (JDK)][jdk_link] バージョン 8 以降。
- [Azure サブスクリプション][azure_subscription]

### <a name="include-the-package"></a>パッケージをインクルードする

[//]: # ({x-version-update-start;com.azure:azure-messaging-webpubsub;current})

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-webpubsub</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-webpubsubserviceclient-using-connection-string"></a>接続文字列を使用して `WebPubSubServiceClient` を作成する

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L21-L24 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-webpubsubserviceclient-using-access-key"></a>アクセス キーを使用して `WebPubSubServiceClient` を作成する

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L31-L35 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
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

* [ハブ全体にメッセージをブロードキャストする](#broadcast-all "ハブ全体にメッセージをブロードキャストする")
* [グループにメッセージをブロードキャストする](#broadcast-group "グループにメッセージをブロードキャストする")
* [接続にメッセージを送信する](#send-to-connection "接続にメッセージを送信する")
* [ユーザーにメッセージを送信する](#send-to-user "ユーザーにメッセージを送信する")

<a name="broadcast-all"></a>

### <a name="broadcast-message-to-entire-hub"></a>ハブ全体にメッセージをブロードキャストする

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L47-L47 -->
```java
webPubSubServiceClient.sendToAll("Hello world!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="broadcast-group"></a>

### <a name="broadcast-message-to-a-group"></a>グループにメッセージをブロードキャストする

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L59-L59 -->
```java
webPubSubServiceClient.sendToGroup("java", "Hello Java!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-connection"></a>

### <a name="send-message-to-a-connection"></a>接続にメッセージを送信する

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L71-L71 -->
```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-user"></a>

### <a name="send-message-to-a-user"></a>ユーザーにメッセージを送信する
<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L83-L83 -->
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!", WebPubSubContentType.TEXT_PLAIN);
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="enable-client-logging"></a>クライアントのログ記録を有効にする
クライアント ライブラリで実行されたログ記録ステートメントは、`AZURE_LOG_LEVEL` 環境変数を設定することで表示できます。 たとえば、`AZURE_LOG_LEVEL=2` と設定すると、情報、警告、エラーのすべてのログ メッセージが表示されます。 ログ レベルについては、こちらの[ログ レベル][log_levels]に関するページを参照してください。

### <a name="default-http-client"></a>既定の HTTP クライアント
すべてのクライアント ライブラリでは、Netty HTTP クライアントが既定で使用されます。 前述の依存関係を追加すると、Netty HTTP クライアントを使用するようにクライアント ライブラリが自動的に構成されます。 HTTP クライアントの構成と変更については、[HTTP クライアントの Wiki](https://github.com/Azure/azure-sdk-for-java/wiki/HTTP-clients) で詳しく説明されています。

### <a name="default-ssl-library"></a>既定の SSL ライブラリ
すべてのクライアント ライブラリは、Tomcat ネイティブの Boring SSL ライブラリを既定で使用して、SSL 操作にネイティブレベルのパフォーマンスを実現しています。 Boring SSL ライブラリは、Linux、macOS、Windows のネイティブ ライブラリを含んだ uber jar であり、JDK 内の既定の SSL 実装よりも優れたパフォーマンスを備えています。 依存関係のサイズを縮小する方法など、詳細については、Wiki の「[パフォーマンス チューニング][performance_tuning]」セクションを参照してください。

[!INCLUDE [next step](includes/include-next-step.md)]


<!-- LINKS -->

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: ./index.yml
[samples_readme]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/webpubsub/azure-messaging-webpubsub/src/samples/README.md
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[cla]: https://cla.microsoft.com
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[api]::
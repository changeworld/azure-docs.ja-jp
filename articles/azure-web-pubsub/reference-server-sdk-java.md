---
title: リファレンス - Azure Web PubSub サービス用 Java サーバー SDK
description: このリファレンスでは、Azure Web PubSub サービス用 Java サーバー SDK について説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: b36bf3715458fdf66d8c1a113e96e048bd414d0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113426"
---
# <a name="java-server-sdk-for-azure-web-pubsub-service"></a>Azure Web PubSub サービス用 Java サーバー SDK

このライブラリの用途は次のとおりです。

- ハブとグループにメッセージを送信します。
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、チェックします

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
    <version>1.0.0-beta.2</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-web-pubsub-client-using-connection-string"></a>接続文字列を使用して Web PubSub クライアントを作成する

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-client-using-access-key"></a>アクセス キーを使用して Web PubSub クライアントを作成する

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-group-client"></a>Web PubSub グループ クライアントを作成する
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .hub("chat")
    .buildClient();
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("java");
```

## <a name="key-concepts"></a>主要な概念

[!INCLUDE [Terms](includes/terms.md)]


## <a name="examples"></a>例

### <a name="broadcast-message-to-entire-hub"></a>ハブ全体にメッセージをブロードキャストする

```java
webPubSubServiceClient.sendToAll("Hello world!");
```

### <a name="broadcast-message-to-a-group"></a>グループにメッセージをブロードキャストする

```java
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("Java");
javaGroup.sendToAll("Hello Java!");
```

### <a name="send-message-to-a-connection"></a>接続にメッセージを送信する

```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!");
```

### <a name="send-message-to-a-user"></a>ユーザーにメッセージを送信する
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!");
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="enable-client-logging"></a>クライアントのログ記録を有効にする
クライアント ライブラリで実行されたログ記録ステートメントは、`AZURE_LOG_LEVEL` 環境変数を設定することで表示できます。 たとえば、`AZURE_LOG_LEVEL=2` と設定すると、情報、警告、エラーのすべてのログ メッセージが表示されます。 ログ レベルについては、こちらの[ログ レベル][log_levels]に関するページを参照してください。

### <a name="default-http-client"></a>既定の HTTP クライアント
すべてのクライアント ライブラリでは、Netty HTTP クライアントが既定で使用されます。 前述の依存関係を追加すると、Netty HTTP クライアントを使用するようにクライアント ライブラリが自動的に構成されます。 HTTP クライアントの構成と変更については、[HTTP クライアントの Wiki](/azure/developer/java/sdk/http-client-pipeline) で詳しく説明されています。

### <a name="default-ssl-library"></a>既定の SSL ライブラリ
すべてのクライアント ライブラリは、Tomcat ネイティブの Boring SSL ライブラリを既定で使用して、SSL 操作にネイティブレベルのパフォーマンスを実現しています。 Boring SSL ライブラリは、Linux、macOS、Windows のネイティブ ライブラリを含んだ uber jar であり、JDK 内の既定の SSL 実装よりも優れたパフォーマンスを備えています。 依存関係のサイズを縮小する方法など、詳細については、Wiki の「[パフォーマンス チューニング][performance_tuning]」セクションを参照してください。

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[api]: /java/api/com.azure.messaging.webpubsub

## <a name="next-steps"></a>次の手順

[!INCLUDE [next step](includes/include-next-step.md)]

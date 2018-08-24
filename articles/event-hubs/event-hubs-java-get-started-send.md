---
title: Java を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs
description: Java を使用して Event Hubs への送信を開始する
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 05/30/2018
ms.author: shvija
ms.openlocfilehash: 6217f507b83325acb9a5062aada150fa6f8bc5d2
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007564"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Java を使用して Azure Event Hubs にイベントを送信する

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して変換と格納を実行できます。

詳細については、「[Event Hubs の概要][Event Hubs overview]」をご覧ください。

このチュートリアルでは、Java のコンソール アプリケーションを使用して、イベント ハブへのイベントを送信する方法について説明します。 Java のイベント プロセッサ ホスト ライブラリを使用してイベントを受信するには、[この記事](event-hubs-java-get-started-receive-eph.md)を参照するか、左側の目次で適切な受信側の言語をクリックしてください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* Java 開発環境。 このチュートリアルでは、[Eclipse](https://www.eclipse.org/) を使用します。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][]を作成してください。

このチュートリアルのコードは [SimpleSend GitHub のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend)に基づくものであり、完全に動作するアプリケーションを表示する場合に確認することができます。

## <a name="send-events-to-event-hubs"></a>イベントを Event Hubs に送信する

[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) の Maven プロジェクトで Event Hub 用の Java クライアント ライブラリを使用できます。 Maven プロジェクト ファイル内で次の依存関係宣言を使用して、このライブラリを参照できます。 現行バージョンは 1.0.1 です。    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.1</version>
</dependency>
```

ビルド環境の種類に応じて、[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) から最新リリースの JAR ファイルを明示的に取得できます。  

単純なイベント パブリッシャーの場合は、Event Hubs クライアント クラス用の *com.microsoft.azure.eventhubs* パッケージと、Azure Service Bus メッセージング クライアントと共有される一般的な例外などのユーティリティ クラス用の *com.microsoft.azure.servicebus* パッケージをインポートします。 

### <a name="declare-the-send-class"></a>Send クラスを宣言する

次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 クラスに `SimpleSend` という名前を付けます。     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>接続文字列を作成する

ConnectionStringBuilder クラスを使用して、Event Hubs クライアント インスタンスに渡す接続文字列の値を作成します。 プレースホルダーは、名前空間とイベント ハブの作成時に取得した値に置き換えます。

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>送信イベント

文字列を UTF-8 バイト エンコードに変換して単数のイベントを作成します。 その後、接続文字列から新しい Event Hubs クライアント インスタンスを作成し、メッセージを送信します。   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください:

* [EventProcessorHost を使用してイベントを受信する](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs の概要][Event Hubs overview]
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio


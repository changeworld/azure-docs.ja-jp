---
title: Azure Monitor Application Insights Java
description: コードを変更することなく、任意の環境で実行されている Java アプリケーションのアプリケーション パフォーマンスを監視します。 分散トレースとアプリケーション マップです。
ms.topic: conceptual
ms.date: 06/24/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 83cccb8e586973cf575cf1fcd3c70a3166aeb905
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913727"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Azure Monitor Application Insights を監視する Java のコード不要のアプリケーション

> [!NOTE]
> 古い 2.x ドキュメントを探している場合は、[こちら](./java-2x-get-started.md) を参照してください。

Java のコード不要のアプリケーション監視は、シンプルさがすべてです。コードの変更がなく、いくつかの構成変更を行うだけで、Java エージェントを有効にすることができます。

Java エージェントは、任意の環境で動作し、すべての Java アプリケーションを監視できます。 つまり、Java アプリを実行しているのが、VM、オンプレミス、AKS、Windows、Linux などのいずれであっても、Application Insights Java エージェントがアプリを監視します。

Application Insights Java 3.x エージェントによって要求、依存関係、ログがすべて独自に自動収集されるため、アプリケーションに Application Insights Java 2.x SDK を追加する必要がなくなりました。

アプリケーションからカスタム テレメトリを送信することはできます。
3\.x エージェントによって、自動収集されたすべてのテレメトリと連動して、追跡と関連付けが行われます。

3\.x エージェントは、Java 8 以降をサポートしています。

## <a name="quickstart"></a>クイック スタート

**1.エージェントのダウンロード**

> [!WARNING]
> **3.0 Preview からアップグレードする場合**
>
> ファイル名自体がすべて小文字になったのに加えて、JSON 構造体が完全に変更されたため、すべての[構成オプション](./java-standalone-config.md)を注意深く確認してください。

> [!WARNING]
> **3.0.x からアップグレードする場合**
>
> 操作名と要求テレメトリ名の先頭には、http メソッド (`GET`、`POST` など) が付けられます。
> これは、プレフィックスが付いていない以前の値に依存している場合には、カスタム ダッシュボードまたは警告に影響する可能性があります。
> 詳細については、[3.1.0 のリリース ノート](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0) を参照してください。

[applicationinsights-agent-3.1.1.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.1.1/applicationinsights-agent-3.1.1.jar) をダウンロードする

**2.JVM をエージェントにポイントする**

アプリケーションの JVM 引数に `-javaagent:path/to/applicationinsights-agent-3.1.1.jar` を追加します。 

アプリケーションの JVM 引数の構成に関するヘルプについては、[JVM の引数の更新に関するヒント](./java-standalone-arguments.md)のページを参照してください。

**3.エージェントを Application Insights リソースにポイントする**

Application Insights リソースをまだ持っていない場合は、[リソース作成ガイド](./create-new-resource.md)の手順に従って、新しいリソースを作成できます。

環境変数を設定して、エージェントを Application Insights リソースにポイントします。

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

または、次の内容で、`applicationinsights.json` という名前の構成ファイルを作成し、`applicationinsights-agent-3.1.1.jar` と同じディレクトリに配置します。

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

接続文字列は、Application Insights リソースで確認できます。

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights の接続文字列":::

**4.これで完了です。**

次に、アプリケーションを起動し、Azure portal の Application Insights リソースにアクセスして、監視データを表示します。

> [!NOTE]
> 監視データがポータルに表示されるまで、数分かかる場合があります。


## <a name="configuration-options"></a>構成オプション

`applicationinsights.json` ファイルでは、次の構成を追加できます。

* クラウド ロール名
* クラウド ロール インスタンス
* サンプリング
* JMX メトリック
* カスタム ディメンション
* テレメトリ プロセッサ (プレビュー)
* 自動収集されるログ
* 自動収集される Micrometer メトリック (Spring Boot アクチュエータ メトリックを含む)
* Heartbeat
* HTTP Proxy
* 自己診断

詳細については、[構成オプション](./java-standalone-config.md)に関するページを参照してください。

## <a name="auto-collected-requests"></a>自動収集される要求

* JMS コンシューマー
* Kafka コンシューマー
* Netty/WebFlux
* Servlets
* Spring スケジュール

## <a name="auto-collected-dependencies"></a>自動収集される依存関係

自動収集される依存関係とダウンストリームへの分散トレースの伝達:

* Apache HttpClient と HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Netty クライアント
* OkHttp

自動収集される依存関係 (ダウンストリームへの分散トレースの伝達なし):

* Cassandra
* JDBC
* MongoDB (非同期および同期)
* Redis (Lettuce および Jedis)

## <a name="auto-collected-logs"></a>自動収集されるログ

* java.util.logging
* Log4j (MDC プロパティを含む)
* SLF4J/Logback (MDC プロパティを含む)

## <a name="auto-collected-metrics"></a>自動収集されるメトリック

* マイクロメーター (Spring Boot アクチュエータ メトリックを含む)
* JMX メトリック

## <a name="azure-sdks-preview"></a>Azure SDKs (プレビュー)

このプレビュー機能を有効にし、次の Azure SDK から送信されるテレメトリを自動収集するための[構成オプション](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview)をご覧ください。

* [App Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Cognitive Search](/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Communication Chat](/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Communication Identity](/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Communication Phone Numbers](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0+
* [Communication SMS](/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Digital Twins - Core](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0+
* [Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs - Azure Blob Storage チェックポイント ストア](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Form Recognizer](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identity](/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault - 証明書](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault - キー](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault - シークレット](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Storage - Blobs](/java/api/overview/azure/storage-blob-readme) 12.11.0+
* [Storage - Blobs Batch](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0+
* [Storage - Blobs Cryptography](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0+
* [Storage - Common](/java/api/overview/azure/storage-common-readme) 12.11.0+
* [Storage - Files Data Lake](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0+
* [Storage - Files Shares](/java/api/overview/azure/storage-file-share-readme) 12.9.0+
* [Storage - Queues](/java/api/overview/azure/storage-queue-readme) 12.9.0+
* [Text Analytics](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "上記の名前とリンクは https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # " から集めたものです。バージョンは azure-core 1.14.0 で構築された maven central の最も古いバージョンに対して手動で同期されています"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    continue"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version + '\n'"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>アプリケーションからカスタム テレメトリを送信する

Application Insights Java 3.x での目標は、標準 API を使用してカスタム テレメトリを送信できるようにすることです。

現時点では、Micrometer、一般的なログ記録フレームワーク、および Application Insights Java 2.x SDK をサポートしています。
Application Insights Java 3.x を使用すると、これらの API を使用して送信されたテレメトリが自動的にキャプチャされ、自動収集されたテレメトリと関連付けられます。

### <a name="supported-custom-telemetry"></a>サポートされているカスタム テレメトリ

次の表は、現在サポートされているカスタム テレメトリの種類を示しています。これを有効にすると、Java 3.x エージェントを補完することができます。 要約すると、カスタム メトリックはマイクロメーターを通じてサポートされ、カスタムの例外とトレースはログ記録フレームワークを使用して有効にできます。また、カスタム テレメトリの種類は、[Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk) を通じてサポートされます。

|                     | Micrometer | Log4j、logback、JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **[カスタム イベント]**   |            |                     |  Yes    |
| **カスタム メトリック**  |  はい       |                     |  はい    |
| **依存関係**    |            |                     |  Yes    |
| **例外**      |            |  はい                |  はい    |
| **ページ ビュー**      |            |                     |  はい    |
| **要求**        |            |                     |  Yes    |
| **トレース**          |            |  はい                |  はい    |

現時点では、Application Insights 3.x を使用した SDK をリリースする予定はありません。

Application Insights Java 3.x では、Application Insights Java 2.x SDK に送信されるテレメトリを既にリッスンしています。 この機能は、既存の 2.x ユーザーのアップグレード ストーリーの重要な部分であり、OpenTelemetry API が GA になるまでの、カスタム テレメトリ サポートの重要なギャップを埋めるものです。

### <a name="send-custom-metrics-using-micrometer"></a>Micrometer を使用してカスタム　メトリックを送信する

アプリケーションに Micrometer を追加します。

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Micrometer の[グローバル レジストリ](https://micrometer.io/docs/concepts#_global_registry)を使用して、メーターを作成します。

```java
static final Counter counter = Metrics.counter("test_counter");
```

次に、それを使用してメトリックを記録します。

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>お気に入りのログ記録フレームワークを使用してカスタム トレースと例外を送信する

Log4j、Logback、java.util.logging は自動的にインストルメント化され、これらのログ記録フレームワークを介して実行されるログは、トレースおよび例外テレメトリとして自動収集されます。

既定では、INFO レベル以上でログ記録が実行された場合にのみ、ログが収集されます。
このレベルを変更する方法については、[構成オプション](./java-standalone-config.md#auto-collected-logging)に関する記事を参照してください。

カスタム ディメンションをログに添付する場合は、[Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html)、[Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)、または [Logback MDC](http://logback.qos.ch/manual/mdc.html) を使用できます。また、Application Insights Java 3.x を使用すると、トレースおよび例外テレメトリでそれらの MDC プロパティがカスタム ディメンションとして自動的にキャプチャされます。

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>2\.x SDK を使用してカスタム テレメトリを送信する

アプリケーションに `applicationinsights-core-2.6.3.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.x でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.3</version>
</dependency>
```

TelemetryClient を作成します。

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

次に、それを使用して、カスタム テレメトリを送信します。

##### <a name="events"></a>events

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>メトリック

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>依存関係

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>ログ

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>例外

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>2\.x SDK を使用して要求カスタム ディメンションを追加する

> [!NOTE]
> この機能は、3.0.2 以降にのみ存在します

アプリケーションに `applicationinsights-web-2.6.3.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.x でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

コードにカスタム ディメンションを追加します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>2\.x SDK を使用して要求テレメトリ user_Id 設定する

> [!NOTE]
> この機能は、3.0.2 以降にのみ存在します

アプリケーションに `applicationinsights-web-2.6.3.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.x でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

コードに `user_Id` を設定します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>2\.x SDK を使用して要求テレメトリ名をオーバーライドする

> [!NOTE]
> この機能は、3.0.2 以降にのみ存在します

アプリケーションに `applicationinsights-web-2.6.3.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.x でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

コードに名前を設定します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>2\.x SDK を使用して要求テレメトリ ID と操作 ID を取得します。

> [!NOTE]
> この機能は、3.0.3 以降にのみ存在します

アプリケーションに `applicationinsights-web-2.6.3.jar` を追加します (すべての 2.x バージョンは Application Insights Java 3.x でサポートされていますが、選択が可能な場合は、最新のバージョンを使用することをお勧めします)。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

次のコードで、要求テレメトリ ID と操作 ID を取得します。

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```

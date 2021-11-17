---
title: Azure Monitor Application Insights Java
description: コードを変更することなく、任意の環境で実行されている Java アプリケーションのアプリケーション パフォーマンスを監視します。 分散トレースとアプリケーション マップです。
ms.topic: conceptual
ms.date: 06/24/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 6fbc2c38431cc638873ed4a93ce80f16d789c1de
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158954"
---
# <a name="azure-monitor-opentelemetry-based-auto-instrumentation-for-java-applications"></a>Java アプリケーションのための Azure Monitor OpenTelemetry ベースの自動インストルメンテーション

この記事では、OpenTelemetry ベースの Azure Monitor Java オファリングを有効にして構成する方法について説明します。 この記事の手順を完了すると、Azure Monitor Application Insights を使用してアプリケーションを監視できるようになります。

## <a name="get-started"></a>はじめに
Java 自動インストルメンテーションは、コード変更なしで有効にできます。

### <a name="prerequisites"></a>必須コンポーネント
- Java 8 以上を使用する Java アプリケーション
- Azure サブスクリプション - [Azure サブスクリプションを無料で作成する](https://azure.microsoft.com/free/)
- Application Insights リソース - [Application Insights リソースを作成する](create-workspace-resource.md#create-workspace-based-resource)

### <a name="enable-azure-monitor-application-insights"></a>Azure Monitor Application Insights を有効にする
**1. 自動インストルメンテーション jar ファイルをダウンロードする**

#### <a name="1-download-jar-file"></a>1. jar ファイルをダウンロードする

[applicationinsights-agent-3.2.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.3/applicationinsights-agent-3.2.3.jar) ファイルをダウンロードします。

> [!WARNING]
> 
> - **3.0 プレビューからアップグレードしている場合**
>
>    すべての[構成オプション](./java-standalone-config.md)を慎重に確認します。 Json 構造体は完全に変更されました。 ファイル名はすべて小文字になりました。
> 
> - **3.0.x からアップグレードしている場合**
> 
>    操作名と要求テレメトリ名の先頭には、http メソッド (`GET`、`POST` など) が付けられます。
>    この変更は、以前の値に依存しているカスタム ダッシュボードまたはアラートに影響を与える場合があります。
>    詳細については、[3.1.0 のリリース ノート](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0)を参照してください。
>
> - **3.1.x からアップグレードしている場合**
> 
>    完全な (サニタイズされた) クエリは変わらず `data` フィールド内に存在しながら、データベースの依存関係名がより簡潔になりました。 HTTP の依存関係名がよりわかりやすいものになっています。
>    この変更は、以前の値に依存しているカスタム ダッシュボードまたはアラートに影響を与える場合があります。
>    詳細については、[3.2.0 のリリース ノート](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0)を参照してください。

#### <a name="2-point-the-jvm-to-the-jar-file"></a>2. JVM が jar ファイルを指すようにする

アプリケーションの JVM 引数に `-javaagent:path/to/applicationinsights-agent-3.2.3.jar` を追加します。 

> [!TIP]
> アプリケーションの JVM 引数の構成に関するヘルプについては、[JVM の引数の更新に関するヒント](./java-standalone-arguments.md)のページを参照してください。

#### <a name="3-set-application-insights-connection-string"></a>3. Application Insights の接続文字列を設定する

jar ファイルが Application Insights リソースを指すように、次の環境変数を設定します。

```console
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

または、次の内容で、`applicationinsights.json` という名前の構成ファイルを作成し、`applicationinsights-agent-3.2.3.jar` と同じディレクトリに配置します。

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Application Insights リソースの接続文字列を見つけます。

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights の接続文字列":::

#### <a name="4-confirm-data-is-flowing"></a>4. データが流れていることを確認する

アプリケーションを実行し、Azure portal で [Application Insights リソース] タブを開きます。 ポータルにデータが表示されるまでに数分かかることがあります。

> [!NOTE]
> アプリケーションを実行できない場合、またはデータが期待どおりに取得されない場合は、「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="サーバー要求とサーバー応答時間が強調表示されている [Application Insights の概要] タブのスクリーンショット。":::

> [!IMPORTANT]
> 同じ Application Insights リソースにテレメトリを出力しているサービスが 2 つ以上ある場合は、アプリケーション マップ上で正しく表すために、[クラウド ロール名を設定する](java-standalone-config.md#cloud-role-name)必要があります。

> [!NOTE]
> Application Insights インストルメンテーションの使用時に、診断データが収集され、Microsoft に送信されます。 このデータは、Application Insights の実行と改善に役立ちます。 重要でないデータ収集を無効にするオプションがあります。 [詳細については、こちらを参照してください](./statsbeat.md)。

## <a name="configuration-options"></a>構成オプション

`applicationinsights.json` ファイルでは、以下の設定を構成することができます。

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

## <a name="instrumentation-libraries"></a>インストルメンテーション ライブラリ

Java 3.X には、次のインストルメンテーション ライブラリが含まれています。

### <a name="auto-collected-requests"></a>自動収集される要求

* JMS コンシューマー
* Kafka コンシューマー
* Netty/WebFlux
* Servlets
* Spring スケジュール

### <a name="auto-collected-dependencies"></a>自動収集される依存関係

自動収集される依存関係とダウンストリームへの分散トレースの伝達:

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS Client
* Jetty HttpClient
* JMS
* Kafka
* Netty クライアント
* OkHttp

自動収集される依存関係 (ダウンストリームへの分散トレースの伝達なし):

* Cassandra
* JDBC
* MongoDB (非同期および同期)
* Redis (Lettuce および Jedis)

### <a name="auto-collected-logs"></a>自動収集されるログ

* java.util.logging
* Log4j (MDC プロパティを含む)
* SLF4J/Logback (MDC プロパティを含む)

### <a name="auto-collected-metrics"></a>自動収集されるメトリック

* マイクロメーター (Spring Boot アクチュエータ メトリックを含む)
* JMX メトリック

### <a name="azure-sdks"></a>Azure SDK

次の Azure SDK によって出力されるテレメトリは、既定で自動収集されます。

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

## <a name="modify-telemetry"></a>テレメトリの変更

### <a name="add-span-attributes"></a>スパン属性を追加する
`opentelemetry-api` を使用して、スパンに属性を追加することができます。 これらの属性には、テレメトリへのカスタム ビジネス ディメンションの追加を含めることができます。 また、属性を使用して、Application Insights のスキーマの省略可能なフィールド ([ユーザー ID] や [クライアント IP] など) を設定することもできます。

#### <a name="add-custom-dimension"></a>カスタム ディメンションを追加する
1 つ以上のカスタム ディメンションを追加すると、要求、依存関係、または例外テーブル内の _customDimensions_ フィールドにデータが入力されます。

> [!NOTE]
> この機能は、3.2.0 以降でのみ利用できます。

アプリケーションに `opentelemetry-api-1.6.0.jar` を追加します:

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

コードにカスタム ディメンションを追加します:

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("mycustomdimension", "myvalue1");
```

#### <a name="set-user-id"></a>ユーザー ID を設定する
要求、依存関係、または例外テーブルの [ユーザー ID] フィールドにデータを入力します。

> [!IMPORTANT]
> 認証されたユーザー ID を設定する前に、該当するプライバシーに関する法律を調べてください。

> [!NOTE]
> この機能は、3.2.0 以降でのみ利用できます。

アプリケーションに `opentelemetry-api-1.6.0.jar` を追加します:

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

コードに `user_Id` を設定します。

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("enduser.id", "myuser");
```

### <a name="get-trace-id-or-span-id"></a>トレース ID またはスパン ID を取得する

`opentelemetry-api` を使用して、トレース ID またはスパン ID を取得することができます。 このアクションは、これらの識別子を既存のログ テレメトリに追加して、問題のデバッグや診断時の相関関係を改善するために行われる場合があります。

> [!NOTE]
> この機能は、3.2.0 以降でのみ利用できます。

アプリケーションに `opentelemetry-api-1.6.0.jar` を追加します:

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

コードで要求のトレース ID とスパン ID を取得します。

```java
import io.opentelemetry.api.trace.Span;

String traceId = Span.current().getSpanContext().getTraceId();
String spanId = Span.current().getSpanContext().getSpanId();
```

## <a name="custom-telemetry"></a>カスタムのテレメトリ

Application Insights Java 3.x での目標は、標準 API を使用してカスタム テレメトリを送信できるようにすることです。

現時点では、Micrometer、一般的なログ記録フレームワーク、および Application Insights Java 2.x SDK をサポートしています。 Application Insights Java 3.x を使用すると、これらの API を使用して送信されたテレメトリが自動的にキャプチャされ、自動収集されたテレメトリと関連付けられます。

### <a name="supported-custom-telemetry"></a>サポートされているカスタム テレメトリ

次の表は、現在サポートされているカスタム テレメトリの種類を示しています。これを有効にすると、Java 3.x エージェントを補完することができます。 要約すると、カスタム メトリックはマイクロメーターを通してサポートされ、カスタムの例外とトレースはログ記録フレームワーク、カスタム要求、依存関係を通して有効にすることができます。また、例外は `opentelemetry-api` を通して有効にすることができ、すべての種類のカスタム テレメトリが [Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk) を通してサポートされます。

|                     | Micrometer | Log4j、logback、JUL | 2.x SDK | opentelemetry-api |
|---------------------|------------|---------------------|---------|-------------------|
| **[カスタム イベント]**   |            |                     |  Yes    |                   |
| **カスタム メトリック**  |  はい       |                     |  はい    |                   |
| **依存関係**    |            |                     |  Yes    |  Yes              |
| **例外**      |            |  はい                |  はい    |  はい              |
| **ページ ビュー**      |            |                     |  はい    |                   |
| **要求**        |            |                     |  Yes    |  Yes              |
| **トレース**          |            |  はい                |  はい    |                   |

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

カウンターを使用してメトリックを記録します。

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>お気に入りのログ記録フレームワークを使用してカスタム トレースと例外を送信する

Log4j、Logback、java.util.logging は自動的にインストルメント化され、これらのログ記録フレームワークを介して実行されるログは、トレースおよび例外テレメトリとして自動収集されます。

既定では、INFO レベル以上でログ記録が実行された場合にのみ、ログが収集されます。
このレベルを変更するには、[構成オプション](./java-standalone-config.md#auto-collected-logging)に関する記事を参照してください。

カスタム ディメンションをログにアタッチする場合は、[Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html)、[Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)、または [Logback MDC](http://logback.qos.ch/manual/mdc.html) を使用できます。 Application Insights Java 3.x では、これらの MDC プロパティがトレースおよび例外テレメトリのカスタム ディメンションとして自動的にキャプチャされます。

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

クライアントを使用してカスタム テレメトリを送信します。

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

## <a name="troubleshooting"></a>トラブルシューティング

[トラブルシューティング](java-standalone-troubleshoot.md)に関する記事を参照してください。

## <a name="support"></a>サポート
- [トラブルシューティング手順](java-standalone-troubleshoot.md)を確認してください。
- Azure サポートの問題については、[Azure サポート チケット](https://azure.microsoft.com/support/create-ticket/)を開いてください。
- OpenTelemetry の問題については、[OpenTelemetry コミュニティ](https://opentelemetry.io/community/)に直接問い合わせてください。

## <a name="opentelemetry-feedback"></a>OpenTelemetry のフィードバック
- OpenTelemetry コミュニティの[カスタマー フィードバック アンケート](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform)に記入してください。
- Microsoft の [OpenTelemetry 早期導入者コミュニティ](https://aka.ms/AzMonOTel/)に参加することにより、お客様自身のことを少しお知らせください。
- [Microsoft の 技術コミュニティ](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)で他の Azure Monitor ユーザーと交流してください。

## <a name="next-steps"></a>次の手順

- [Azure Monitor Java 自動インストルメンテーション GitHub リポジトリ](https://github.com/Microsoft/ApplicationInsights-Java)でソース コードを確認します。
- OpenTelemetry とそのコミュニティの詳細については、[OpenTelemetry Java GitHub リポジトリ](https://github.com/open-telemetry/opentelemetry-java-instrumentation)を参照してください。
- 使用法エクスペリエンスを有効にするために、[Web またはブラウザーのユーザー監視を有効にします](javascript.md)。

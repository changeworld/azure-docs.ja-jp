---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: jbeauregardb
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: jbeauregardb
ms.openlocfilehash: 8de03cce67b5eba27d771fbc95d538366724481b
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292432"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../create-communication-resource.md)。
- Azure portal で、[Application Insights リソース](../../../azure-monitor/app/create-new-resource.md)を作成します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開きます。 Java アプリケーションを作成するディレクトリに移動します。 次のコマンドを実行して、maven-archetype-quickstart テンプレートから Java プロジェクトを生成します。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

"generate" タスクによって、`artifactId` と同じ名前のディレクトリが作成されたことがわかります。 このディレクトリの下の src/main/java ディレクトリにはプロジェクトのソース コードが含まれており、 `src/test/java directory` にはテスト ソースが含まれており、`pom.xml` ファイルはプロジェクトのプロジェクト オブジェクト モデル (POM) です。

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで **pom.xml** ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-monitor-opentelemetry-exporter</artifactId>
      <version>1.0.0-beta.4</version>
    </dependency>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. */src/main/java/com/communication/quickstart* ディレクトリに移動します
1. 使用しているエディターで *App.java* ファイルを開きます
1. `System.out.println("Hello world!");` ステートメントを置き換えます
1. `import` ディレクティブを追加します

次のコードを使用して開始します。

```java
package com.communication.quickstart;

import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.Tracer;
import io.opentelemetry.context.Scope;
import io.opentelemetry.sdk.OpenTelemetrySdk;
import io.opentelemetry.sdk.trace.SdkTracerProvider;
import io.opentelemetry.sdk.trace.export.SimpleSpanProcessor;
import com.azure.monitor.opentelemetry.exporter.*;
import com.azure.communication.identity.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Export Telemetry to Application Insights");
        // Quickstart code goes here
    }
}
```
## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>Communication ID SDK 呼び出しを使用したテレメトリ トレーサーの設定

接続文字列を使用して `CommunicationIdentityClient` を初期化します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

```java

    CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<COMMUNICATION-RESOURCE-CONNECTION-STRING>")
        .buildClient();
```

まず、Azure Monitor での要求のトレースを可能にするスパンを作成するために、`AzureMonitorTraceExporter` オブジェクトのインスタンスを作成する必要があります。 Application Insights リソースの接続文字列を指定する必要があります。

```java
    AzureMonitorTraceExporter exporter = new AzureMonitorExporterBuilder()
        .connectionString("<APPLICATION-INSIGHTS-CONNECTION-STRING>")
        .buildTraceExporter();
```

このエクスポーターにより、要求トレースを可能にするための次のインスタンスを作成できます。 `AzureMonitorTraceExporter` の作成の後に、次のコードを追加します。

```java

    SdkTracerProvider tracerProvider = SdkTracerProvider.builder()
        .addSpanProcessor(SimpleSpanProcessor.create(exporter))
        .build();

    OpenTelemetrySdk openTelemetrySdk = OpenTelemetrySdk.builder()
        .setTracerProvider(tracerProvider)
        .buildAndRegisterGlobal();

    Tracer tracer = openTelemetrySdk.getTracer("Sample");
```
トレーサーが初期化されたら、要求のトレースを担当するスパンを作成できます。

```java
    Span span = tracer.spanBuilder("sample-span").startSpan();
    final Scope scope = span.makeCurrent();
    try {
        // Thread bound (sync) calls will automatically pick up the parent span and you don't need to pass it explicitly.
        client.createUser();
    } finally {
        span.end();
        scope.close();
    }
    Thread.sleep(10000);
```

## <a name="run-the-code"></a>コードの実行

*pom.xml* ファイルが格納されているディレクトリに移動し、次の `mvn` コマンドを使用してプロジェクトをコンパイルします。

```console
mvn compile
```

次に、パッケージをビルドします。

```console
mvn package
```

次の `mvn` コマンドを実行して、アプリを実行します。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
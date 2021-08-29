---
title: Anomaly Detector (多変量) Java クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: cbea7a93d80a0d8f68b23cbcfde92d34d5a0d1d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802289"
---
Java 用 Anomaly Detector (多変量) クライアント ライブラリを使ってみましょう。 サービスによって提供されるアルゴリズムを使用してパッケージをインストールするには、次の手順に従います。 新しい多変量異常検出 API を使用すると、機械学習の知識やラベル付けされたデータがなくても、一連のメトリックから異常を検出できる高度な AI を開発者が容易に統合することができます。 異なる信号間の依存関係や相互相関が自動的に主要な要因として考慮されます。 これにより、複雑なシステムを障害から予防的に保護することができます。

Java 用 Anomaly Detector (多変量) クライアント ライブラリは、次の目的に使用します。

* 時系列のグループからシステム レベルの異常を検出する。
* 個々の時系列では得られる情報が少なく、すべての信号に着目して問題を検出する必要がある。
* システム正常性をさまざまな側面から測定する数十個から数百個にのぼる各種センサーを使用して高価な物理資産の予測メンテナンスを行う。

[ライブラリのリファレンス ドキュメント](/java/api/com.azure.ai.anomalydetector) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [パッケージ (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/) | [サンプル コード](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリの詳細については、[Maven Central Repository](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) を参照してください。

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

作業ディレクトリから `gradle init` コマンドを実行します。 このコマンドによって、Gradle 用の重要なビルド ファイルが作成されます。たとえば、実行時にアプリケーションを作成して構成するために使用される *build.gradle.kts* などです。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

*build.gradle.kts* を検索し、任意の IDE またはテキスト エディターで開きます。 その後、次のビルド構成をコピーします。 必ずプロジェクトの依存関係を含めてください。

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Java ファイルを作成する

サンプル アプリ用のフォルダーを作成します。 作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

新しいフォルダーに移動し、*MetricsAdvisorQuickstarts.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 サンプル データ ファイル用にもう 1 つの変数を作成します。

> [!NOTE]
> 常に、2 つのキーのいずれかを使用できます。 これは、セキュリティ保護されたキーのローテーションを可能にするためです。 このクイックスタートでは、1 番目のキーを使用します。 

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

Anomaly Detector 多変量 API シリーズを使用するには、最初に独自のモデルをトレーニングする必要があります。 トレーニング データは、次の要件を満たす複数の時系列のセットです。

各時系列は、ヘッダー行として "timestamp" と "value" (すべて小文字) の 2 つの列のみを含む CSV ファイルである必要があります。 "timestamp" の値は、ISO 8601 に準拠している必要があります。"value" は、整数または小数点以下の桁数が任意の小数にすることができます。 次に例を示します。

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

各 CSV ファイルには、モデルのトレーニングに使用する異なる変数に基づいて名前を付ける必要があります。 たとえば、"temperature.csv" や "humidity.csv" などです。 すべての CSV ファイルは、サブフォルダーを使用しないで 1 つの ZIP ファイルに圧縮する必要があります。 ZIP ファイルには任意の名前を付けることができます。 ZIP ファイルは Azure Blob Storage にアップロードする必要があります。 その ZIP ファイルの BLOB SAS (Shared Access Signature) URL を生成したら、それをトレーニングに使用できます。 Azure Blob Storage から SAS URL を生成する方法については、このドキュメントを参照してください。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 Anomaly Detector クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [モデルをトレーニングする](#train-a-model)
* [異常を検出する](#detect-anomalies)
* [モデルをエクスポートする](#export-model)
* [モデルを削除する](#delete-model)

## <a name="authenticate-the-client"></a>クライアントを認証する

ご利用のエンドポイントと資格情報を使用して `anomalyDetectorClient` オブジェクトをインスタンス化します。

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>モデルをトレーニングする

### <a name="construct-a-model-result-and-train-model"></a>モデルの結果を構築してモデルをトレーニングする

まず、モデル要求を構築する必要があります。 開始時刻と終了時刻は、必ずデータソースに合わせて調整してください。

Anomaly Detector (多変量) API を使用するには、検出を使用する前に独自のモデルをトレーニングする必要があります。 トレーニングに使用するデータは、時系列のバッチです。各時系列は、 **"timestamp"** と **"value"** の 2 つの列だけを含む CSV ファイルに含まれている必要があります (列名はまったく同じである必要があります)。 各 CSV ファイルには、時系列の各変数にちなんで名前を付ける必要があります。 すべての時系列を 1 つの ZIP ファイルに圧縮し、[Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs) にアップロードします。ZIP ファイル名の要件はありません。 あるいは、変数の名前を .zip ファイル名とは異なるものにしたい場合は、追加の meta.json ファイルを ZIP ファイルに含めることもできます。 [BLOB の SAS (Shared Access Signature) URL](../../../../storage/common/storage-sas-overview.md) を生成したら、ZIP ファイルの URL をトレーニングに使用できます。

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy()
                                .setAlignMode(alignMode)
                                .setFillNAMethod(fillNAMethod)
                                .setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo()
                        .setSlidingWindow(window)
                        .setAlignPolicy(alignPolicy)
                        .setSource(source)
                        .setStartTime(startTime)
                        .setEndTime(endTime)
                        .setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] substring = header.split("/");
UUID modelId = UUID.fromString(substring[substring.length - 1]);
System.out.println(modelId);

//Check model status until the model is ready
Response<Model> trainResponse;
while (true) {
    trainResponse = anomalyDetectorClient.getMultivariateModelWithResponse(modelId, Context.NONE);
    ModelStatus modelStatus = trainResponse.getValue().getModelInfo().getStatus();
    if (modelStatus == ModelStatus.READY || modelStatus == ModelStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (trainResponse.getValue().getModelInfo().getStatus() != ModelStatus.READY){
    System.out.println("Training failed.");
    List<ErrorResponse> errorMessages = trainResponse.getValue().getModelInfo().getErrors();
    for (ErrorResponse errorMessage : errorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="detect-anomalies"></a>異常を検出する

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(modelId, detectionRequest, Context.NONE);
String location = detectAnomalyResponse.getDeserializedHeaders().getLocation();
String[] substring = location.split("/");
UUID resultId = UUID.fromString(substring[substring.length - 1]);

DetectionResult detectionResult;
while (true) {
    detectionResult = anomalyDetectorClient.getDetectionResult(resultId);
    DetectionStatus detectionStatus = detectionResult.getSummary().getStatus();;
    if (detectionStatus == DetectionStatus.READY || detectionStatus == DetectionStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (detectionResult.getSummary().getStatus() != DetectionStatus.READY){
    System.out.println("Inference failed");
    List<ErrorResponse> detectErrorMessages = detectionResult.getSummary().getErrors();
    for (ErrorResponse errorMessage : detectErrorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="export-model"></a>モデルをエクスポートする

> [!NOTE]
> エクスポート コマンドは、コンテナー化された環境で Anomaly Detector 多変量モデルを実行できるようにするために使用することを目的としています。 現在、これは多変量ではサポートされていませんが、今後サポートが追加される予定です。

トレーニング済みのモデルをエクスポートするには、`exportModelWithResponse` を使用します。

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>モデルを削除する

現在のリソースで使用可能な既存のモデルを削除するには、`deleteMultivariateModelWithResponse` 関数を使用します。

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>アプリケーションの実行

次を使用してアプリをビルドできます。

```console
gradle build
```
### <a name="run-the-application"></a>アプリケーションの実行

実行する前に、実際のコードを[完全なサンプル コード](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)に照らして確認することをお勧めします。

`run` ゴールを使用してアプリケーションを実行します。

```console
gradle run
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのリソース グループに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

* [Anomaly Detector API とは](../../overview-multivariate.md)
* [Anomaly Detector API を使用する場合のベスト プラクティス](../../concepts/best-practices-multivariate.md)

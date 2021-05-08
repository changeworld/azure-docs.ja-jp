---
title: Anomaly Detector (多変量) Java クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: eae4d00cd7b1a0ff90648086320135505a0d900a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318765"
---
Java 用 Anomaly Detector (多変量) クライアント ライブラリを使ってみましょう。 サービスによって提供されるアルゴリズムを使用してパッケージをインストールするには、次の手順に従います。 新しい多変量異常検出 API を使用すると、機械学習の知識やラベル付けされたデータがなくても、一連のメトリックから異常を検出できる高度な AI を開発者が容易に統合することができます。 異なる信号間の依存関係や相互相関が自動的に主要な要因として考慮されます。 これにより、複雑なシステムを障害から予防的に保護することができます。

Java 用 Anomaly Detector (多変量) クライアント ライブラリは、次の目的に使用します。

* 時系列のグループからシステム レベルの異常を検出する。
* 個々の時系列では得られる情報が少なく、すべての信号に着目して問題を検出する必要がある。
* システム正常性をさまざまな側面から測定する数十個から数百個にのぼる各種センサーを使用して高価な物理資産の予測メンテナンスを行う。

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

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Anomaly Detector (多変量) API を使用するには、検出を使用する前に独自のモデルをトレーニングする必要があります。 トレーニングに使用するデータは時系列のバッチであり、各時系列は、timestamp と value の 2 つの列を含む CSV 形式である必要があります。 すべての時系列を 1 つの ZIP ファイルに圧縮し、[Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md) にアップロードする必要があります。 既定では、時系列の変数を表すためにこのファイル名が使用されます。 あるいは、変数の名前を .zip ファイル名とは異なるものにしたい場合は、追加の meta.json ファイルを ZIP ファイルに含めることもできます。 [BLOB の SAS (Shared Access Signature) URL](../../../../storage/common/storage-sas-overview.md) を生成したら、ZIP ファイルの URL をトレーニングに使用できます。

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

 Anomaly Detector (多変量) API を使用するには、検出を使用する前に独自のモデルをトレーニングする必要があります。 トレーニングに使用するデータは時系列のバッチであり、各時系列は、timestamp と value の 2 つの列を含む CSV 形式である必要があります。 すべての時系列を 1 つの ZIP ファイルに圧縮し、[Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs) にアップロードする必要があります。 既定では、時系列の変数を表すためにこのファイル名が使用されます。 あるいは、変数の名前を .zip ファイル名とは異なるものにしたい場合は、追加の meta.json ファイルを ZIP ファイルに含めることもできます。 [BLOB の SAS (Shared Access Signature) URL](../../../../storage/common/storage-sas-overview.md) を生成したら、ZIP ファイルの URL をトレーニングに使用できます。

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
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>異常を検出する

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>モデルをエクスポートする

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

`run` ゴールを使用してアプリケーションを実行します。

```console
gradle run
```

## <a name="next-steps"></a>次のステップ

* [Anomaly Detector (多変量) のベスト プラクティス](../../concepts/best-practices-multivariate.md)

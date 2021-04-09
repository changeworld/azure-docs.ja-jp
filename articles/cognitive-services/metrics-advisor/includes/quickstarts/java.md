---
title: Metrics Advisor Java クイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 316a27c6b63d71fb87649524adb4b61bd2bd133b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444149"
---
[リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [成果物 (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Azure サブスクリプションを作成したら、Azure portal で <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor リソースを作成"  target="_blank">Metrics Advisor リソースを作成</a>して、Metrics Advisor インスタンスをデプロイします。  
* 時系列データを含む独自の SQL データベース。
  
  
> [!TIP]
> * Java Metrics Advisor のサンプルは、[GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) にあります。
> * Metrics Advisor リソースによってサービス インスタンスがデプロイされ、使用できるようになるまでには、10 分から 30 分ほどかかる可能性があります。 デプロイに成功したら、 **[リソースに移動]** をクリックします。 デプロイ後、Web ポータルと REST API の両方で Metrics Advisor インスタンスの使用を開始できます。 
> * REST API の URL は Azure portal で確認できます (自分のリソースの **[概要]** セクション)。 次のようになります。
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
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
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.2")
}
```

### <a name="create-a-java-file"></a>Java ファイルを作成する

サンプル アプリ用のフォルダーを作成します。 作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

新しいフォルダーに移動し、*MetricsAdvisorQuickstarts.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) にあり、このクイックスタートのコード例が含まれています。

アプリケーションの `MetricsAdvisorQuickstarts` クラスに、リソースのキーとエンドポイントの変数を作成します。


> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Metrics Advisor リソースが正常にデプロイされたら、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプションのキーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 <br><br>API キーを取得するには、[https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) にアクセスする必要があります。 リソースに適切な **ディレクトリ**、**サブスクリプション**、**ワークスペース** を選択し、 **[Get started]\(作業の開始\)** を選択します。 その後、[https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) から API キーを取得できるようになります。   
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

アプリケーションの `Main()` メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 これらは後で作成します。

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>オブジェクト モデル

次のクラスによって、Metrics Advisor Java SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **使用目的**: <br> - 異常インシデントの一覧表示 <br> - インシデントの根本原因の一覧表示 <br> - 元の時系列データと、サービスによってエンリッチされた時系列データの取得 <br> - アラートの一覧表示 <br> - モデルを調整するためのフィードバックの追加 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **次のことを実行できます**: <br> - データ フィードを管理する <br> - 異常検出構成を設定する <br> - 異常アラート構成を設定する <br> - フックを管理する  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeed.html) | **Metrics Advisor によってデータソースから取り込まれるもの。`DataFeed` には、次の行が含まれます:** <br> - タイムスタンプ <br> - 0 個以上のディメンション <br> - 1 つ以上のメジャー  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeedMetric.html) | `DataFeedMetric` は、特定のビジネス プロセスの状態を監視および評価するために使用される定量化可能なメジャーです。 ディメンションに分割された複数の時系列値を組み合わせることができます。 たとえば、web health のメトリックには、user count と en-us market のディメンションが含まれている場合があります。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Java 用 Metrics Advisor クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [データ フィードを追加する](#add-a-data-feed)
* [インジェストの状態を確認する](#check-the-ingestion-status)
* [異常検出を構成する](#configure-anomaly-detection)
* [フックを作成する](#create-a-hook)
* [アラート構成を作成する](#create-an-alert-configuration)
* [アラートに対してクエリを実行する](#query-the-alert)

## <a name="authenticate-the-client"></a>クライアントを認証する

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>MetricsAdvisorKeyCredential を使用して Metrics Advisor クライアントを作成する

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>MetricsAdvisorKeyCredential を使用して Metrics Administration クライアントを作成する

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>データ フィードを追加する

`sql_server_connection_string` を、実際の SQL Server 接続文字列に置き換え、`query` を、1 つのタイムスタンプでデータを返すクエリに置き換えます。 また、カスタム データに基づいて `DataFeedMetric` と `DataFeedDimension` の値を調整する必要もあります。

> [!IMPORTANT]
> クエリからは、各タイムスタンプで、ディメンションの各組み合わせに対して多くても 1 つのレコードが返される必要があります。 また、クエリによって返されるすべてのレコードで、タイムスタンプが同じになっている必要があります。 Metrics Advisor によってタイムスタンプごとにこのクエリが実行され、データが取り込まれます。 詳細と例については、[クエリの FAQ セクション](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)を参照してください。 

```java
DataFeed dataFeed = new DataFeed()
    .setName("dataFeedName")
    .setSource(new MySqlDataFeedSource("sql_server_connection_string", "query"))
    .setGranularity(new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY))
    .setSchema(new DataFeedSchema(
        Arrays.asList(
            new DataFeedMetric().setName("cost"),
            new DataFeedMetric().setName("revenue")
        )).setDimensions(
        Arrays.asList(
            new DataFeedDimension().setName("city"),
            new DataFeedDimension().setName("category")
        ))
    )
    .setIngestionSettings(new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")))
    .setOptions(new DataFeedOptions()
        .setDescription("data feed description")
        .setRollupSettings(new DataFeedRollupSettings()
            .setRollupType(DataFeedRollupType.AUTO_ROLLUP)));
final DataFeed createdSqlDataFeed = metricsAdvisorAdminClient.createDataFeed(dataFeed);

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(System.out::println);
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());

if (SQL_SERVER_DB == createdSqlDataFeed.getSourceType()) {
    System.out.printf("Data feed sql server query: %s%n",
        ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
}
```

## <a name="check-the-ingestion-status"></a>インジェストの状態を確認する

この例では、以前に提供されたデータ フィード ソースのインジェストの状態を確認します。

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdminClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>異常検出を構成する 

この例では、データの異常検出構成を設定する方法を示します。

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdminClient.createMetricAnomalyDetectionConfig(
        metricId,
        new AnomalyDetectionConfiguration("My dataPoint anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>フックを作成する

この例では、異常インシデント アラートを受け取る email hook を作成します。

```java
NotificationHook emailNotificationHook = new EmailNotificationHook("email Hook")
    .setDescription("my email Hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://example.com/handleAlerts"); // you must enter a valid webhook url to post the alert payload

final NotificationHook notificationHook = metricsAdvisorAdminClient.createHook(emailNotificationHook);
EmailNotificationHook createdEmailHook = (EmailNotificationHook) notificationHook;
System.out.printf("Email Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Email Hook name: %s%n", createdEmailHook.getName());
System.out.printf("Email Hook description: %s%n", createdEmailHook.getDescription());
System.out.printf("Email Hook external Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Email Hook emails to alert: %s%n",
    String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>アラート構成を作成する

この例では、データ内で検出された異常に対するアラート構成を設定する方法を示します。

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdminClient.createAnomalyAlertConfig(
        new AnomalyAlertConfiguration("My anomaly alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityRangeCondition(new SeverityCondition()
                                .setMaxAlertSeverity(AnomalySeverity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>アラートに対してクエリを実行する

この例では、アラート検出構成に対してトリガーされたアラートに対してクエリを実行し、そのアラートの異常を取得する方法を示します。

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
final OffsetDateTime startTime = OffsetDateTime.parse("2020-01-01T00:00:00Z");
final OffsetDateTime endTime = OffsetDateTime.parse("2020-09-09T00:00:00Z");
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
        startTime, endTime)
    .forEach(alert -> {
        System.out.printf("AnomalyAlert Id: %s%n", alert.getId());
        System.out.printf("AnomalyAlert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("DataPoint Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("DataPoint Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("DataPoint Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("DataPoint Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

次を使用してアプリをビルドできます。

```console
gradle build
```
### <a name="run-the-application"></a>アプリケーションの実行

`run` ゴールを使用してアプリケーションを実行します。

```console
gradle run
```
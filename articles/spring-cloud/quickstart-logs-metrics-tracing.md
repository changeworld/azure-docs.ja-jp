---
title: クイックスタート - ログ、メトリック、およびトレースを使用した Azure Spring Cloud アプリの監視
description: ログ ストリーミング、ログ分析、メトリック、およびトレースを使用して、Azure Spring Cloud の PetClinic サンプル アプリを監視します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/12/2021
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 50149a6c1b6b8db8f727c81d7c5a5685f55b862a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996116"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>クイック スタート:ログ、メトリック、およびトレースを使用した Azure Spring Cloud アプリの監視

::: zone pivot="programming-language-csharp"
Azure Spring Cloud の組み込み監視機能を使用すると、複雑な問題をデバッグおよび監視できます。 Azure Spring Cloud は、Steeltoe [分散トレース](https://docs.steeltoe.io/api/v3/tracing/)と Azure の [Application Insights](../azure-monitor/app/app-insights-overview.md) を統合します。 この統合により、Azure portal から強力なログ、メトリック、および分散トレース機能を利用できます。

次の手順では、前のクイックスタートでデプロイしたサンプル アプリで、ログ ストリーミング、Log Analytics、メトリック、分散トレースを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

* このシリーズの先行する次のクイックスタートを完了しておきます。

  * 「[Azure Spring Cloud サービスのプロビジョニング](./quickstart-provision-service-instance.md)」。
  * [Azure Spring Cloud の構成サーバーを設定する](./quickstart-setup-config-server.md)
  * [アプリをビルドおよびデプロイする](./quickstart-deploy-apps.md)。

## <a name="logs"></a>ログ

Azure Spring Cloud でログを表示するには、次の 2 つの方法があります。アプリ インスタンスごとのリアルタイム ログの **ログ ストリーミング**、または高度なクエリ機能を使用して集計されたログの **ログ分析**。

### <a name="log-streaming"></a>ログ ストリーミング

ログ ストリーミングを使用するには、Azure CLI で次のコマンドを実行します。

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

次の例のような出力が表示されます。

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> さらに多くのパラメーターとログ ストリーム機能を調べるには、`az spring-cloud app logs -h` を使用します。

### <a name="log-analytics"></a>Log Analytics

1. Azure portal で **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Logs]\(ログ\)** を選択します。 Azure Spring Cloud のいずれかのサンプル クエリで **[Run]\(実行\)** を選択します。

   [ ![Logs Analytics のエントリ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)

1. クエリを編集し、表示を警告とエラーのログに制限する Where 句を削除します。

1. その後、[`Run`] を選択すると、ログが表示されます。 クエリの記述の詳細なガイダンスについては、[Azure Log Analytics のドキュメント](../azure-monitor/logs/get-started-queries.md)を参照してください。

   [ ![Log Analytics クエリ - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. Log Analytics で使用されるクエリ言語の詳細については、「[Azure Monitor ログ クエリ](/azure/data-explorer/kusto/query/)」を参照してください。 一元化されたクライアントからすべての Log Analytics ログを照会する場合は、[Azure Data Explorer](/azure/data-explorer/query-monitor-data) に関するページを参照してください。

## <a name="metrics"></a>メトリック

1. Azure portal で **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Metrics]\(メトリック\)** を選択します。 最初のメトリックを追加するには、 **[メトリック]** ドロップダウンで **[Performance (.NET)]\(パフォーマンス (.NET)\)** または **[Request (.NET)]\(要求 (.NET)\)** の下の .NET メトリックのいずれかを選択します。また、そのメトリックのタイムラインを表示するには、 **[集計]** で `Avg` を選択します。

   [ ![メトリック エントリ - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)

1. ツール バーで **[Add filter]\(フィルターの追加\)** を選択し、`App=solar-system-weather` を選択して、**solar-system-weather** アプリのみの CPU 使用率を表示します。

   [ ![メトリックでフィルターを使用する - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. 前の手順で作成したフィルターを破棄し、 **[Apply Splitting]\(分割の適用\)** を選択します。 **[値]** で `App` を選択して、さまざまなアプリごとの CPU 使用率を表示します。

   [ ![メトリックで分割を適用する - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>分散トレース

1. Azure portal で **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Distributed tracing]\(分散トレース\)** を選択します。 次に、右側の **[View application map]\(アプリケーション マップの表示\)** タブを選択します。

   [ ![分散トレースのエントリ - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. これで、アプリ間の呼び出しの状態を確認できるようになりました。

   [ ![分散トレースの概要 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)

1. **solar-system-weather** と **planet-weather-provider** の間のリンクを選択すると、HTTP メソッドごとの最低速の呼び出しなどの詳細が表示されます。

   [ ![分散トレース - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)

1. 最後に、 **[パフォーマンスの調査]** を選択して、より強力な組み込みのパフォーマンス分析を調べます。

   [ ![分散トレースのパフォーマンス - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud の組み込み監視機能を使用すると、複雑な問題をデバッグおよび監視できます。 Azure Spring Cloud は、[Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](../azure-monitor/app/app-insights-overview.md) を統合します。 この統合により、Azure portal から強力なログ、メトリック、および分散トレース機能を利用できます。 次の手順では、デプロイされた PetClinic アプリでログ ストリーミング、ログ分析、メトリック、および分散トレースを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

以下の先行する手順を完了してください。

* [Azure Spring Cloud のインスタンスをプロビジョニングする](./quickstart-provision-service-instance.md)
* [構成サーバーを設定する](./quickstart-setup-config-server.md)
* [アプリをビルドおよびデプロイする](./quickstart-deploy-apps.md)。

## <a name="logs"></a>ログ

Azure Spring Cloud でログを表示するには、次の 2 つの方法があります。アプリ インスタンスごとのリアルタイム ログの **ログ ストリーミング**、または高度なクエリ機能を使用して集計されたログの **ログ分析**。

### <a name="log-streaming"></a>ログ ストリーミング

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

ログ ストリーミングを使用するには、Azure CLI で次のコマンドを実行します。

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

次のようなログが表示されます。

[ ![Azure CLI からのログ ストリーミング](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> さらに多くのパラメーターとログ ストリーム機能を調べるには、`az spring-cloud app logs -h` を使用します。

Log Analytics で使用されるクエリ言語の詳細については、「[Azure Monitor ログ クエリ](/azure/data-explorer/kusto/query/)」を参照してください。 一元化されたクライアントからすべての Log Analytics ログのクエリを実行する場合には、[Azure Data Explorer](/azure/data-explorer/query-monitor-data)に関するページを確認してください。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Azure Toolkit for IntelliJ を使用してログを取得するには:

1. **Azure Explorer** を選択し、 **[Spring Cloud]** を選択します。

1. 実行中のアプリを右クリックします。

1. ドロップダウン リストから **[Streaming Logs]\(ストリーミング ログ\)** を選択します。

   ![ストリーミング ログの選択](media/spring-cloud-intellij-howto/streaming-logs.png)

1. **[Instance]\(インスタンス\)** を選択します。

   ![インスタンスの選択](media/spring-cloud-intellij-howto/select-instance.png)

1. ストリーミング ログが出力ウィンドウに表示されます。

   ![ストリーミング ログの出力](media/spring-cloud-intellij-howto/streaming-log-output.png)

 Log Analytics で使用されるクエリ言語の詳細については、「[Azure Monitor ログ クエリ](/azure/data-explorer/kusto/query/)」を参照してください。 一元化されたクライアントからすべての Log Analytics ログのクエリを実行する場合には、[Azure Data Explorer](/azure/data-explorer/query-monitor-data)に関するページを確認してください。

---

### <a name="log-analytics"></a>Log Analytics

1. **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Logs]\(ログ\)** を選択します。 Azure Spring Cloud のいずれかのサンプル クエリで **[Run]\(実行\)** を選択します。

   [ ![Logs Analytics ポータルへのエントリ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png#lightbox)

1. そうすると、フィルター処理されたログが表示されます。 クエリの記述の詳細なガイダンスについては、[Azure Log Analytics のドキュメント](../azure-monitor/logs/get-started-queries.md)を参照してください。

   [ ![Logs Analytics のクエリ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>メトリック

`Application insights` ブレードに移動します。 次に、[`Metrics`] ブレードに移動します。Spring Boot アプリ、Spring Cloud モジュール、および依存関係によって提供されるメトリックが表示されます。 

次のグラフは、`gateway-requests` (Spring Cloud Gateway)、`hikaricp_connections` (JDBC Connections)、および `http_client_requests` を示しています。

[ ![[メトリック] ブレード](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg#lightbox)

Spring Boot では、JVM、CPU、Tomcat、Logback などの多数のコア メトリックが登録されます。Spring Boot の自動構成により、Spring MVC によって処理される要求のインストルメンテーションが有効になります。
これらの 3 つの REST コントローラー `OwnerResource`、`PetResource`、および `VisitResource` は、すべて `@Timed` マイクロメーター注釈によってクラス レベルでインストルメント化されています。

* `customers-service` アプリケーションでは、次のカスタム メトリックが有効になっています。
  * @Timed: `petclinic.owner`
  * @Timed: `petclinic.pet`
* `visits-service` アプリケーションでは、次のカスタム メトリックが有効になっています。
  * @Timed: `petclinic.visit`

これらのカスタム メトリックは、[`Metrics`] ブレードで確認できます。[ ![カスタム メトリック](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg#lightbox)

Application Insights の可用性テスト機能を使用して、アプリケーションの可用性を監視できます。

[ ![可用性テスト](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg#lightbox)

[`Live Metrics`] ブレードに移動します。1 秒未満の待ち時間で画面にライブ メトリックが表示されます。[ ![ライブ メトリック](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg#lightbox)

## <a name="tracing"></a>トレース

Azure Spring Cloud によって作成された Application Insights を開き、マイクロサービス アプリケーションの監視を開始します。

[`Application Map`] ブレードに移動します。[ ![アプリケーション マップ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg#lightbox)

[`Performance`] ブレードに移動します。[ ![[パフォーマンス] ブレード](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg#lightbox)

[`Performance/Dependenices`] ブレードに移動します。依存関係 (特に SQL 呼び出し) のパフォーマンス番号が表示されます。[ ![[パフォーマンス] - [依存関係] ブレード](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg#lightbox)

SQL 呼び出しを選択して、コンテキスト内のエンドツーエンド トランザクションを表示します。[ ![SQL エンドツーエンド トランザクション](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg#lightbox)

[`Failures/Exceptions`] ブレードに移動します。例外のコレクションが表示されます。[ ![[失敗] - [例外]](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.png#lightbox)

例外を選択して、コンテキスト内のエンドツーエンドのトランザクションとスタックトレースを表示します。[ ![エンドツーエンドのスタックトレース](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのクイックスタートでは、サブスクリプションに残っていると課金が継続される Azure リソースを作成しました。 今後これらのリソースが必要になることが予想されない場合は、ポータルを使用するか、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

前のクイックスタートでは、既定のリソース グループ名も設定しました。 次のクイックスタートに進まない場合は、次の CLI コマンドを実行して、既定値をクリアします。

```azurecli
az config set defaults.group=
```

## <a name="next-steps"></a>次の手順

Azure Spring Cloud の監視機能の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [診断サービス](diagnostic-services.md)
>
> [分散トレース](./how-to-distributed-tracing.md)
>
> [リアルタイムでログをストリームする](./how-to-log-streaming.md)

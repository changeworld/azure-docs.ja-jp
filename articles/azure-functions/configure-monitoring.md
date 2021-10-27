---
title: Azure Functions の監視を構成する
description: 監視のために関数アプリを Application Insights に接続する方法と、データ収集を構成する方法について説明します。
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3afe10184ba2b3f0eba02111b98b31e86b26e075
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006483"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Azure Functions の監視を構成する方法

Azure Functions が Application Insights に統合されると、関数アプリの監視をより適切に行うことができます。 Azure Monitor の機能である Application Insights は、アプリがログに書き込む情報など、関数アプリによって生成されたデータを収集する拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 Application Insights との統合は、通常、関数アプリが作成されるときに有効になります。 アプリにインストルメンテーション キーが設定されていない場合は、まず [Application Insights との統合を有効にする](#enable-application-insights-integration)必要があります。 

Application Insights はカスタム構成なしで使用できます。 既定の構成ではデータ量が多くなる可能性があります。 Visual Studio Azure サブスクリプションを使っている場合、Application Insights のデータ上限に達する可能性があります。 Application Insights のコストの詳細については、「[Application Insights の使用量とコストを管理する](../azure-monitor/app/pricing.md)」を参照してください。 詳細については、「[テレメトリの量が多いソリューション](#solutions-with-high-volume-of-telemetry)」を参照してください。

この記事の後半では、関数から Application Insights に送信するデータを構成し、カスタマイズする方法を説明します。 関数アプリの場合、ログは [host.json] ファイルで構成されます。 

> [!NOTE]
> 特別に構成されたアプリケーション設定を使用すれば、特定の環境の host.json ファイル内の特定の設定を表すことができます。 これにより、プロジェクトに host.json ファイルを再発行しなくても、host.json 設定を効果的に変更できます。 詳細については、「[host.json 値をオーバーライドする](functions-host-json.md#override-hostjson-values)」を参照してください。

## <a name="configure-categories"></a>カテゴリを構成する

Azure Functions のロガーでは、すべてのログに *カテゴリ* があります。 カテゴリは、ランタイム コードや関数コードのどの部分にログが記述されているかを示します。 バージョン 1.x とそれ以降のバージョンとでは、カテゴリが異なります。 以下のチャートは、ランタイムが作成するログの主なカテゴリについて説明します。 

# <a name="v2x"></a>[v2.x+](#tab/v2)

| カテゴリ | テーブル | 説明 |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **dependencies**| 一部のサービスについては、依存関係データが自動的に収集されます。 正常に実行された場合、これらのログは `Information` レベルとなります。 詳細については、「[依存関係](functions-monitoring.md#dependencies)」を参照してください。 例外は `Error` レベルでログされます。 また、キュー メッセージが[有害キュー](functions-bindings-storage-queue-trigger.md#poison-messages)に送信されたといったような場合には、ランタイムによって `Warning` レベルのログも作成されます。 | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | C# および JavaScript SDK を使用すると、カスタム メトリックを収集し、カスタム イベントをログに記録することができます。 詳細については、「[カスタム テレメトリ データ](functions-monitoring.md#custom-telemetry-data)」を参照してください。|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| 特定の関数の実行について、Function started および completed のログが含まれます。 正常に実行された場合、これらのログは `Information` レベルとなります。 例外は `Error` レベルでログされます。 また、キュー メッセージが[有害キュー](functions-bindings-storage-queue-trigger.md#poison-messages)に送信されたといったような場合には、ランタイムによって `Warning` レベルのログも作成されます。 | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| ユーザーが生成したログ。任意のログ レベルとすることができます。 関数からのログへの書き込みの詳細については、「[ログへの書き込み](functions-monitoring.md#writing-to-logs)」を参照してください。 | 
| **`Host.Aggregator`** | **customMetrics** | これらのランタイム生成ログには、[構成可能な](#configure-the-aggregator)期間の関数呼び出しの回数と平均回数が記録されます。 既定の期間は、30 秒か 1,000 回のどちらか早い方です。 例としては、実行回数、成功率、時間などがあります。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |
| **`Host.Results`** | **requests** | これらのランタイム生成ログから、関数の成功または失敗を確認できます。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |
| **`Microsoft`** | **traces** | ホストによって起動される .NET ランタイム コンポーネントを反映する完全修飾ログ カテゴリ。  |
| **`Worker`** | **traces** | .NET 以外の言語用の言語ワーカー プロセスによって生成されるログ。 言語ワーカー ログは、`Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` などの `Microsoft.*` カテゴリでログされる場合もあります。 これらのログは `Information` レベルで書き込まれます。|

> [!NOTE]
> .NET クラス ライブラリ関数の場合、これらのカテゴリでは、`ILogger<T>` ではなく `ILogger` を使用していることが想定されています。 詳細については、[関数 ILogger のドキュメント](functions-dotnet-class-library.md#ilogger)を参照してください。 

# <a name="v1x"></a>[v1.x](#tab/v1)

| カテゴリ | テーブル | 説明 |
| ----- | ----- | ----- |
| **`Function`** | **traces**| ユーザーが生成したログ。任意のログ レベルとすることができます。 関数からのログへの書き込みの詳細については、「[ログへの書き込み](functions-monitoring.md#writing-to-logs)」を参照してください。 | 
| **`Host.Aggregator`** | **customMetrics** | これらのランタイム生成ログには、[構成可能な](#configure-the-aggregator)期間の関数呼び出しの回数と平均回数が記録されます。 既定の期間は、30 秒か 1,000 回のどちらか早い方です。 例としては、実行回数、成功率、時間などがあります。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |
| **`Host.Executor`** | **traces** | 特定の関数の実行について、**Function started** および **Function completed** ログを含みます。 正常に実行された場合、これらのログは `Information` レベルとなります。 例外は `Error` レベルで記録されます。 また、キュー メッセージが[有害キュー](functions-bindings-storage-queue-trigger.md#poison-messages)に送信されたといったような場合には、ランタイムによって `Warning` レベルのログも作成されます。  |
| **`Host.Results`** | **requests** | これらのランタイム生成ログから、関数の成功または失敗を確認できます。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |

---

**テーブル** 列に、Application Insights のどのテーブルにログが書き込まれるかが示されます。 

## <a name="configure-log-levels"></a>ログ レベルを構成する

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

カテゴリごとに、送信する最小のログ レベルを指定します。 host.json の設定は、[Functions ランタイムのバージョン](functions-versions.md)によって異なります。 

以下の例では、次の規則に基づいてログ記録を定義します。

+ `Host.Results` または `Function` のログについては、`Error` 以上のレベルのイベントのみをログします。 
+ `Host.Aggregator` のログについては、生成されたすべてのメトリック (`Trace`) をログします。
+ その他のすべてのログ (ユーザー ログなど) については、`Information` レベル以上のイベントのみをログします。

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

[host.json] に、同じ文字列で始まる複数のログが含まれている場合は、より明確に定義されたログが最初に照合されます。 ランタイムで、`Host.Aggregator` を除き、`Error` レベルのすべてのものをログする次の例について考えてみます。

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

`None` というログ レベル設定を使用すれば、カテゴリのログが書き込まれないようにすることができます。 

> [!CAUTION]
> Azure Functions と Application Insights の統合は、Application Insights のテーブルにテレメトリ イベントを格納することによって行われます。カテゴリ ログ レベルを `Information` 以外の値に設定すると、テレメトリはそれらのテーブルに流れなくなるため、Application Insights または関数の [モニター] タブに関連データを表示することはできません。
>
> 上記のサンプルから:
> * `Host.Results` カテゴリが `Error` ログ レベルに設定されている場合、`requests` テーブルには、失敗した関数の実行に関するホスト実行テレメトリ イベントのみが収集されます。これにより、Application Insights と関数の [モニター] タブの両方に、成功した実行のホスト実行の詳細は表示されなくなります。
> * `Function` カテゴリが `Error` ログ レベルに設定されている場合は、すべての関数について、`dependencies`、`customMetrics`、`customEvents` に関連する関数テレメトリ データの収集が停止され、Application Insights にこのデータが表示されなくなります。 `Error` レベルで記録された `traces` のみが収集されます。 
>
> どちらの場合も、Application Insights と関数の [モニター] タブでのエラーと例外のデータの収集は続けられます。詳細については、「[テレメトリの量が多いソリューション](#solutions-with-high-volume-of-telemetry)」を参照してください。


## <a name="configure-the-aggregator"></a>アグリゲーターを構成する

前のセクションで述べたように、ランタイムでは期間内の関数実行についてデータが集計されます。 既定の期間は、30 秒か 1,000 回実行のどちらか早い方です。 [host.json] ファイル内でこの設定を構成できます。  次に例を示します。

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>サンプリングを構成する

Application Insights には、負荷がピークのときに、完了した実行に関してテレメトリ データが生成されすぎないようにする[サンプリング](../azure-monitor/app/sampling.md)機能があります。 Application Insights では、受信実行の割合が指定されたしきい値を超えると、受信した実行の一部がランダムに無視され始めます。 1 秒あたりの最大実行数の既定の設定は 20 (バージョン 1.x では 5) です。 [host.json](./functions-host-json.md#applicationinsights) でサンプリングを構成できます。  次に例を示します。

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


特定の種類のテレメトリをサンプリングから除外することができます。 この例では、`Request` および `Exception` 型のデータがサンプリングから除外されます。 これにより、"*すべての*" 関数実行 (要求) と例外が確実にログされる一方、他の種類のテレメトリはサンプリング対象のままとなります。 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

詳細については、「[Application Insights におけるサンプリング](../azure-monitor/app/sampling.md)」を参照してください。

## <a name="configure-scale-controller-logs"></a>スケール コントローラーのログを構成する

_この機能はプレビュー段階にあります。_ 

[Azure Functions スケール コントローラー](./event-driven-scaling.md#runtime-scaling)から Application Insights または BLOB ストレージにログを出力させることで、関数アプリに対してスケール コントローラーが行っている決定をより詳しく把握することができます。

この機能を有効にするには、`SCALE_CONTROLLER_LOGGING_ENABLED` という名前のアプリケーション設定を、ご利用の関数アプリの設定に追加します。 この設定の値は `<DESTINATION>:<VERBOSITY>` の形式にし、以下に基づいて指定する必要があります。

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

たとえば、次の Azure CLI コマンドを実行すると、スケール コントローラーから Application Insights への詳細ログ記録が有効になります。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

この例では、`<FUNCTION_APP_NAME>` と `<RESOURCE_GROUP_NAME>` を実際の関数アプリの名前とリソース グループ名でそれぞれ置き換えます。 

次の Azure CLI コマンドでは、詳細度が `None` に設定されているため、ログ記録が無効になります。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

また、次の Azure CLI コマンドを使用して `SCALE_CONTROLLER_LOGGING_ENABLED` 設定を削除することで、ログ記録を無効にすることもできます。

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

スケール コントローラーのログを有効にすると、[スケール コントローラーのログに対してクエリを実行](analyze-telemetry-data.md#query-scale-controller-logs)できるようになります。 

## <a name="enable-application-insights-integration"></a>Application Insights との統合を有効にする

関数アプリでデータを Application Insights に送信するには、Application Insights リソースのインストルメンテーション キーについて知っておく必要があります。 キーは、**APPINSIGHTS_INSTRUMENTATIONKEY** という名前のアプリ設定に指定されている必要があります。

[Azure portal](./functions-get-started.md) で、または [Azure Functions Core Tools](./create-first-function-cli-csharp.md) を使用してコマンド ラインから、あるいは [Visual Studio Code](./create-first-function-vs-code-csharp.md) を使用して関数アプリを作成すると、Application Insights 統合が既定で有効になります。 Application Insights リソースは関数アプリと同じ名前を持ち、同じリージョンまたは最も近いリージョンのどちらかで作成されます。

### <a name="new-function-app-in-the-portal"></a>ポータルでの新しい関数アプリ

作成されている Application Insights リソースを確認するには、それを選択して **[Application Insights]** ウィンドウを展開します。 **[新しいリソース名]** を変更するか、またはデータを格納する [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **[場所]** を選択することができます。

![関数アプリの作成時に Application Insights を有効にする](media/functions-monitoring/enable-ai-new-function-app.png)

**[作成]** を選択すると、関数アプリと共に Application Insights リソースが作成され、アプリケーション設定の `APPINSIGHTS_INSTRUMENTATIONKEY` が設定されます。 これで、すべて準備ができました。

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>既存の関数アプリに追加する 

ご利用の関数アプリで Application Insights リソースが作成されていない場合は、次の手順を使用してリソースを作成します。 その後、関数アプリの[アプリケーション設定](functions-how-to-use-azure-function-app-settings.md#settings)として、そのリソースからインストルメンテーション キーを追加できます。

1. [Azure portal](https://portal.azure.com) で、**関数アプリ** を検索して選択してから、対象の関数アプリを選択します。 

1. ウィンドウの上部にある **[Application Insights が構成されていません]** バナーを選択します。 このバナーが表示されない場合は、アプリで既に Application Insights が有効になっている可能性があります。

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="ポータルから Application Insights を有効にする":::

1. **[リソースの変更]** を展開し、下の表に指定されている設定を使用して、Application Insights リソースを作成します。  

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **新しいリソース名** | 一意のアプリ名 | 関数アプリと同じ名前を使用するのが最も簡単です。この名前は、サブスクリプション内で一意である必要があります。 | 
    | **場所** | 西ヨーロッパ | 可能であれば、お使いの関数アプリと同じ[リージョン](https://azure.microsoft.com/regions/)、または近隣のリージョンを使用してください。 |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Application Insights リソースを作成します":::。

1. **[適用]** を選択します。 

   Application Insights リソースは、関数アプリと同じリソース グループおよびサブスクリプションに作成されます。 リソースが作成されたら、Application Insights ウィンドウを閉じます。

1. 対象の関数アプリで、 **[設定]** の下の **[構成]** を選択し、 **[アプリケーション設定]** を選択します。 `APPINSIGHTS_INSTRUMENTATIONKEY` という設定が表示された場合は、Azure で実行されている関数アプリに対して Application Insights 統合が有効になっています。 何らかの理由でこの設定が存在しない場合は、Application Insights インストルメンテーション キーを使用してそれを値として追加します。

> [!NOTE]
> 初期バージョンの関数では組み込みの監視を使用していましたが、これは推奨されなくなりました。 このような関数アプリで Application Insights 統合を有効にする場合は、[組み込みログも無効にする](#disable-built-in-logging)必要があります。  

## <a name="disable-built-in-logging"></a>組み込みログを無効にする

Application Insights を有効にする場合は、Azure Storage を使用する組み込みログを無効にします。 組み込みログは軽量のワークロードには便利ですが、高負荷の実稼働環境での使用には向きません。 実稼働環境の監視には、Application Insights をお勧めします。 組み込みログを実稼働環境で使用すると、Azure Storage での調整のためにログ レコードが不完全になる場合があります。

組み込みログを無効にするには、`AzureWebJobsDashboard` アプリ設定を削除します。 Azure Portal でアプリ設定を削除する方法については、[関数アプリの管理方法](functions-how-to-use-azure-function-app-settings.md#settings)に関するページで「**アプリケーションの設定**」セクションを参照してください。 アプリ設定を削除する前に、同じ関数アプリの既存の関数によって、Azure Storage のトリガーまたはバインドにその設定が使用されていないことを確認してください。

## <a name="solutions-with-high-volume-of-telemetry"></a>テレメトリの量が多いソリューション 

関数アプリはソリューションの重要な部分であり、本質的に大量のテレメトリ (IoT ソリューション、イベント ドリブン ベースのソリューション、高負荷の金融システム、統合システムなど) が発生する可能性があります。この場合、監視を維持しながらコストを削減するために、追加の構成を検討する必要があります。

生成されたテレメトリの使用方法 (リアルタイム ダッシュボード、アラート、詳細な診断など) に応じて、生成されるデータの量を減らすための戦略を定義する必要があります。 その戦略により、運用環境での関数アプリの監視、運用、診断を適切に行うことができます。 次のオプションを検討できます。

* **サンプリングを使用する**: [前述](#configure-sampling)のように、これは統計的に正しい分析を維持しながら、取り込まれるテレメトリ イベントの量を大幅に減らすのに役立ちます。 サンプリングを使用しても、テレメトリの量が多くなる可能性があります。 [アダプティブ サンプリング](../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-applications)によって提供されるオプションを調べてください。たとえば、`maxTelemetryItemsPerSecond` を、監視のニーズによって生成される量のバランスを取る値に設定します。 テレメトリ サンプリングは、関数アプリを実行しているホストごとに適用されることに注意してください。 

* **既定のログ レベル**: すべてのテレメトリ カテゴリの既定値として `Warning` または `Error` を使用します。 その上で、関数を適切に監視および診断できるように、どの[カテゴリ](#configure-categories)を `Information` に設定するかを決めることができます。

* **関数のテレメトリを調整する**: 既定のログ レベルを `Error` または `Warning` に設定すると、各関数からの詳細情報 (依存関係、カスタム メトリック、カスタム イベント、トレース) は収集されません。 運用環境の監視にとって重要な関数については、`Function.<YOUR_FUNCTION_NAME>` カテゴリに明示的なエントリを定義して、`Information` に設定します。これにより、詳細情報を収集できます。 この時点で、[ユーザーが生成したログ](functions-monitoring.md#writing-to-logs)が `Information` レベルで収集されないようにするには、`Function.<YOUR_FUNCTION_NAME>.User` カテゴリを `Error` または `Warning` のログ レベルに設定します。

* **Host.Aggregator カテゴリ**: 「[カテゴリを構成する](#configure-categories)」で説明したように、このカテゴリを使用すると関数呼び出しの集計情報が提供されます。 このカテゴリの情報は Application Insights の `customMetrics` テーブルに収集され、Azure portal の 関数の [概要] タブに表示されます。 アグリゲーターの構成方法によっては、`flushTimeout` によって決定される遅延が、収集されるテレメトリに含まれることを考慮してください。 このカテゴリを `Information` 以外の値に設定すると、`customMetrics` テーブルでのデータの収集は停止され、関数の [概要] タブにメトリックが表示されなくなります。

  次に示すのは、関数の [概要] タブに表示されている Host.Aggregator テレメトリ データのスクリーンショットです。:::image type="content" source="media/configure-monitoring/host-aggregator-function-overview.png" alt-text="関数の [概要] タブに表示されている Host.Aggregator テレメトリのスクリーンショット。" lightbox="media/configure-monitoring/host-aggregator-function-overview-big.png":::

  次のスクリーンショットは、Application Insights の customMetrics テーブルの Host.Aggregator テレメトリ データを示したものです。
  :::image type="content" source="media/configure-monitoring/host-aggregator-custom-metrics.png" alt-text="customMetrics Application Insights テーブルの Host.Aggregator テレメトリのスクリーンショット。" lightbox="media/configure-monitoring/host-aggregator-custom-metrics-big.png":::

* **Host.Results カテゴリ**: 「[カテゴリを構成する](#configure-categories)」で説明されているように、このカテゴリでは、ランタイムによって生成された、関数呼び出しの成功または失敗を示すログが提供されます。 このカテゴリの情報は、Application Insights の `requests` テーブルに収集されて、関数の [モニター] タブと Application Insights のさまざまなダッシュボード ([パフォーマンス]、[エラー] など) に表示されます。このカテゴリを `Information` 以外の値に設定した場合は、定義されているログ レベル (またはそれより高いもの) で生成されたテレメトリのみが収集されます。たとえば、これを `error` に設定すると、失敗した実行の要求データのみが追跡されます。 

  次のスクリーンショットでは、関数の [モニター] タブに表示された Host.Results テレメトリ データが示されています。:::image type="content" source="media/configure-monitoring/host-results-function-monitor.png" alt-text="関数の [モニター] タブでの Host.Results テレメトリのスクリーンショット。" lightbox="media/configure-monitoring/host-results-function-monitor-big.png":::

  次のスクリーンショットは、Application Insights の [パフォーマンス] ダッシュボードに表示された Host.Results テレメトリ データを示したものです。
  :::image type="content" source="media/configure-monitoring/host-results-application-insights.png" alt-text="Application Insights の [パフォーマンス] ダッシュボードでの Host.Results テレメトリのスクリーンショット。" lightbox="media/configure-monitoring/host-results-application-insights-big.png":::

* **Host.Aggregator と Host.Results の比較**: どちらのカテゴリでも、関数の実行に関する優れた分析情報が提供されます。必要に応じて、これらのカテゴリの 1 つから詳細情報を削除して、他のカテゴリを監視とアラートに使用できます。
サンプルを次に示します。
# <a name="v2x"></a>[v2.x+](#tab/v2)

``` json
{
  "version": "2.0",  
  "logging": {
    "logLevel": {
      "default": "Warning",
      "Function": "Error",
      "Host.Aggregator": "Error",
      "Host.Results": "Information", 
      "Function.Function1": "Information",
      "Function.Function1.User": "Error"
    },
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond": 1,
        "excludedTypes": "Exception"
      }
    }
  }
} 
```
# <a name="v1x"></a>[v1.x](#tab/v1) 
```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Warning",
      "categoryLevels": {
        "Function": "Error",
        "Host.Aggregator": "Error",
        "Host.Results": "Information",
        "Host.Executor": "Warning"
      }
    }
  },
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

この構成では、次のものが得られます。

* すべての関数とテレメトリ カテゴリの既定値は `Warning` に設定されるため (Microsoft と Worker のカテゴリを含め)、既定では、ランタイム ログとカスタム ログの両方によって生成されたエラーと警告がすべて収集されます。 

* `Function` カテゴリ ログ レベルは `Error` に設定されているため、すべての関数について、既定では例外とエラー ログだけが収集されます (依存関係、ユーザー生成メトリック、ユーザー生成イベントはスキップされます)。

* `Host.Aggregator` カテゴリは `Error` ログ レベルに設定されているため、関数呼び出しからの集計情報は `customMetrics` Application Insights テーブルに収集されず、実行数 (合計、成功、失敗など) に関する情報は関数の概要ダッシュボードに表示されません。

* `Host.Results` カテゴリについては、すべてのホスト実行情報が `requests` Application Insights テーブルに収集されます。 関数の [モニター] ダッシュボードと Application Insights のダッシュボードに、すべての呼び出し結果が表示されます。

* `Function1` という関数ではログ レベルを `Information` に設定しているので、この具象関数については、すべてのテレメトリ (依存関係、カスタム メトリック、カスタム イベント) が収集されます。 同じ関数で、`Function1.User` カテゴリ (ユーザー生成トレース) は `Error` に設定されているため、カスタム エラー ログだけが収集されます。 v1.x では、関数ごとの構成はサポートされていないので注意してください。 

* 例外を除き、サンプリングは種類ごとに 1 秒あたり 1 つのテレメトリ項目を送信するように構成されます。 このサンプリングは、関数アプリを実行しているサーバー ホストごとに行われるので、4 つのインスタンスがある場合、この構成により、1 秒あたり 4 つの種類ごとのテレメトリ項目と、発生する可能性があるすべての例外が出力されます。 メトリックの要求レートや例外レートなどのカウントはサンプリング レートを補正するように調整され、メトリックス エクスプローラーにはほぼ正しい値が表示されることに注意してください。  

> [!TIP]
> さまざまな構成を試して、ログ、監視、アラートの要件が満たされることを確認してください。 予期しないエラーや誤動作が発生した場合に詳細な診断ができるようにします。

### <a name="overriding-monitoring-configuration-at-runtime"></a>実行時の監視構成のオーバーライド
最後に、運用環境で特定のカテゴリのログ動作をすばやく変更する必要があり、`host.json` ファイルの変更のためだけに全体のデプロイを行いたくない場合があります。 そのような場合は、[host.json の値](functions-host-json.md#override-hostjson-values)をオーバーライドすることができます。


これらの値をアプリ設定レベルで構成する (そして、host.json の変更だけでの再デプロイを回避する) には、アプリケーションの設定として同等の値を作成することにより、`host.json` の特定の値をオーバーライドする必要があります。 ランタイムによって `AzureFunctionsJobHost__path__to__setting` の形式のアプリケーション設定が検出されると、JSON の `path.to.setting` にある同等の `host.json` の設定がオーバーライドされます。 アプリケーション設定として表現した場合、JSON 階層を示すために使用されるドット (`.`) は 2 つのアンダースコア (`__`) に置き換えられます。 たとえば、以下のアプリ設定を使用して、上記の `host.json` と同じ個々の関数ログ レベルを構成できます。


| host.json のパス | アプリ設定 |
|----------------|-------------|
| logging.logLevel.default  | AzureFunctionsJobHost__logging__logLevel__default  |
| logging.logLeve.Host.Aggregator | AzureFunctionsJobHost__logging__logLevel__Host__Aggregator |
| logging.logLevel.Function | AzureFunctionsJobHost__logging__logLevel__Function |
| logging.logLevel.Function.Function1 | AzureFunctionsJobHost__logging__logLevel__Function1 |
| logging.logLevel.Function.Function1.User | AzureFunctionsJobHost__logging__logLevel__Function1.User |


設定のオーバーライドは、Azure portal の関数アプリの [構成] ブレードで直接、または Azure CLI や PowerShell スクリプトを使用して、行うことができます。

# <a name="az-cli"></a>[az cli](#tab/v2)
```azurecli-interactive
az functionapp config appsettings set --name MyFunctionApp --resource-group MyResourceGroup --settings "AzureFunctionsJobHost__logging__logLevel__Host__Aggregator=Information"
```
# <a name="powershell"></a>[PowerShell](#tab/v1) 
```powershell
Update-AzFunctionAppSetting -Name MyAppName -ResourceGroupName MyResourceGroupName -AppSetting @{"AzureFunctionsJobHost__logging__logLevel__Host__Aggregator" = "Information"}
```
---

> [!NOTE]
> アプリ設定を変更して `host.json` をオーバーライドすると、関数アプリが再起動されます。
 
## <a name="next-steps"></a>次のステップ

監視の詳細については、以下に関するページを参照してください。

+ [Azure Functions を監視する](functions-monitoring.md)
+ [Application Insights で Azure Functions のテレメトリ データを分析する](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md

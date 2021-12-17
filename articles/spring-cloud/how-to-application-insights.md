---
title: Azure Spring Cloud で Application Insights Java In-Process Agent を使用する方法
description: Azure Spring Cloud で Application Insights Java In-Process Agent を使用してアプリとマイクロサービスを監視する方法。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5652c28f90c697bc2394e2bb018bff3d8a7ddc7a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324671"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud"></a>Azure Spring Cloud での Application Insights Java In-Process Agent

この記事では、Azure Spring Cloud で Application Insights Java エージェントを使用してアプリとマイクロサービスを監視する方法について説明します。

この機能を使用すると、次のことができます。

* さまざまなフィルターを使用してトレース データを検索する。
* マイクロサービスの依存関係マップを表示する。
* 要求のパフォーマンスを確認する。
* リアルタイムのライブ メトリックを監視する。
* 要求の失敗を確認する。
* アプリケーション メトリックを確認する。

Application Insights には、次のように多くの監視可能なパースペクティブが用意されています。

* アプリケーション マップ
* パフォーマンス
* エラー
* メトリック
* ライブ メトリック
* 可用性

## <a name="enable-java-in-process-agent-for-application-insights"></a>Application Insights に対して Java In-Process Agent を有効にする

次の手順を使用して、Java In-Process Agent を有効にします。

1. サービス インスタンスのサービスの概要ページに移動します。
2. **[監視]** ペインで **[Application Insights]** エントリを選択します。
3. **[Application Insights を有効にする]** を選択して **Application Insights** 統合を有効にします。
4. Application Insights の既存のインスタンスを選択するか、新規に作成します。
   ここで、サンプリング レートを 0 から 100 にカスタマイズすることもできます。
5. **[保存]** を選択して変更を保存します。

## <a name="portal"></a>ポータル

1. **[サービス | 概要]** ページに移動し、 **[監視]** セクションで **[Application Insights]** を選択します。
2. **[Application Insights を有効にする]** を選択して、Azure Spring Cloud で Application Insights を有効にします。
3. **Application Insights** を有効にすると、1 つのオプションのサンプリング レート (既定値は 10.0%) を構成できます。

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

> [!Note]
> 異なる Azure Spring Cloud インスタンスで同じ AI を使用しないでください。そうしないと、混合データが表示されます。

## <a name="using-the-application-insights-feature"></a>Application Insights 機能の使用

**Application Insights** 機能を有効にすると、次のことができます。

左側のナビゲーション ペインで **[Application Insights]** を選択し、Application Insights の **[概要]** ページにジャンプします。

* **[アプリケーション マップ]** を選択して、アプリケーション間の呼び出しの状態を確認します。

   [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* SQL からのクエリなどの詳細を表示するには、customers-service と `petclinic` の間のリンクを選択します。

* 左側のナビゲーション ペインで **[パフォーマンス]** を選択して、すべてのアプリケーションの操作のパフォーマンス データと、依存関係とロールを表示します。

   [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 左側のナビゲーション ペインで **[失敗]** を選択して、アプリケーションに予期しない問題が発生してないかどうかを確認します。

   [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 左側のナビゲーション ペインで **[メトリック]** を選択して名前空間を選択すると、Spring Boot メトリックとカスタム メトリックの両方が表示されます (存在する場合)。

   [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 左側のナビゲーション ペインで **[ライブ メトリック]** を選択して、さまざまなディメンションのリアルタイム メトリックを表示します。

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 左側のナビゲーション ペインで **[可用性]** を選択し、[[Availability tests in Application Insights]\(Application Insights での可用性テスト\)](../azure-monitor/app/monitor-web-app-availability.md) を作成して Web アプリの可用性と応答性を監視します。

   [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="automation"></a>オートメーション

以下のセクションでは、Azure Resource Manager テンプレート (ARM テンプレート) または Terraform を使用して、デプロイを自動化する方法について説明します。
    
### <a name="arm-templates"></a>ARM テンプレート

ARM テンプレートを使用してデプロイするには、次の内容を *azuredeploy.json* ファイルにコピーします。 詳細については、[Microsoft.AppPlatform Spring/monitoringSettings](/azure/templates/microsoft.appplatform/spring/monitoringsettings) を参照してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

### <a name="terraform"></a>Terraform

Terraform デプロイの場合は、次のテンプレートを使用します。 詳細については、「[azurerm_spring_cloud_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_service)」を参照してください。

```terraform
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_application_insights" "example" {
  name                = "tf-test-appinsights"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  application_type    = "web"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku_name            = "S0"

  config_server_git_setting {
    uri          = "https://github.com/Azure-Samples/piggymetrics"
    label        = "config"
    search_paths = ["dir1", "dir2"]
  }

  trace {
    connection_string = azurerm_application_insights.example.connection_string
    sample_rate       = 10.0
  }

  tags = {
    Env = "staging"
  }
}
```

## <a name="cli"></a>CLI

Azure CLI コマンドを使用して Application Insights を管理できます。 次のコマンドでは、 *\<placeholder>* テキストを説明されている値に置き換える必要があります。 ここで、 *\<service-name>* プレースホルダーは Azure Spring Cloud インスタンスの名前を表します。

Azure Spring Cloud インスタンスを作成するときに Application Insights を構成するには、次のコマンドを使用します。 `app-insights` 引数には、Application Insights の名前またはリソース ID を指定できます。

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name "serviceName" \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

次の例に示すように Application Insights 接続文字列 (推奨) またはインストルメンテーション キーを使用することもできます。

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

Azure Spring Cloud インスタンスを作成するときに Application Insights を無効にするには、次のコマンドを使用します。

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable-app-insights
```

既存の Azure Spring Cloud インスタンスの Application Insights 設定を確認するには、次のコマンドを使用します。

```azurecli
az spring-cloud app-insights show \
    --resource-group <resource-group-name> \
    --name <service-name>
```

接続文字列 (推奨) またはインストルメンテーション キーを使用して Application Insights を有効にするには、次のコマンドを使用します。

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

リソース名または ID を使用して Application Insights を有効にするには、次のコマンドを使用します。

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

既存の Azure Spring Cloud インスタンスの Application Insights を無効にするには、次のコマンドを使用します。

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable
```

## <a name="java-agent-updateupgrade"></a>Java エージェントの更新またはアップグレード

Java エージェントは、JDK を使用して定期的に更新またはアップグレードされます。これにより、次のシナリオが影響を受ける可能性があります。

> [!Note]
> JDK バージョンは、四半期ごとに 1 回更新またはアップグレードされます。

* 更新またはアップグレードの前に Java エージェントを使用していた既存のアプリケーションは影響を受けません。
* 更新またはアップグレードの後に作成されたアプリケーションは、新しいバージョンの Java エージェントを利用します。
* 以前に Java エージェントを使用していなかった既存のアプリケーションでは、新しいバージョンの Java エージェントを利用するには再起動または再展開が必要になります。

## <a name="java-agent-configuration-hot-loading"></a>Java エージェント構成のホット ローディング

Azure Spring Cloud では、アプリケーションの再起動なしでエージェント構成の設定を調整するために、ホット ローディングのメカニズムを有効にしています。

> [!Note]
> ホット ローディングのメカニズムには、分単位の遅延があります。

* Java エージェントが既に有効になっている場合は、Application Insights インスタンスまたは SamplingRate を変更しても、アプリケーションを再起動する必要はありません。
* Java エージェントを有効にする場合は、アプリケーションを再起動する必要があります。
* Java エージェントを無効にすると、アプリケーションでは、分単位の遅延の後にすべての監視データの送信を停止します。 アプリケーションを再起動すると、このエージェントを Java ランタイム環境から削除できます。

## <a name="concept-matching-between-azure-spring-cloud-and-application-insights"></a>Azure Spring Cloud と Application Insights 間の概念の照合

| Azure Spring Cloud | Application Insights                                         |
| ------------------ | ------------------------------------------------------------ |
| `App`              | * __アプリケーション マップ__/ロール<br />* __ライブ メトリック__/ロール<br />* __失敗__/ロール/クラウド ロール<br />* __パフォーマンス__/ロール/クラウド ロール |
| `App Instance`     | * __アプリケーション マップ__/ロール インスタンス<br />* __ライブ メトリック__/サーバー名<br />* __失敗__/ロール/クラウド インスタンス<br />* __パフォーマンス__/ロール/クラウド インスタンス |

Azure Spring Cloud の名前 `App Instance` は、次のシナリオで変更または生成されます。

* 新しいアプリケーションを作成した。
* JAR ファイルまたはソース コードを既存のアプリケーションにデプロイした。
* ブルー/グリーン デプロイを開始した。
* アプリケーションを再起動した。
* アプリケーションのデプロイを停止し、再起動した。

データが Application Insights に格納されると、そこには、Java エージェントが有効にされた後に作成またはデプロイされた Azure Spring Cloud アプリ インスタンスの履歴が含まれます。 つまり、Application Insights ポータルでは、前日に作成され、その後、特定の時間範囲内 (たとえば、過去 24 時間) に削除されたアプリケーション データを確認できます。 次のシナリオに、この仕組みを示します。

* Java エージェントが有効になっている Azure Spring Cloud から今日の午前 8 時頃にアプリケーションを作成し、その後、今日の午前 8 時 10 分頃にそのアプリケーションに JAR ファイルをデプロイしました。 テストの後、コードを変更し、今日の午前 8 時 30 分にこのアプリケーションに新しい JAR ファイルをデプロイします。 その後、休憩を取り、午前 11 時頃に戻って、Application Insights のデータを確認します。 次の内容を見ていきます。
  * 時間範囲が過去 24 時間以内の [アプリケーション マップ] の 3 つのインスタンスと、[失敗]、[パフォーマンス]、[メトリック]。
  * 時間範囲が過去 1 時間以内の [アプリケーション マップ] の 1 つのインスタンスと、[失敗]、[パフォーマンス]、[メトリック]。
  * [ライブ メトリック] の 1 つのインスタンス。
* Java エージェントが有効になっている Azure Spring Cloud から今日の午前 8 時頃にアプリケーションを作成し、その後、今日の午前 8 時 10 分頃にそのアプリケーションに JAR ファイルをデプロイしました。 今日の午前 8 時 30 分頃、別の JAR ファイルでブルー/グリーン デプロイを試みます。 現時点で、このアプリケーションに対して 2 つのデプロイがあります。 今日の午前 11:00 頃に休憩した後に、Application Insights のデータを確認します。 次の内容を見ていきます。
  * 時間範囲が過去 24 時間以内の [アプリケーション マップ] の 3 つのインスタンスと、[失敗]、[パフォーマンス]、[メトリック]。
  * 時間範囲が過去 1 時間以内の [アプリケーション マップ] の 2 つのインスタンスと、[失敗]、[パフォーマンス]、[メトリック]。
  * [ライブ メトリック] の 2 つのインスタンス。
## <a name="see-also"></a>関連項目

* [Azure Spring Cloud で分散トレースを使用する](./how-to-distributed-tracing.md)
* [ログとメトリックの分析](diagnostic-services.md)
* [リアルタイムでログをストリームする](./how-to-log-streaming.md)
* [アプリケーション マップ](../azure-monitor/app/app-map.md)
* [ライブ メトリック](../azure-monitor/app/live-stream.md)
* [パフォーマンス](../azure-monitor/app/tutorial-performance.md)
* [エラー](../azure-monitor/app/tutorial-runtime-exceptions.md)
* [メトリック](../azure-monitor/essentials/tutorial-metrics.md)
* [ログ](../azure-monitor/logs/data-platform-logs.md)
---
title: Azure Spring Cloud で Application Insights Java In-Process Agent を使用する方法
description: Azure Spring Cloud で Application Insights Java In-Process Agent を使用してアプリとマイクロサービスを監視する方法。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 457b21a0d84202cc712d5b1b719f5239de0e3391
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108324673"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Azure Spring Cloud での Application Insights Java In-Process Agent (プレビュー)

この記事では、Azure Spring Cloud で Application Insights Java エージェントを使用してアプリとマイクロサービスを監視する方法について説明します。 

この機能を使用すると、次のことができます。

* さまざまなフィルターを使用してトレース データを検索する。
* マイクロサービスの依存関係マップを表示する。
* 要求のパフォーマンスを確認する。
* リアルタイムのライブ メトリックを監視する。
* 要求の失敗を確認する。
* アプリケーション メトリックを確認する。

Application Insights には、次のようにさまざまな観察できるパースペクティブが用意されています。

* アプリケーション マップ
* パフォーマンス
* エラー
* メトリック
* ライブ メトリック
* 可用性

## <a name="enable-java-in-process-agent-for-application-insights"></a>Application Insights に対して Java In-Process Agent を有効にする

次の手順を使用して、Java In-Process Agent プレビュー機能を有効にします。

1. サービス インスタンスのサービスの概要ページに移動します。
2. [監視] ブレードの下にある **[Application Insights]** エントリをクリックします。
3. **[Application Insights を有効にする]** ボタンをクリックして **Application Insights** 統合を有効にします。
4. Application Insights の既存のインスタンスを選択するか、新規に作成します。
5. **[Enable Java in-process agent]\(Java In-Process Agent を有効にする\)** をクリックして、Java In-Process Agent 機能のプレビューを有効にします。 ここで、サンプリング レートを 0 から 100 にカスタマイズすることもできます。
6.  **[保存]** をクリックして変更内容を保存します。

## <a name="portal"></a>ポータル

1. **[サービス | 概要]** ページに移動し、 **[監視]** セクションで **[Application Insights]** を選択します。 
2. **[Application Insights を有効にする]** をクリックして、Azure Spring Cloud で Application Insights を有効にします。
3. **[Enable Java in-process agent]\(Java In-Process Agent を有効にする\)** をクリックして、Java IPA プレビュー機能を有効にします。 IPA プレビュー機能を有効にすると、1 つのオプションのサンプリング レート (既定値は 10.0%) を構成できます。

  [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Application Insights 機能の使用

**Application Insights** 機能を有効にすると、次のことができます。

左側のナビゲーション ペインで **[Application Insights]** をクリックし、Application Insights の **[概要]** ページにジャンプします。 

* **[アプリケーション マップ]** をクリックして、アプリケーション間の呼び出しの状態を確認します。

  [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* SQL からのクエリなどの詳細を表示するには、customers-service と `petclinic` の間のリンクをクリックします。

* 左側のナビゲーション ペインで **[パフォーマンス]** をクリックして、すべてのアプリケーションの操作のパフォーマンス データと、依存関係とロールを表示します。

  [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 左側のナビゲーション ペインで **[失敗]** をクリックして、アプリケーションに予期しない問題が発生してないかどうかを確認します。

  [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 左側のナビゲーション ペインで **[メトリック]** をクリックして名前空間を選択すると、Spring Boot メトリックとカスタム メトリックの両方が表示されます (存在する場合)。

  [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 左側のナビゲーション ペインで **[ライブ メトリック]** をクリックして、さまざまなディメンションのリアルタイム メトリックを表示します。

  [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 左側のナビゲーション ペインで **[可用性]** をクリックし、[[Availability tests in Application Insights]\(Application Insights での可用性テスト\)](../azure-monitor/app/monitor-web-app-availability.md) を作成して Web アプリの可用性と応答性を監視します。

  [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM テンプレート

Azure Resource Manager テンプレートを使用するには、次の内容を `azuredeploy.json` にコピーします。

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

## <a name="cli"></a>CLI

CLI コマンドを使用して ARM テンプレートを適用します。

* 既存の Azure Spring Cloud インスタンスの場合:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* 新しく作成された Azure Spring Cloud インスタンスの場合:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* app-insight を無効にするには:

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

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

## <a name="see-also"></a>関連項目
* [Azure Spring Cloud で分散トレースを使用する](./how-to-distributed-tracing.md)
* [ログとメトリックの分析](diagnostic-services.md)
* [リアルタイムでログをストリームする](./how-to-log-streaming.md)
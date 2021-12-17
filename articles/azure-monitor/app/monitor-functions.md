---
title: Azure Functions で実行されているアプリケーションを Application Insights - Azure Monitor で監視する | Microsoft Docs
description: Azure Functions で実行されているアプリケーションとシームレスに統合された Azure Monitor を使用して、パフォーマンスを監視し、アプリケーションの問題を即座に見つけることができます。
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 5be1c91fae86c4c15f9a6d835c36e53aa0f29f14
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132020"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Monitor Application Insights で Azure Functions を監視する

[Azure Functions](../../azure-functions/functions-overview.md) には、関数を監視する Azure Application Insights との統合機能が組み込まれています。 .NET と .NETCore 以外の言語については、分散トレースの利点を最大限に活用するには、追加の言語固有のワーカーや拡張機能が必要です。 

Application Insights は、ログ、パフォーマンス、エラー データを収集し、パフォーマンスの異常を自動的に検出します。 Application Insights は、問題の診断や関数の使用状況の把握に役立つ強力な分析ツールを備えています。 アプリケーション データを可視化すると、パフォーマンスと使いやすさを継続的に向上させることができます。 ローカル関数アプリ プロジェクトの開発中に Application Insights を使用することもできます。 

必要な Application Insights インストルメンテーションは、Azure Functions に組み込まれています。 唯一必要なのは、関数アプリを Application Insights リソースに接続するための有効なインストルメンテーション キーです。 インストルメンテーション キーは、関数アプリのリソースが Azure で作成されるときに、アプリケーションの設定に追加する必要があります。 関数アプリにまだこのキーがない場合は、手動で設定できます。 詳細については、[Azure Functions の監視](../../azure-functions/functions-monitoring.md?tabs=cmd)に関するページを参照してください。

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Java アプリケーションの分散トレース (パブリック プレビュー)

> [!IMPORTANT]
> この機能は現在、Windows と Linux の両方の Java Azure Functions でパブリック プレビュー段階にあります。

アプリケーションが Java で記述されている場合は、関数アプリケーションからの豊富なデータ (要求、依存関係、ログ、メトリックなど) を表示できます。 この追加データによって、エンドツーエンドのトランザクションの表示や診断を行えるほか、アプリケーション マップで多数のトランザクションを集計して、システムの相互作用の状況を示すトポロジ ビューや、平均パフォーマンスとエラー率を確認できます。

このエンドツーエンドの診断とアプリケーション マップによって、トランザクション/要求を個別に可視化できます。 この 2 つの機能を併用すれば、信頼性に関する問題の根本原因やパフォーマンスのボトルネックを要求単位で特定できるようになります。

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Java 関数アプリの分散トレースを有効にする方法

関数アプリの [概要] ブレードに移動し、構成に移動します。 [アプリケーション設定] で、[+ 新しいアプリケーション設定] をクリックします。 

> [!div class="mx-imgBorder"]
> ![[設定] で、新しいアプリケーション設定を追加する](./media//functions/create-new-setting.png)

次のアプリケーション設定を以下の値で追加し、左上の [保存] をクリックします。 これで完了です。

#### <a name="windows"></a>Windows
```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

#### <a name="linux-dedicatedpremium"></a>Linux Dedicated/Premium
```
ApplicationInsightsAgent_EXTENSION_VERSION -> ~3
```

#### <a name="linux-consumption"></a>Linux Consumption
```
APPLICATIONINSIGHTS_ENABLE_AGENT: true
```

## <a name="distributed-tracing-for-python-function-apps"></a>Python 関数アプリの分散トレース

Redis、Memcached、MongoDB などのサービスからカスタム テレメトリを収集するには、[OpenCensus Python 拡張機能](https://github.com/census-ecosystem/opencensus-python-extensions-azure)を使用して、[テレメトリをログに記録](../../azure-functions/functions-reference-python.md?tabs=azurecli-linux%2capplication-level#log-custom-telemetry)できます。 サポートされているサービスの一覧は、[こちら](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib)で確認できます。

## <a name="next-steps"></a>次の手順

* 「[Azure Functions を監視する](../../azure-functions/functions-monitoring.md)」で監視の手順と詳細情報を確認する
* [分散トレース](./distributed-tracing.md)の概要を確認する
* [アプリケーション マップ](./app-map.md?tabs=net)を業務で活用する方法を確認する
* [Java アプリの要求と依存関係](./java-in-process-agent.md)について確認する
* [Azure Monitor](../overview.md) と [Application Insights](./app-insights-overview.md) の詳細を確認する

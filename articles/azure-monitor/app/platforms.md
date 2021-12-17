---
title: 'Application Insights: 言語、プラットフォーム、統合 | Microsoft Docs'
description: Application Insights で利用できる言語、プラットフォーム、統合
ms.topic: conceptual
ms.date: 10/29/2021
ms.reviewer: olegan
ms.openlocfilehash: 44c08d1008691826299d1fa41507df26064f6274
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462028"
---
# <a name="supported-languages"></a>サポートされている言語

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>サポートされているプラットフォームとフレームワーク

### <a name="azure-service-integration-portal-enablement-arm-deployments"></a>Azure サービスの統合 (ポータルの有効化、ARM のデプロイ)
* [Azure VM および Azure 仮想マシン スケール セット](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [Azure Functions](../../azure-functions/functions-monitoring.md)
* [Azure Cloud Services](./cloudservices.md)、Web ロールと worker ロールの両方を含む

### <a name="auto-instrumentation-enable-without-code-changes"></a>自動インストルメンテーション (コード変更なしで有効化)
* [ASP.NET - IIS をホストとする Web アプリの場合](./status-monitor-v2-overview.md)
* [Java](./java-in-process-agent.md)

### <a name="manual-instrumentation--sdk-some-code-changes-required"></a>手動インストルメンテーション/SDK (一部のコード変更が必要)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript - Web](./javascript.md)
  * [React](./javascript-react-plugin.md)
  * [React Native](./javascript-react-native-plugin.md)
  * [Angular](./javascript-angular-plugin.md)
* [Windows デスクトップのアプリケーションとサービス、および worker ロール](./windows-desktop.md)
* [ユニバーサル Windows アプリ](../app/mobile-center-quickstart.md) (App Center)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)

> [!NOTE]
> [C#、Node.js、Python](opentelemetry-enable.md) では、OpenTelemetry ベースのインストルメンテーションは、プレビューとして使用できます。 各言語の公式ドキュメントの冒頭で説明されている制限事項を確認してください。 フル機能のエクスペリエンスを必要とするユーザーは、既存の Application Insights SDK を使用する必要があります。

## <a name="logging-frameworks"></a>ログ記録フレームワーク
* [ILogger](./ilogger.md)
* [Log4Net、NLog、または System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java、Log4J、または Logback](java-2x-trace-logs.md)
* [LogStash プラグイン](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>エクスポートとデータ分析
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>サポートされていない SDK
コミュニティでサポートされている Application Insights SDK が他にもいくつか存在します。 ただし、Azure Monitor では、このページに記載されているサポート対象のインストルメンテーション オプションを使用した場合のサポートのみが提供されます。 Microsoft は、他の言語のサポートを拡大する機会を常に評価しています。 最新の SDK のニュースについては、[Application Insights の Azure の更新情報](https://azure.microsoft.com/updates/?query=application%20insights)を参照してください。

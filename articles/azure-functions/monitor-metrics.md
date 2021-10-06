---
title: Azure Functions と共に Monitor メトリックを使用する
description: Azure Monitor メトリックを使用して、Azure Application Insights によって収集と格納が行われた Azure Functions テレメトリ データを表示し、クエリを実行する方法について説明します。
ms.topic: how-to
ms.date: 07/4/2021
ms.openlocfilehash: 8f9e80a50e3041ea37d765938613563b31f16af0
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215759"
---
# <a name="using-azure-monitor-metric-with-azure-functions"></a>Azure Functions と共に Azure Monitor メトリックを使用する

Azure Functions は Azure Monitor Metric と統合されているので、実行中の関数アプリによって生成されたメトリックを分析できます。 詳細については、「[Azure Monitor メトリックの概要](../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。 これらのメトリックは、関数アプリが App Service プラットフォーム上でどのように実行されているかを示します。 従量課金プランのコスト見積もりに使用されるリソース消費データを確認できます。 ログ データを含め、関数実行からの詳細なテレメトリを調査するには、Azure Monitor で [Application Insights](functions-monitoring.md) も使用する必要があります。 

## <a name="available-metrics"></a>使用可能なメトリック

Azure Monitor により、監視対象リソースのセットから数値データが収集され、時系列データベースに入力されます。 Azure Monitor により、Functions と基礎となる App Service リソースの両方に固有のメトリックが収集されます。   

### <a name="functions-specific-metrics"></a>Functions 固有のメトリック

対象となる Functions に固有のメトリックが 2 つあります。

| メトリック | 説明 |
| ---- | ---- |
| **FunctionExecutionCount** | 関数の実行回数は、関数アプリが実行された回数を示します。 この値は、アプリ内で関数が実行された回数に関連付けられています。 |
| **FunctionExecutionUnits** | 関数の実行単位は、実行時間とメモリ使用量を組み合わせたものです。  現在、Azure Monitor では、メモリ データのメトリックは使用できません。 ただし、アプリのメモリ使用量を最適化したい場合は、Application Insights によって収集されるパフォーマンス カウンター データを使用できます。 現在、このメトリックは、Linux で実行されている Premium および Dedicated (App Service) のプランではサポートされていません。|

これらのメトリックは、[従量課金プランのコストを見積もる](functions-consumption-costs.md)場合に特に使用されます。 

### <a name="general-app-service-metrics"></a>一般的な App Service メトリック

関数固有のメトリック以外にも、App Service プラットフォームには、関数アプリの監視に使用できるメトリックが実装されています。 詳細な一覧については、[App Service アプリで使用できるメトリック](../app-service/web-sites-monitor.md#understand-metrics)に関するセクションを参照してください。

## <a name="accessing-metrics"></a>メトリックにアクセスする

Monitor メトリック データを取得するには、[Azure portal](https://portal.azure.com) の [Azure Monitor メトリックス エクスプローラー](../azure-monitor/essentials/metrics-getting-started.md)または REST API を使用できます。 

次の例では、Monitor メトリックを使用して、従量課金プランで関数アプリを実行するコストを見積もることができます。 従量課金プランのコストの見積もりについては、「[従量課金プランのコストの見積もり](functions-consumption-costs.md)」を参照してください。

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]  

Monitor エクスプローラーを使用してメトリックを表示する方法の詳細については、[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Functions の監視の詳細を確認してください。

+ [Azure Functions を監視する](functions-monitoring.md)
+ [Application Insights で Azure Functions のテレメトリを分析する](analyze-telemetry-data.md)

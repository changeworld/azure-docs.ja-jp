---
title: Azure Application Insights - Azure Functions でサポートされる機能 |Microsoft Docs
description: Azure Functions でサポートされる Application Insights の機能
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: dd28bc3925b0f07a441c46a26498ef1a14c3e650
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510325"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions でサポートされる Application Insights の機能

Azure Functions では、ILogger インターフェイス経由で使用できる、Application Insights との[組み込みの統合](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)を提供しています。 現在サポートされている機能の一覧を次に示します。 [概要](https://github.com/Azure/Azure-Functions/wiki/App-Insights)については、Azure Functions のガイドを確認してください。

## <a name="supported-features"></a>サポートされる機能

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **自動収集の対象**        |                 |                   |               
| &bull; 要求                     | はい             | はい               | 
| &bull; 例外                   | はい             | はい               | 
| &bull; パフォーマンス カウンター         | はい             |                   |
| &bull; 依存関係                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | はい               | 
| | | | 
| **サポートされる機能**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | はい             | はい               | 
| &nbsp;&nbsp;&nbsp;&mdash; コントロール チャネルの保護|                 | はい               | 
| &bull; サンプリング                     | はい             | はい               | 
| &bull; ハートビート                   |                 | はい               | 
| | | | 
| **相関関係**                       |                   |                   |               
| &bull; ServiceBus                     |                   | はい               | 
| &bull; EventHub                       |                   | はい               | 
| | | | 
| **構成可否**                      |                   |                   |           
| &bull;完全に構成可能。<br/>手順については、[Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) を確認する。<br/>すべてのオプションについては、[Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) を確認する。               |                   | はい                   | 


## <a name="performance-counters"></a>パフォーマンス カウンター

パフォーマンス カウンターの自動収集は、Windows マシンでのみ機能します。


## <a name="live-metrics--secure-control-channel"></a>Live Metrics とコントロール チャネルの保護

指定したカスタム フィルター条件は、Application Insights SDK の Live Metrics コンポーネントに送信されます。 フィルターに顧客 ID などの機密情報が含まれている可能性があります。 シークレット API キーを使用してチャネルをセキュリティで保護できます。 手順については、「[コントロール チャネルの保護](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)」を参照してください。

## <a name="sampling"></a>サンプリング

Azure Functions では、構成の中で、サンプリングが既定で有効になっています。 詳細については、[サンプリングの構成](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)に関するページをご覧ください。

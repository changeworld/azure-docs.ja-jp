---
title: インクルード ファイル
description: インクルード ファイル
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 90de751f416ca611f3c674232c224199ad7af717
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310169"
---
アプリケーションごと (インストルメンテーション キーごと) のメトリックとイベントの数には制限があります。 制限は、選択する[料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

| リソース | 既定の制限 | 注
| --- | --- | --- |
| 1 日あたりの合計データ量 | 100 GB | 上限を設定することでデータを削減できます。 さらにデータが必要な場合は、ポータルで上限を最大 1,000 GB まで引き上げることができます。 1,000 GB を超える容量については、AIDataCap@microsoft.com までメールでご連絡ください。
| 調整 | 32K イベント/秒 | 制限は 1 分以上にわたって測定されます。
| データの保持 | 90 日間 | このリソースは、[Search](../articles/application-insights/app-insights-diagnostic-search.md)、[Analytics](../articles/application-insights/app-insights-analytics.md)、および[メトリックス エクスプローラー](../articles/application-insights/app-insights-metrics-explorer.md)用です。
| [可用性の複数手順のテスト](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)の詳細な結果の保持 | 90 日間 | このリソースは、各手順の詳細な結果を提供します。
| イベントの最大サイズ | 64 K | 
| プロパティとメトリック名の長さ | 150 | [型スキーマ](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)に関するページを参照してください。
| プロパティ値の文字列の長さ | 8,192 | [型スキーマ](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)に関するページを参照してください。
| トレースおよび例外のメッセージ長 | 10K | [型スキーマ](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)に関するページを参照してください。
| アプリあたりの[可用性テスト](../articles/application-insights/app-insights-monitor-web-app-availability.md)の数 | 100 |
| [プロファイラー](../articles/application-insights/app-insights-profiler.md)のデータ保持期間 | 5 日 |
| [プロファイラー](../articles/application-insights/app-insights-profiler.md)の 1 日あたりの送信データ | 10 GB |

詳細については、[Application Insights の価格とクォータ](../articles/application-insights/app-insights-pricing.md)に関するページを参照してください。
---
title: "Azure Application Insights Telemetry のデータ モデル - トレース テレメトリ | Microsoft Docs"
description: "トレース テレメトリ用の Application Insights データ モデル"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: 0398774e21d89fd084e6929bc5e410697d2aafaa
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="trace-telemetry-application-insights-data-model"></a>トレース テレメトリ: Application Insights データ モデル

トレース テレメトリ ([Application Insights](app-insights-overview.md)) は、テキスト検索される `printf` スタイルのトレース ステートメントを表します。 `Log4Net`、`NLog`、およびその他のテキスト ベースのログ ファイルのエントリは、この型のインスタンスに変換されます。 トレースには、機能拡張としての測定はありません。

## <a name="message"></a>メッセージ

トレース メッセージ。

最大長: 32,768 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>次のステップ

- [Application Insights で .NET トレース ログを調べます](app-insights-asp-net-trace-logs.md)。
- [Application Insights で Java トレース ログを調べます](app-insights-java-trace-logs.md)。
- Application Insights の型とデータ モデルについては、[データ モデル](application-insights-data-model.md)に関するページを参照してください。
- [カスタム トレース テレメトリを記述します](app-insights-api-custom-events-metrics.md#tracktrace)。
- Application Insights でサポートされている[プラットフォーム](app-insights-platforms.md)を確認します。

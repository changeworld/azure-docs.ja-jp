---
title: Azure Application Insights Telemetry のデータ モデル - トレース テレメトリ | Microsoft Docs
description: トレース テレメトリ用の Application Insights データ モデル
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 2487df93baec80c0e4f7741aa7b052ac89459b3b
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "35636220"
---
# <a name="trace-telemetry-application-insights-data-model"></a>トレース テレメトリ: Application Insights データ モデル

トレース テレメトリ ([Application Insights](app-insights-overview.md)) は、テキスト検索される `printf` スタイルのトレース ステートメントを表します。 `Log4Net`、`NLog`、およびその他のテキスト ベースのログ ファイルのエントリは、この型のインスタンスに変換されます。 トレースには、機能拡張としての測定はありません。

## <a name="message"></a>Message

トレース メッセージ。

最大長: 32,768 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>次の手順

- [Application Insights で .NET トレース ログを調べます](app-insights-asp-net-trace-logs.md)。
- [Application Insights で Java トレース ログを調べます](app-insights-java-trace-logs.md)。
- Application Insights の型とデータ モデルについては、[データ モデル](application-insights-data-model.md)に関するページを参照してください。
- [カスタム トレース テレメトリを記述します](app-insights-api-custom-events-metrics.md#tracktrace)。
- Application Insights でサポートされている[プラットフォーム](app-insights-platforms.md)を確認します。

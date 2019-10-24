---
title: Azure Application Insights Telemetry のデータ モデル - トレース テレメトリ | Microsoft Docs
description: トレース テレメトリ用の Application Insights データ モデル
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 855a93d8a6350c625fe0820fae83644aec3459ed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678117"
---
# <a name="trace-telemetry-application-insights-data-model"></a>トレース テレメトリ:Application Insights データ モデル

トレース テレメトリ ([Application Insights](../../azure-monitor/app/app-insights-overview.md)) は、テキスト検索される `printf` スタイルのトレース ステートメントを表します。 `Log4Net`、`NLog`、およびその他のテキスト ベースのログ ファイルのエントリは、この型のインスタンスに変換されます。 トレースには、機能拡張としての測定はありません。

## <a name="message"></a>Message

トレース メッセージ。

最大長:32768 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>次の手順

- [Application Insights で .NET トレース ログを調べます](../../azure-monitor/app/asp-net-trace-logs.md)。
- [Application Insights で Java トレース ログを調べます](../../azure-monitor/app/java-trace-logs.md)。
- Application Insights の型とデータ モデルについては、[データ モデル](data-model.md)に関するページを参照してください。
- [カスタム トレース テレメトリを記述します](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)。
- Application Insights でサポートされている[プラットフォーム](../../azure-monitor/app/platforms.md)を確認します。

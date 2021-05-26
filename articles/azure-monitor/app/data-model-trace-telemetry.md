---
title: Azure Application Insights データ モデル - トレース テレメトリ
description: トレース テレメトリ用の Application Insights データ モデル
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: cb298812b9970882c4bb3c7d2562bb5cbbf69b2d
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110098904"
---
# <a name="trace-telemetry-application-insights-data-model"></a>トレース テレメトリ:Application Insights データ モデル

トレース テレメトリ ([Application Insights](./app-insights-overview.md)) は、テキスト検索される `printf` スタイルのトレース ステートメントを表します。 `Log4Net`、`NLog`、およびその他のテキスト ベースのログ ファイルのエントリは、この型のインスタンスに変換されます。 トレースには、機能拡張としての測定はありません。

## <a name="message"></a>Message

トレース メッセージ。

最大長:32768 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>次のステップ

- [Application Insights で .NET トレース ログを調べます](./asp-net-trace-logs.md)。
- [Application Insights で Java トレース ログを調べます](java-2x-trace-logs.md)。
- Application Insights の型とデータ モデルについては、[データ モデル](data-model.md)に関するページを参照してください。
- [カスタム トレース テレメトリを記述します](./api-custom-events-metrics.md#tracktrace)。
- Application Insights でサポートされている[プラットフォーム](./platforms.md)を確認します。


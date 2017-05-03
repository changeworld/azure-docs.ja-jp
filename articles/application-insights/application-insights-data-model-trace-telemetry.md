---
title: "Azure Application Insights Telemetry のデータ モデル - トレース テレメトリ | Microsoft Docs"
description: "トレース テレメトリ用の Application Insights データ モデル"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: e7825b26c47a80debf92be1ad166e5a18bae4a61
ms.lasthandoff: 04/22/2017


---
# <a name="trace-telemetry-application-insights-data-model"></a>トレース テレメトリ: Application Insights データ モデル

トレース テレメトリは、テキスト検索される `printf` スタイルのトレース ステートメントを表します。 `Log4Net`、`NLog`、およびその他のテキスト ベースのログ ファイルのエントリは、この型のインスタンスに変換されます。 トレースには、機能拡張としての測定はありません。

## <a name="message"></a>メッセージ

トレース メッセージ。

最大長: 32,768 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>次のステップ

- Application Insights の型とデータ モデルについては、[データ モデル](/application-insights-data-model.md)に関するページを参照してください。
- [Application Insights で .NET トレース ログを調べます](/app-insights-asp-net-trace-logs.md)。
- [Application Insights で Java トレース ログを調べます](/app-insights-java-trace-logs.md)。
- Application Insights でサポートされている[プラットフォーム](/app-insights-platforms.md)を確認します。


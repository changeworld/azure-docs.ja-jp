---
title: Azure Application Insights Telemetry のデータ モデル - 例外テレメトリ | Microsoft Docs
description: 例外テレメトリ用の Application Insights データ モデル
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
ms.openlocfilehash: dc5480b90ef6b5520f47c51f0c105202d7071089
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093595"
---
# <a name="exception-telemetry-application-insights-data-model"></a>例外テレメトリ: Application Insights データ モデル

[Application Insights](app-insights-overview.md)で、例外のインスタンスは、監視対象のアプリケーションの実行中に発生したハンドルされるまたはハンドルされない例外を表します。

## <a name="problem-id"></a>問題 ID

コードで例外がスローされた場所の識別子。 例外をグループ化するために使用されます。 通常は、例外の種類と呼び出し履歴の関数の組み合わせです。

最大長: 1,024 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="exception-details"></a>例外の詳細

(拡張予定)

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>カスタム測定値

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>次の手順

- Application Insights の型とデータ モデルについては、[データ モデル](application-insights-data-model.md)に関するページを参照してください。
- [Application Insights で Web アプリの例外を診断する](app-insights-asp-net-exceptions.md)方法を確認します。
- Application Insights でサポートされている[プラットフォーム](app-insights-platforms.md)を確認します。

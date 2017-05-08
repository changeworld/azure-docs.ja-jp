---
title: "Azure Application Insights Telemetry のデータ モデル - イベント テレメトリ | Microsoft Docs"
description: "イベント テレメトリ用の Application Insights データ モデル"
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
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>イベント テレメトリ: Application Insights データ モデル

イベントは、アプリケーション内で特定の時点で発生したアクションを表します。 通常は、ボタンのクリックや注文のチェックアウトなどのユーザーの操作です。 初期化や構成の更新などのアプリケーション ライフ サイクル イベントの場合もあります。 イベント名は、短い小さな基数の文字列であると想定されています。 

意味的には、イベントは、要求に相関付けられていてもいなくてもかまいません。 ただし、適切に使用すれば、イベント テレメトリは、要求やトレースよりも重要になります。 イベントはビジネス テレメトリを表しているため、独立したより緩やかなサンプリングの対象にする必要があります。

## <a name="name"></a>名前

イベント名。 適切にグループ化でき、有用なメトリックになるように、小さな基数を保持します。

最大長: 512 文字

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>カスタム測定値

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>次のステップ

- Application Insights の型とデータ モデルについては、[データ モデル](/application-insights-data-model.md)に関するページを参照してください。
- [カスタムのイベントとメトリックのための Application Insights API](/app-insights-asp-net-dependencies.md) の使用方法を確認します。
- Application Insights でサポートされている[プラットフォーム](/app-insights-platforms.md)を確認します。


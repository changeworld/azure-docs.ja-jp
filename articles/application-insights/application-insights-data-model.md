---
title: "Azure Application Insights Telemetry のデータ モデル | Microsoft Docs"
description: "Application Insights データ モデルの概要"
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
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Application Insights Telemetry のデータ モデル

Application Insights では、Application Performance Management (APM) のテレメトリ データ モデルを定義します。 このモデルは、データ収集を標準化し、プラットフォームと言語に依存しない監視シナリオを作成できるようにします。 Application Insights で収集されたデータは、典型的なアプリケーション実行パターンをモデル化します。

![Application Insights のアプリケーション モデル](./media/application-insights-data-model/application-insights-data-model.png)

外部***要求***を受信するエンドポイントを持つアプリケーション (Web アプリケーション) と、定期的に "ウェイクアップ" してどこかに格納されているデータを処理するアプリケーション (Web ジョブまたは関数) の 2 種類のアプリケーションがあります。 どちらの場合も、***操作***の一意の実行を呼び出します。 操作は成功するか、***例外***で失敗します。または、他のサービス/ストレージに依存してビジネス ロジックを実行することがあります。 これらの概念を反映するために、Application Insights データ モデルは[要求](./application-insights-data-model-request-telemetry.md)、[例外](/application-insights-data-model-exception-telemetry.md)、[依存関係](/application-insights-data-model-dependency-telemetry.md)という 3 種類のテレメトリを定義します。

通常、これらの種類はアプリケーション フレームワークによって定義され、SDK によって自動的に収集されます。 `ASP.NET MVC` は、そのモデル ビュー コントローラーのプラミングで要求実行の概念を定義し、要求の開始と停止をマークします。 SQL の依存関係呼び出しは `System.Data` で定義されます。 HTTP エンドポイントの呼び出しは `System.Net` で定義されます。 カスタム プロパティと測定値を使用して特定のプラットフォームとフレームワークによって収集されたテレメトリの種類を拡張することができます。 ただし、カスタム テレメトリをレポートすることが必要な場合もあります。 `Log4Net` や `System.Diagnostics` などの使い慣れたインストルメンテーション フレームワークを使用して、診断ログ記録を実装することができます。 または、ユーザーとサービスのやり取りをキャプチャして、使用状況のパターンを分析することが必要な場合があります。 Application Insights は、[トレース](/application-insights-data-model-trace-telemetry.md)、[イベント](/application-insights-data-model-event-telemetry.md)、[メトリック](/application-insights-data-model-metric-telemetry.md)という 3 つの追加のデータ型を認識して、これらのシナリオをモデル化します。

Application Insights Telemetry モデルは、テレメトリをそれが属する操作に[関連付ける](/correlation.md)方法を定義します。 たとえば、要求で SQL Database を呼び出し、診断情報を記録できます。 要求テレメトリに関連付けられるこれらのテレメトリ項目の相関関係コンテキストを設定することができます。

## <a name="schema-improvements"></a>スキーマの強化

Application Insights データ モデルはシンプルで基本的でありながら、アプリケーション テレメトリをモデル化するのに十分に強力な方法です。 基本的なシナリオをサポートするモデルをシンプルかつスリムに維持しながら、高度な用途に対してスキーマを拡張できるよう努めています。

データ モデルまたはスキーマの問題やご提案を報告するには、GitHub の[ApplicationInsights ホーム](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) リポジトリを使用してください。

## <a name="next-steps"></a>次のステップ

- Application Insights でサポートされている[プラットフォーム](/app-insights-platforms.md)を確認します。
- [テレメトリの拡張とフィルター処理](/app-insights-api-filtering-sampling.md)の方法を学習します。
- [サンプリング](/app-insights-sampling.md)を使用して、データ モデルに基づいてテレメトリの量を最小限に抑えます。


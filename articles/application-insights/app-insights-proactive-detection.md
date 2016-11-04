---
title: Application Insights のプロアクティブ検出 | Microsoft Docs
description: Application Insights は、アプリのテレメトリの詳細な分析を自動的に実行し、潜在的なパフォーマンスの問題について警告します。
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: awills

---
# Application Insights でのプロアクティブ検出
 プロアクティブ検出により、Web アプリケーションの潜在的なパフォーマンスの問題について警告を自動的に受け取ることができます。プロアクティブ診断では、アプリから [Visual Studio Application Insights](app-insights-overview.md) に送信されるテレメトリがスマートに分析されます。障害発生率が急激に上昇したり、クライアントまたはサーバーのパフォーマンスに異常なパターンが発生したりした場合に、アラートが表示されます。この機能には構成は不要です。アプリケーションから適切なテレメトリが送信されていれば動作します。

プロアクティブ検出アラートには、受信した電子メールと、[プロアクティブ検出] ブレードの両方からアクセスできます。

## プロアクティブ検出を確認する
検出結果は 2 種類の方法で確認できます。

* **電子メール**を Application Insights から受信します。典型的な例を次に示します。
  
    ![電子メール アラート](./media/app-insights-proactive-detection/03.png)
  
    ポータルで詳細を表示するには、大きなボタンをクリックします。
* アプリの概要のブレードの**プロアクティブ検出タイル**に、最近のアラートの数が表示されます。最近のアラートの一覧を確認するには、タイルをクリックします。

![最近の検出結果の表示](./media/app-insights-proactive-detection/04.png)

詳細を表示するには、対象のアラートを選択します。

## 検出される問題
検出には 3 種類あります。

* [ほぼリアルタイムの障害のアラート](app-insights-proactive-failure-diagnostics.md)。機械学習を使用して、負荷などの要因と関連付けて、アプリの要求が失敗すると予想される率を設定します。障害発生率が、予想の包絡線を超えた場合、アラートが送信されます。
* [異常診断](app-insights-proactive-anomaly-diagnostics.md)。応答時間と障害発生率の中から異常なパターンを毎日検索します。これらの問題を、場所、ブラウザー、クライアント OS、サーバー インスタンス、時刻などのプロパティに関する問題と関連付けます。
* [Azure Cloud Services](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/)。アプリを Azure Cloud Services でホストしている場合に、起動エラー、頻繁なリサイクル、ランタイム クラッシュがロール インスタンスで発生すると、アラートを受け取ります

(各通知のヘルプ リンクをクリックすると関連する記事が表示されます)。

## 次のステップ
これらの診断ツールを使用すると、アプリからテレメトリを調査できます。

* [メトリックス エクスプローラー](app-insights-metrics-explorer.md)
* [Search エクスプローラー](app-insights-diagnostic-search.md)
* [Analytics - 強力なクエリ言語](app-insights-analytics-tour.md)

プロアクティブ検出は、すべて自動化されています。ただし、アラートを追加で設定する機能が用意されています。

* [手動で構成するメトリックのアラート](app-insights-alerts.md)
* [可用性 Web テスト](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->
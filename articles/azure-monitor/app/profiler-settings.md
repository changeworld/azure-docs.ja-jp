---
title: Azure Application Insights Profiler 設定ブレード | Microsoft Docs
description: プロファイラーの状態を確認し、プロファイリング セッションを開始する
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: b656fb521ad72256e91de63e96aa261f1e94bd13
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082429"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler を構成する

## <a name="profiler-settings-page"></a>プロファイラー設定ページ

プロファイラー設定ページは **[プロファイラー]** ボタンを押すことで Application Insights Performance ページから開くことができます。

![[Configure Profiler]\(Profiler の構成\) ウィンドウの入力][configure-profiler-entry]

[Application Insights Profiler の構成] ページには 4 つの機能が含まれています。 
1. **今すぐプロファイル** - このボタンをクリックすると、Application Insights のこのインスタンスにリンクされているすべてのアプリでプロファイリング セッションが開始します。
1. **リンクされたアプリ** - この Application Insights リソースにプロファイラーを送信するアプリケーションの一覧
1. **セッションは進行中です** - **[今すぐプロファイル]** を押すと、セッションの状態がここに表示されます。
1. **最近のプロファイリング セッション** - 過去のプロファイリング セッションに関する情報が表示されます。

![オンデマンドでのプロファイラー][profiler-on-demand]

## <a name="app-service-environments-ase"></a>App Service Environments (ASE)
ASE の構成によっては、エージェントの状態を確認するための呼び出しがブロックされます。 エージェントが実際に実行されていないとき、このページにはそのように表示されます。 アプリケーションの webjob で確認できます。 ただし、すべてのアプリ設定が正しく設定されており、かつ、App Insights サイト拡張機能がアプリケーションにインストールされている場合、プロファイラーは実行され、アプリケーションに十分なトラフィックがあるとき、最近のプロファイリング セッションが表示されます。

## <a id="profileondemand"></a> Profiler を手動でトリガーする

Profiler は、ボタンを 1 回クリックすることで手動でトリガーできます。 Web パフォーマンス テストを実行しているとします。 Web アプリに負荷がかかった状態のパフォーマンスを把握するには、トレースが必要です。 ロード テストが実行されるタイミングはわかっているので、トレースがキャプチャされるタイミングをコントロールすることが重要ですが、ランダム サンプリングの間隔により欠落する場合があります。
次の手順では、このシナリオがどのように機能するかについて説明します。

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(省略可能) 手順 1:Web パフォーマンス テストを開始して Web アプリへのトラフィックを生成する

Web アプリが既にトラフィックを受信している、または手動でトラフィックを生成する場合は、このセクションをスキップして手順 2 に進みます。

Application Insights ポータルで、**[構成] > [パフォーマンス テスト]** に移動します。 [新規] ボタンをクリックして新しいパフォーマンス テストを開始します。

![新しいパフォーマンス テストを作成する][create-performance-test]

**[新しいパフォーマンス テスト]** ウィンドウで、テスト対象の URL を構成します。 すべての既定の設定をそのまま使用し、ロード テストの実行を開始します。

![ロード テストを構成する][configure-performance-test]

新しいテストはまずキューに登録済みと表示され、次に状態が進行中になります。

![ロード テストが送信されキューに登録済み][load-test-queued]

![ロード テストが進行中][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>手順 2:オンデマンドでプロファイラーを開始する

ロード テストの実行中、プロファイラーを開始して Web アプリが負荷を受けている間にトレースをキャプチャできます。
[Configure Profiler]\(Profiler の構成\) ウィンドウに移動します。


### <a name="step-3-view-traces"></a>手順 3:トレースを表示する

プロファイラーの実行が完了したら、通知の指示に従って [パフォーマンス] ページに移動し、トレースを表示します。

## <a name="troubleshooting-on-demand-profiler"></a>オンデマンドでのプロファイラーのトラブルシューティング

オンデマンド セッションの後に、次のような Profiler のタイムアウト エラー メッセージが表示されることがあります。

![Profiler のタイムアウト エラー][profiler-timeout]

このエラーが発生する理由として、次の 2 つが考えられます。

1. オンデマンドでのプロファイラー セッションは成功したものの、Application Insights が収集したデータを処理するのに時間がかかった。 データの処理が 15 分以内に完了しなかった場合、ポータルにタイムアウト メッセージが表示されます。 ただし、しばらくしてから Profiler のトレースが表示されます。 これが発生した場合、現時点ではエラー メッセージを無視してください。 現在、この問題の修正に積極的に取り組んでいます。

1. Web アプリでオンデマンド機能がない古いバージョンの Profiler エージェントが使用されている。 以前に Application Insights Profiler を有効にしたことがある場合、オンデマンド機能の使用を開始するには Profiler エージェントの更新が必要になることがあります。
  
次の手順に従って Profiler を確認し、最新の Profiler をインストールしてください。

1. App Services のアプリ設定に移動し、次の設定を確認します。
    * **APPINSIGHTS_INSTRUMENTATIONKEY**:Application Insights 用の適切なインストルメンテーション キーに置き換えます。
    * **APPINSIGHTS_PORTALINFO**:ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**:1.0.0 これらの設定のいずれかが設定されていない場合は、Application Insights の有効化ウィンドウに移動して、最新のサイト拡張機能をインストールしてください。

1. App Services ポータルの [Application Insights] ウィンドウに移動します。

    ![App Services ポータルで Application Insights を有効にします。][enable-app-insights]

1. 次のページで [更新] ボタンが表示されている場合は、それをクリックして Application Insights サイト拡張機能を更新します。これにより、最新の Profiler エージェントがインストールされます。

    ![サイト拡張機能の更新][update-site-extension]

1. 次に **[変更]** をクリックして Profiler がオンになっていることを確認し、**[OK]** を選択して変更を保存します。

    ![App Insights を変更して保存する][change-and-save-appinsights]

1. App Service の **[アプリ設定]** タブに戻り、次のアプリ設定項目が設定されていることをもう一度確認します。
    * **APPINSIGHTS_INSTRUMENTATIONKEY**:Application Insights 用の適切なインストルメンテーション キーに置き換えます。
    * **APPINSIGHTS_PORTALINFO**:ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**:1.0.0

    ![Profiler 用のアプリ設定][app-settings-for-profiler]

1. 必要に応じて、拡張機能のバージョンを確認し、利用可能な更新プログラムがないことを確認します。

    ![拡張機能の更新プログラムを確認する][check-for-extension-update]

## <a name="next-steps"></a>次の手順
[Profiler を有効にし、トレースを表示する](profiler-overview.md ?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
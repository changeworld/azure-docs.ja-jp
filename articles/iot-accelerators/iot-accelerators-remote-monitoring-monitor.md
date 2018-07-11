---
title: Azure ソリューションからの IoT デバイスの監視 | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して IoT デバイスを監視する方法を学習します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097463"
---
# <a name="tutorial-monitor-your-iot-devices"></a>チュートリアル: IoT デバイスの監視

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して、接続されている IoT デバイスを監視します。 ソリューション ダッシュボードを使用して、テレメトリ、デバイス情報、アラート、および KPI を表示します。

これらの監視機能を導入するために、このチュートリアルでは 2 つのシミュレートされたトラック デバイスを使用します。 トラックは Contoso という組織によって管理され、リモート監視ソリューション アクセラレータに接続されています。 Contoso の運用者として、フィールド上のトラックの場所と動きを監視する必要があります。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * ダッシュボードのデバイスをフィルターで絞り込む
> * テレメトリをリアルタイムで表示する
> * デバイスの詳細の表示
> * デバイスのアラートを表示する
> * システムの KPI を表示する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにリモート監視ソリューション アクセラレータのインスタンスをデプロイしておく必要があります。

まだリモート監視ソリューション アクセラレータをデプロイしていない場合は、クイック スタート「[クラウドベースのリモート監視ソリューションのデプロイ](quickstart-remote-monitoring-deploy.md)」を完了する必要があります。

## <a name="choose-the-devices-to-display"></a>表示するデバイスを選択する

**[ダッシュボード]** ページに表示する接続されたデバイスを選択するには、フィルターを使用します。 **[トラック]** デバイスのみを選択するには、フィルターのドロップダウン メニューで組み込みの **[トラック]** フィルターを選択します。

[![ダッシュボードに表示するトラックをフィルター処理する](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

フィルターを適用すると、フィルター条件に一致するデバイスのみが **[ダッシュボード]** ページのマップに表示されます。

[![トラックのみがマップに表示される](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

**[テレメトリ]** グラフで表示されるデバイスも、フィルターによって決定されます。

[![トラックのテレメトリがダッシュボードに表示される](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

フィルターを作成、編集、および削除するには、**[Manage device groups]\(デバイス グループの管理\)** を選択します。

## <a name="view-real-time-telemetry"></a>テレメトリをリアルタイムで表示する

ソリューション アクセラレータでは、リアルタイムのテレメトリがグラフとして **[ダッシュボード]** ページに表示されます。 テレメトリ グラフの上部には、現在のフィルターによって選択されているデバイスで使用可能なテレメトリの種類が表示されます。

[![トラックのテレメトリの種類](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

温度テレメトリを表示するには、**[温度]** をクリックします。

[![トラックの温度テレメトリのプロット](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>マップを使用する

マップには、現在のフィルターで選択されているシミュレートされたトラックに関する情報が表示されます。 マップを拡大または縮小して、場所の細部や広域を表示できます。 マップ上のデバイス アイコンの色は、デバイスに対して**アラート**または**警告**がアクティブであるかどうかを示します。 **アラート**と**警告**をまとめた数値がマップの左側に表示されます。

デバイスの詳細を表示するには、マップをパンおよび拡大縮小してデバイスを探し、マップ上でデバイスを選択します。 次に、デバイス ラベルをクリックして **[デバイスの詳細]** パネルを開きます。 デバイスの詳細情報は次のとおりです。

* 最新のテレメトリの値
* デバイスがサポートしているメソッド
* デバイスのプロパティ

[![ダッシュボードにデバイスの詳細を表示する](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>アラートを表示する

**[アラート]** パネルには、デバイスからの最新の警告に関する詳細情報が表示されます。

[![ダッシュボードにデバイスのアラートを表示する](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

フィルターを使用して最新のアラートの時間範囲を調整できます。 既定では、パネルには過去 1 時間のアラートが表示されます。

[![アラートを時間でフィルター処理する](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>システムの KPI を表示する

ソリューション アクセラレータによって計算されたシステム KPI は、**[ダッシュボード]** ページの **[分析]** パネルに表示されます。

[![ダッシュボードの KPI](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

ダッシュボードには、現在のデバイスと期間フィルターによって選択されたアラートに対して、次の 3 つの KPI が表示されます。

* 最も多くのアラートをトリガーした規則のアクティブなアラートの数。
* デバイスの種類ごとのアラートの割合。
* 重大なアラートの割合。

アラートの時間間隔を設定し、表示するデバイスを制御する同じフィルターによって、KPI の集計方法が決まります。 既定では、過去 1 時間に集計された KPI がパネルに表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のチュートリアルに進む場合は、リモート監視ソリューション アクセラレータをデプロイしたままにしておきます。 ソリューション アクセラレータを使用していないときの実行コストを削減するには、設定パネルでシミュレートされたデバイスを停止します。

[![テレメトリを一時停止する](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

次のチュートリアルを開始する準備ができたら、シミュレートされたデバイスを再起動することができます。

ソリューション アクセラレータが不要になった場合は、[[プロビジョニングされたソリューション]](https://www.azureiotsolutions.com/Accelerators#dashboard) ページから削除します。

![ソリューションを削除する](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リモート監視ソリューション アクセラレータの **[ダッシュボード]** ページを使用して、シミュレートされたトラックをフィルター処理および監視する方法について説明しました。 ソリューション アクセラレータを使用して接続されているデバイスの問題を検出する方法について学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [監視ソリューションに接続されているデバイスの問題の検出](iot-accelerators-remote-monitoring-automate.md)
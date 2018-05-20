---
title: リモート監視ソリューションでの高度な監視 - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューションのダッシュボードでデバイスを監視する方法を示します。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 63d5d8de82d97e7f8ca65ad04cdd4357cace0be1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>リモート監視ソリューションを使用して高度な監視を行う

このチュートリアルでは、リモート監視のダッシュボードの機能を示します。 これらの機能を紹介するために、このチュートリアルでは Contoso IoT アプリケーションのシナリオを使用します。

このチュートリアルでは、シミュレートされた 2 つの Contoso トラック デバイスを使用して、ソリューション アクセラレータのダッシュボードからデバイスを監視する方法を説明します。 Contoso の運用者として、フィールド上のトラックの場所と動きを監視する必要があります。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * ダッシュボードのデバイスをフィルターで絞り込む
> * テレメトリをリアルタイムで表示する
> * デバイスの詳細の表示
> * デバイスのアラートを表示する
> * システムの KPI を表示する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにリモート監視ソリューションのインスタンスをデプロイしておく必要があります。

まだリモート監視ソリューションをデプロイしていない場合は、「[リモート監視ソリューション アクセラレータをデプロイする](iot-suite-remote-monitoring-deploy.md)」チュートリアルを実行する必要があります。

## <a name="choose-the-devices-to-display"></a>表示するデバイスを選択する

**[ダッシュボード]** ページに表示するデバイスを選択するには、フィルターを使用します。 **[トラック]** デバイスのみを選択するには、フィルターのドロップダウン メニューで組み込みの **[トラック]** フィルターを選択します。

![ダッシュボードにトラックをフィルター表示する](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

フィルターを適用すると、フィルター条件に一致するデバイスのみが **[ダッシュボード]** ページのマップに表示されます。

![マップに表示されたトラック](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

**[テレメトリ]** グラフで表示されるデバイスも、フィルターによって決定されます。

![ダッシュボードに表示されたトラックのテレメトリ](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

フィルターの作成、編集、削除を行うには **[Manage filters]\(フィルターを管理する\)** を選択します。

## <a name="view-real-time-telemetry"></a>テレメトリをリアルタイムで表示する

ソリューション アクセラレータでは、リアルタイムの詳細なテレメトリ データがグラフとして **[ダッシュボード]** ページに表示されます。 テレメトリ グラフには、現在のフィルターによって選択されているデバイスのテレメトリが表示されます。

![トラックのテレメトリのプロット](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

表示するテレメトリの値を選択するには、グラフの上部にあるテレメトリの種類を選択します。

![トラックのテレメトリのプロット](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>マップを使用する

マップには、現在のフィルターで選択されているシミュレートされたトラックに関する情報が表示されます。 マップを拡大または縮小して、場所の細部や広域を表示できます。 マップ上のデバイス アイコンには、デバイスのアクティブな **[アラート]** または **[警告]** が示されています。 **[アラート]** と **[警告]** をまとめた数値がマップの左側に表示されます。

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>デバイスのアラートを表示する

マップには現在のフィルターで表示されているデバイスと、**[アラート]** および **[警告]** が強調表示されます。 **[アラート]** パネルには、デバイスからの最新の警告に関する詳細情報が表示されます。

![ダッシュボードにシステムのアラートを表示する](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

**[ダッシュボード]** フィルターを使用して最新のアラートの時間範囲を調整できます。 既定では、パネルには過去 1 時間のアラートが表示されます。

![アラートを時間でフィルター表示する](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>システムの KPI を表示する

**[ダッシュボード]** ページには、システムの KPI が表示されます。

![ダッシュボード KPI](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

**ダッシュボード** フィルターを使用して KPI を集計する時間の範囲を調整できます。 既定では、過去 1 時間に集計された KPI がパネルに表示されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リモート監視ソリューションでプロビジョニングされた、シミュレートされたトラックのフィルター表示や監視を、**[ダッシュボード]** ページを使用して行う方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * ダッシュボードのデバイスをフィルターで絞り込む
> * テレメトリをリアルタイムで表示する
> * デバイスの詳細の表示
> * デバイスのアラートを表示する
> * システムの KPI を表示する

デバイスの監視方法について説明したので、次に推奨される手順を以下に示します。

* [しきい値に基づくルールを使用して問題を検出する](./iot-suite-remote-monitoring-automate.md)。
* [デバイスの管理と構成を行う](./iot-suite-remote-monitoring-manage.md)。
* [トラブルシューティングを行ってデバイスの問題を修復する](./iot-suite-remote-monitoring-maintain.md)。
* [シミュレートされたデバイスを使用してソリューションをテストする](iot-suite-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->
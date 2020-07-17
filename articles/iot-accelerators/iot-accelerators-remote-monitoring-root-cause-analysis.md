---
title: アラートの根本原因分析を実施する - Azure | Microsoft Docs
description: このチュートリアルでは、Azure Time Series Insights を使ってアラートの根本原因分析を実施する方法について説明します。
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565470"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>チュートリアル: アラートの根本原因分析を実施する

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用してアラートの根本原因を診断する方法を説明します。 リモート監視ソリューション ダッシュボードでアラートがトリガーされたことを確認し、Azure Time Series Insights エクスプローラーを使って根本原因を調査します。

このチュートリアルでは、場所、高度、速度、および貨物温度のテレメトリを送信する 2 つのシミュレートされた配送トラック デバイスを使用します。 トラックは Contoso という組織によって管理され、リモート監視ソリューション アクセラレータに接続されています。 Contoso オペレーターは、1 台のトラック (delivery-truck-02) で低温アラートが記録された理由を知る必要があります。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * ダッシュボードのデバイスをフィルターで絞り込む
> * テレメトリをリアルタイムで表示する
> * Time Series Insights エクスプローラーでデータを調べる
> * 根本原因分析を実施する
> * わかったことに基づいて新しいルールを作成する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>表示するデバイスを選択する

**[ダッシュボード]** ページに表示する接続されたデバイスを選択するには、フィルターを使用します。 **[トラック]** デバイスのみを選択するには、フィルターのドロップダウン メニューで組み込みの **[トラック]** フィルターを選択します。

[![ダッシュボードに表示するトラックをフィルター処理する](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

フィルターを適用すると、フィルター条件に一致するデバイスのみが **[ダッシュボード]** のマップとテレメトリ パネルに表示されます。 **truck-02** を含む 2 台のトラックがソリューション アクセラレータに接続されていることがわかります。

## <a name="view-real-time-telemetry"></a>テレメトリをリアルタイムで表示する

ソリューション アクセラレータでは、リアルタイムのテレメトリがグラフとして **[ダッシュボード]** ページに表示されます。 既定では、グラフには高度テレメトリが表示され、時間経過とともに変化しています。

[![トラックの高度テレメトリのプロット](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

トラックの温度テレメトリを表示するには、 **[テレメトリ]** パネルで **[温度]** をクリックします。 両方のトラックの温度が過去 15 分間にどのように変化したかを見ることができます。 アラート ウィンドウでは delivery-truck-02 に対して低温のアラートがトリガーされたこともわかります。

[![低温アラートが表示された RM ダッシュボード](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>データを検索する

低温アラートの原因を特定するには、Time Series Insights エクスプローラーで配送トラックのテレメトリ データを開きます。 ダッシュボードのいずれかの **[Explore in Time Series Insights]\(Time Series Insights で調べる\)** リンクをクリックします。

[![TSI のリンクが強調表示されている RM ダッシュボード](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

エクスプローラーが起動すると、すべてのデバイスの一覧が表示されます。

[![TSI エクスプローラーの初期ビュー](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

フィルター ボックスに「**delivery-truck**」と入力してデバイスをフィルター処理し、左側のパネルで **[メジャー]** として**温度**を選択します。

[![TSI エクスプローラーのトラック温度](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

リモート監視ダッシュボードと同じビューが表示されます。 加えて、ここではアラートがトリガーされた時間範囲を拡大できるようになりました。

[![TSI エクスプローラーの拡大](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

トラックから送られた他のテレメトリ ストリームを追加することもできます。 左上隅にある **[追加]** ボタンをクリックします。 新しいウィンドウが表示されます。

[![TSI エクスプローラーの新しいウィンドウ](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

新しいウィンドウで、新しいラベルの名前を「**デバイス**」に変更して前のものと一致するようにします。 **[メジャー]** として**高度**を選択し、 **[次で分割]** の値として **iothub-connection-device-id** を選択して、高度テレメトリをビューに追加します。

[![温度と高度が表示された TSI エクスプローラー](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>アラートを診断する

現在のビューのストリームを見ると、2 台のトラックの高度プロファイルが異なることがわかります。 また、トラックの高度が高くなると **delivery-truck-02** の温度が低下しています。 トラックは同じルートをたどるようにスケジュールされていたため、オペレータは驚きます。

トラックが異なる経路を走ったかどうかを確認するため、 **[追加]** ボタンを使用してサイド パネルに別のウィンドウを追加します。 新しいウィンドウで、新しいラベルの名前を「**デバイス**」に変更して前のものと一致するようにします。 **[メジャー]** として**経度**を選択し、 **[次で分割]** の値として **iothub-connection-device-id** を選択して、経度テレメトリをビューに追加します。 **経度**ストリームの違いを見ることで、トラックが別の道を通ったことがわかります。

[![温度、高度、経度が表示された TSI エクスプローラー](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>新しいルールの作成

通常、トラックのルートは事前に最適化されていますが、オペレーターは交通量のパターン、天気、その他の予期しないイベントが原因で遅れる可能性があることを理解しており、トラックの運転手は最善の判断に基づいて直前にルートを変更することができます。 ただし、トラックの積荷の温度は重要であるため、リモート監視ソリューションで追加ルールを作成する必要があります。 1 分間隔の平均高度が 350 フィートを超えた場合に確実に警告を受け取ることが、このルールの目的です。

[![リモート監視のルール タブでの高度ルールの設定](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

ルールの作成と編集の方法については、[デバイスの問題の検出](iot-accelerators-remote-monitoring-automate.md)に関する前のチュートリアルをご覧ください。

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、リモート監視ソリューション アクセラレータで Time Series Insights エクスプローラーを使用してアラートの根本原因を診断する方法を説明します。 ソリューション アクセラレータを使用して接続されているデバイスの問題を特定して修正する方法について学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [デバイス アラートを使用して、監視ソリューションに接続されているデバイスの問題を特定し、解決する](iot-accelerators-remote-monitoring-maintain.md)

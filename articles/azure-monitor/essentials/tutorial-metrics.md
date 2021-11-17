---
title: チュートリアル - Azure リソースのメトリックを分析する
description: Azure Monitor のメトリックス エクスプローラーを使用して Azure リソースのメトリックを分析する方法について説明します
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: fc996a635caea212d185b54c5493bf781d098f57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350115"
---
# <a name="tutorial-analyze-metrics-for-an-azure-resource"></a>チュートリアル: Azure リソースのメトリックを分析する
メトリックとは、一定の間隔で自動的に収集される、リソースの状況の一部を表す数値のことです。 たとえば、仮想マシンのプロセッサ使用率、ストレージ アカウント内の空き領域、仮想ネットワークの受信トラフィックなどを通知するメトリックがあります。 メトリックス エクスプローラーは、Azure portal 内の Azure Monitor の 1 機能であり、メトリック値からグラフを作成したり、傾向を視覚的に関連付けたり、メトリック値の急上昇または急降下を調査したりすることができます。 メトリックス エクスプローラーを使用して、Azure リソースによって作成されたメトリックをもとにグラフをプロットし、リソースの正常性と使用率を調査します。 

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Azure リソースのメトリックス エクスプローラーを開く
> * グラフにプロットするメトリックを選択する
> * メトリック値のさまざまな集計を実行する
> * グラフの時間の範囲と粒度を変更する


次のビデオでは、この記事で概説している手順よりも詳細なシナリオが示されています。 メトリックを初めて使用する場合は、まずこの記事を読んでから、ビデオを見て詳細を確認することをお勧めします。 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。 

- 監視する Azure リソース。 メトリックをサポートする Azure サブスクリプションの任意のリソースを使用できます。 リソースがメトリックをサポートしているかどうかを判断するには、Azure portal のメニューにアクセスし、メニューの **[監視]** セクションに **[メトリック]** オプションがあることを確認します。


## <a name="open-metrics-explorer"></a>メトリックス エクスプローラーを開く 
リソースのメニューの **[監視]** セクションから、 **[メトリックス]** を選択します。 スコープには、既にリソースが入力されています。 次の例はストレージ アカウントの場合ですが、他の Azure サービスの場合も同様に表示されます。

:::image type="content" source="media/tutorial-metrics/metrics-menu.png" lightbox="media/tutorial-metrics/metrics-menu.png" alt-text="[メトリック] メニューのアイテム":::

スコープに複数の名前空間がある場合は、**名前空間** を選択します。 名前空間は、メトリックを簡単に見つけることができるように整理するために用意された手段です。 たとえば、ストレージ アカウントには、ファイル、テーブル、BLOB、およびキューのメトリックを格納するための個別の名前空間があります。 多くの種類のリソースは、名前空間を 1 つ備えているだけです。

選択したスコープと名前空間の利用可能なメトリックの一覧からメトリックを 1 つ選択します。

:::image type="content" source="media/tutorial-metrics/metric-picker.png" lightbox="media/tutorial-metrics/metric-picker.png" alt-text="名前空間とメトリックを選択する":::

必要に応じて、メトリックの **集計** を変更することができます。 これにより、グラフの時間粒度全体でメトリック値がどのように集計されるかが定義されます。 たとえば、時間の粒度を 15 分に設定し、集計を合計に設定した場合、グラフ内の各ポイントは、15 分セグメントごとに収集されたすべての値の合計になります。

:::image type="content" source="media/tutorial-metrics/chart.png" lightbox="media/tutorial-metrics/chart.png" alt-text="スクリーンショットには、Sum Ingress for contosoretailweb というタイトルのグラフが表示されています":::


同じグラフに複数のメトリックをプロットして表示する場合は、 **[メトリックの追加]** ボタンを使用して上記の手順を繰り返します。 1 つのビューに複数のグラフを表示する場合は、 **[+ New chart]\(+ 新規グラフ\)** ボタンを選択します。

## <a name="select-a-time-range-and-granularity"></a>時間の範囲と粒度を選択する

既定では、直近の 24 時間のメトリック データがグラフに表示されます。 時間の選択ツールを使用して、グラフの **時間の範囲** を変更するか、各データ ポイントの時間の範囲を定義する **時間の粒度** を変更します。 グラフでは、指定された集計を使用して、指定された時間粒度でサンプリングされたすべての値が計算されます。

:::image type="content" source="media/tutorial-metrics/time-picker.png" lightbox="media/tutorial-metrics/time-picker.png" alt-text="[時間の範囲] パネルを変更する":::

グラフ内で関心のある領域 (急上昇または急降下) を調査するには、**時間ブラシ** を使用します。 領域の先頭にマウス ポインターを置き、マウスの左ボタンを押したまま領域のもう一方の側にドラッグしてボタンを離します。 その時間範囲のグラフが拡大されます。 

:::image type="content" source="media/tutorial-metrics/time-brush.png" lightbox="media/tutorial-metrics/time-brush.png" alt-text="時間ブラシ":::

## <a name="apply-dimension-filters-and-splitting"></a>ディメンションのフィルターと分割を適用する
メトリックに対して追加の分析を実行したり、データの潜在的な外れ値を特定したりできる高度な機能については、次のリファレンスを参照してください。

- [フィルター処理](../essentials/metrics-charts.md#filters)を使用すると、グラフに表示するディメンション値を選択できます。 たとえば、"*サーバー応答時間*" メトリックのグラフを作成する場合は、成功した要求を表示します。 

- [分割](../essentials/metrics-charts.md#apply-splitting)を使用すると、グラフ内でディメンションの値ごとに個別のラインを表示するか、それとも値を集計して 1 本のラインとして表示するかを制御できます。 たとえば、すべてのサーバー インスタンスの応答時間を平均した 1 本のラインを表示することも、サーバーごとに個別のラインを表示することもできます。 

フィルター処理と分割が適用されたグラフの例については、[こちら](../essentials/metric-chart-samples.md)を参照してください。

## <a name="advanced-chart-settings"></a>グラフの詳細設定

グラフのスタイルおよびタイトルをカスタマイズして、グラフの詳細設定を変更することができます。 カスタマイズが完了したら、ダッシュボードにピン留めして作業内容を保存します。 メトリック アラートを構成することもできます。 Azure Monitor メトリックス エクスプローラーの上記のような高度な機能を学習するには、「[Azure メトリックス エクスプローラーの高度な機能](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis)」をご覧ください。


## <a name="next-steps"></a>次のステップ
Azure Monitor でメトリックを使用する方法を学習したので、メトリックの値に潜在的な問題がある場合に通知されるようにするメトリック アラート ルールを作成する方法を確認します。

> [!div class="nextstepaction"]
> [Azure Monitor でメトリック アラートを作成する](../alerts/tutorial-metric-alert.md)


---
title: Azure Time Series Insights プレビュー エクスプローラーでデータを視覚化する | Microsoft Docs
description: この記事では、Azure Time Series Insights プレビュー エクスプローラー Web アプリで使用できる機能とオプションについて説明します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442079"
---
# <a name="visualize-data-in-the-explorer-preview"></a>エクスプローラー プレビューでデータを視覚化する

このドキュメントでは、Azure Time Series Insights プレビューの[デモ Web アプリ](https://insights.timeseries.azure.com/preview/demo)に備わっている UI/UX の機能とインターフェイスについて説明します。 具体的には、ホストされているサンプルのレイアウトと、インターフェイスのカスタマイズ オプション、提供されているデモのナビゲーションについて取り上げます。

## <a name="prerequisites"></a>前提条件

Azure Time Series Insights プレビュー エクスプローラーを使用するには、次のことが済んでいる必要があります。

* Time Series Insights 環境をセットアップします。 インスタンスのプロビジョニングの詳細については、[Azure Time Series Insights プレビュー](./time-series-insights-update-create-environment.md)のチュートリアルを参照してください。
* 作成した Time Series Insights 環境への[データ アクセスをアカウントに対して提供](./time-series-insights-data-access.md)します。 自分自身と同じように他のユーザーにもアクセスを提供できます。
* 環境にデータをプッシュするために Time Series Insights 環境にイベント ソースを追加します。
  * [イベント ハブに接続する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * [IoT ハブに接続する方法](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>プレビュー エクスプローラーについて

Azure Time Series Insights プレビュー エクスプローラーは次の要素で構成されます。

[![エクスプローラー ビュー](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**環境パネル**</a>: Azure TSI 環境を表示します。
1. <a href="#navigation-menu">**ナビゲーション メニュー**</a>: **分析**ページと**モデル** ページを切り替えることができます。
1. <a href="#hierarchy-tree">**階層ツリー**</a>: グラフ化する特定のモデルとデータ要素を選択できます。
1. <a href="#preview-well">**タイム シリーズ ウェル**</a>: 現在選択されているデータ要素が、色分けされた表形式で表示されます。
1. <a href="#preview-chart">**グラフ パネル**</a>: 現在作業しているグラフが表示されます。
1. <a href="#time-editor-panel">**タイムライン**</a>: 作業する時間範囲を変更できます。
1. <a href="#navigation-panel">**アプリ バー**</a>: 現在のテナントなどのユーザー管理オプションが含まれており、テーマと言語の設定を変更することができます。

## <a name="environment-dropdown"></a>環境ドロップダウン

環境ドロップダウンには、アクセスできるすべての Time Series Insights 環境が表示されます。 一覧には、従量課金制環境 (プレビュー) と S1/S2 環境 (一般提供: GA) が含まれます。 

1. 表示された環境の横にあるドロップダウン矢印をクリックします。

   [![コントロール パネル](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 次に、目的の環境を選択します。

## <a name="navigation-menu"></a>ナビゲーション メニュー

  [![ナビゲーション メニュー](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

ナビゲーション メニューでは、次の 2 つのビューを選択できます。

* **分析**:モデル化されたタイム シリーズ データまたはモデル化されていないタイム シリーズ データで、グラフを作成したり、豊富な分析を実行したりできます。
* **モデル**:Time Series Insights プレビューの新しい種類、階層、インスタンスを Time Series Insights のモデルにプッシュすることができます。

## <a name="hierarchy-tree"></a>階層ツリー

階層ツリーには、選択されたデータ要素 (モデル、特定のデバイス、デバイス上のセンサーなど) が表示されます。

### <a name="model-search-panel"></a>モデル検索パネル

モデル検索パネルを使用すると、タイム シリーズ モデル階層を簡単に検索してその内部を移動し、グラフに表示する特定のタイム シリーズ インスタンスを見つけることができます。 選択したインスタンスは、現在のグラフとデータ ウェルの両方に追加されます。

  [![モデル検索パネル](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>モデルの作成

Azure Time Series Insights プレビューは、ご利用のタイム シリーズ モデルに対する完全な CRUD (Create、Read、Update、Delete) 操作をサポートします。  

* **タイム シリーズ モデルの種類**:Time Series Insights の種類では、計算を行うための変数または数式を定義できます。 それらは、特定の Time Series Insights インスタンスに関連付けられます。 種類は、1 つまたは複数の変数を持つことができます。
* **タイム シリーズ モデルの階層**:階層は、データの体系的な編成です。 階層は、Time Series Insights データ内のさまざまなエンティティ間のリレーションシップを示しています。
* **タイム シリーズ モデルのインスタンス**:インスタンスは、タイム シリーズ自体を表します。 ほとんどの場合は、環境内での資産の一意識別子である **DeviceID** または **AssetID** を持っています。

タイム シリーズ モデルの詳細については、「[Times Series Models](./time-series-insights-update-tsm.md)」(タイム シリーズ モデル) をご覧ください。

## <a name="preview-well"></a>プレビューのウェル

ウェルには、選択した TSI インスタンスに関連付けられているインスタンス フィールドとその他のメタデータが表示されます。 右側にあるチェック ボックスを使用して、現在のグラフでの特定のインスタンスの表示/非表示を切り替えることができます。 また、要素の左側にある赤い**削除** (ゴミ箱) コントロールをクリックして、現在のデータ ウェルから特定のデータ要素を削除することもできます。

  [![プレビューのウェル](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

右上の省略記号アイコンを選択して、**分析**グラフ ページのレイアウトを再構成することもできます。

  [![テレメトリ レイアウト オプション](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 次のメッセージが表示される場合、インスタンスには選択した時間範囲のデータがありません。 問題を解決するには、時間範囲を広げるか、またはインスタンスがデータをプッシュしていることを確認します。
>
> ![データ通知なし](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>プレビューのグラフ

グラフでは、TSI のインスタンスを線として表示できます。 Web コントロールをクリックすることで、環境パネル、データ モデル、時間範囲コントロール パネルを折りたたみ、グラフを大きくすることができます。

  [![プレビューのグラフの概要](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **選択されている日付範囲**:視覚化に使用できるデータ要素を制御します。

1. **内部日付範囲スライダー ツール**:2 つの終端コントロールをドラッグして、目的の時間範囲を調整します。

1. **時間範囲折りたたみコントロール**:時間範囲パネル エディターを折りたたんだり展開したりします。

1. **Y 軸形式コントロール**:Y 軸の使用可能な表示オプションを順番に切り替えます。

    * `Default`:各線に個別に Y 軸が作成されます。
    * `Stacked`:複数の線が同じ Y 軸に積み重ねられます。Y 軸のデータは選択されている線に基づいて変化します。
    * `Shared`:すべての Y 軸データが一緒に表示されます。

1. **現在のデータ要素**:現在選択されているデータ要素とそれに関連する詳細です。

現在のグラフ上でデータ ポイントを左クリックし、選択した領域を選択した終端にドラッグすることで、特定のデータ スライスを詳細に表示できます。 次の図のように、グレーの選択領域を右クリックして**ズーム**をクリックします。

  [![プレビューのグラフのズーム](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

**ズーム**操作を実行すると、選択したデータセットが表示されます。 Time Series Insights データの 3 つの Y 軸表現を順番に切り替えるには、Y 軸形式コントロールをクリックします。

  [![プレビューのグラフの Y 軸](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

次に示すのは、共有 Y 軸の例です。

  [![プレビューの共有 Y 軸](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>期間編集パネル

Time Series Insights プレビューを使用するときは、最初に時間範囲を選択します。 選択した時間範囲によって、Time Series Insights プレビュー ウィジェットで操作できるデータセットを制御します。 Time Series Insights プレビューでは、次の Web コントロールを使用して、作業する時間範囲を選択できます。

  [![日時選択パネル](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **内部日付範囲スライダー ツール**:2 つの終端コントロールをドラッグして、目的の時間範囲を調整します。 この内部日付範囲は、外部日付範囲スライダー コントロールによって制限されます。

1. **日付範囲の拡大縮小ボタン**:いずれかのボタンを選択して、目的の期間になるように時間範囲を広げたり狭めたりします。

1. **時間範囲折りたたみコントロール**:この Web コントロールを使用すると、内部日付範囲スライダー ツールを除くすべてのコントロールを非表示にできます。

1. **外部日付範囲スライダー コントロール**:終端コントロールを使用して、内部日付範囲コントロールで使用できる外部日付範囲を選択します。

1. **クイック タイム日付範囲ドロップダウン**: 過去 **30 分**、**過去 12 時間**、**カスタム範囲**など、事前設定された時間範囲の選択肢をすばやく切り替えることができます。 この値を変更すると、間隔サイズ スライダー ツールで説明されている使用可能な間隔範囲も変化します。

1. **間隔サイズ スライダー ツール**:同じ時間範囲で間隔を拡大縮小できます。 この操作により、大きい時間スライスの間での動きをより細かく制御できます。 最小ミリ秒のスライスまで滑らかにトレンドを表示して、いっそう細かく高い解像度でデータの断片を見ることができます。 スライダーの既定の開始点は、解像度、クエリ速度、および粒度のバランスをとって、選択したデータが最適に表示されるよう設定されます。

1. **日付範囲の開始と終了 Web コントロール**:この Web コントロールを使用すると、クリックして簡単に目的の日付と時刻の範囲を選択できます。 コントロールを使用して、異なるタイム ゾーンに切り替えることもできます。 変更を行った後、現在のワークスペースに適用するには、**[保存]** を選択します。

   [![日付範囲の開始と終了のパネル](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>ナビゲーション パネル

Time Series Insights プレビューのナビゲーション パネルは、TSI アプリの一番上に表示されます。 次の機能が用意されています。

### <a name="current-session-share-link-control"></a>現在のセッションの共有リンク コントロール

  [![共有アイコン](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

URL リンクをチームと共有するには、新しい **[Share]\(共有\)** アイコンを選択します。

  [![インスタンスの URL を共有する](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>テナント セクション

  [![テナントの選択](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 現在の Time Series Insights ログイン アカウントの情報が表示されます。
* Time Series Insights の使用可能なテーマを切り替えることができます。
* プレビューの[デモ Web アプリ](https://insights.timeseries.azure.com/preview/demo)を表示できます。

### <a name="theme-selection"></a>テーマの選択

新しいテーマを選択するには、右上隅にあるプロフィール アイコンをクリックします。 次に、**[Change Theme]\(テーマの変更\)** を選択します。

  [![テーマの選択](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> プロフィール アイコンをクリックして言語を選択することもできます。

Azure Time Series Insights プレビューでは、2 つのテーマがサポートされています。

* **ライト テーマ**:このドキュメントで示されている既定のテーマです。
* **ダーク テーマ**:次に示すようにエクスプローラーをレンダリングします。

  [![選択されたダーク テーマ](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 環境コントロール

### <a name="preview-terms-panel"></a>プレビューの期間パネル

このセクションは、更新された UI でのエクスプローラーの使用を試みる既存の S1/S2 環境のみに適用されます。 GA 製品とプレビューを組み合わせて使用できます。 既存の UI から更新されたエクスプローラーにいくつかの機能が追加されていますが、既存の Time Series Insights エクスプローラーで S1/S2 環境向けの完全な UI エクスペリエンスを利用できます。  

階層の代わりに、Time Series Insights 条件パネルが表示され、環境内でのクエリを定義します。 述語に基づいてデータをフィルター処理できます。

  [![Where クエリ パネル](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Time Series Insights プレビュー条件編集パネルには、次のパラメーターが表示されます。

**Where**:Where 句と、次の表で示すオペランドのセットを使用して、イベントをすばやくフィルター処理できます。 オペランドを選択して検索を実施した場合、述語はその検索に基づいて自動的に更新されます。 サポートされるオペランドの型には以下が含まれます。

| Operation | サポートされている型   | メモ |
| --- | --- | --- |
| `<`、`>`、`<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | すべてのオペランドは同じ型か NULL 定数である必要があります。 |
| `HAS` | String | 右側で使用できるのは定数文字列リテラルのみです。 空の文字列と NULL は使用できません。 |

サポートされているクエリ操作とデータ型の詳細については、「[Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)」(タイム シリーズ式 (TSX)) をご覧ください。

### <a name="examples-of-where-clauses"></a>Where 句の例

  [![Where 句の例](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**[メジャー]**:現在のグラフの要素として使用できるすべての数値列 (**Double**) が表示されるドロップダウン リスト。

**分割基準**:このドロップダウン リストには、データのグループ化に使用できるモデル内のすべてカテゴリ列 (String) が表示されます。 同じ X 軸上に表示する期間を最大 5 つまで追加できます。 目的のパラメーターを入力し、**[追加]** を選択して新しい条件を追加します。

  [![クエリ/フィルター ビュー 1](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

次の図で示す表示アイコンを選択して、グラフ パネルでの要素の表示/非表示を切り替えることができます。 赤い **[X]** をクリックすると、クエリを完全に削除できます。

  [![クエリ/フィルター ビュー 2](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>次の手順

- Azure Time Series Insights プレビューの[ストレージとイングレス](./time-series-insights-update-storage-ingress.md)の詳細を確認する。

- Time Series Insights プレビューでの[データ モデリング](./time-series-insights-update-tsm.md)に関するドキュメントを読む。

- Time Series Insights インスタンスの[診断とトラブルシューティングの方法](./time-series-insights-update-how-to-troubleshoot.md)を確認する。

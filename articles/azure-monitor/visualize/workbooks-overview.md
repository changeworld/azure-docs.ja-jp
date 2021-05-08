---
title: Azure Monitor ブックの概要
description: Azure portal 内でデータを分析し、高度な視覚的レポートを作成するためのブックの柔軟なキャンバスについて説明します。
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: a02e5fced0a9e338a32d8d8beaa9e4b5fca994e8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309481"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor ブック

ブックは、Azure portal 内でデータを分析し、高度な視覚的レポートを作成するための柔軟なキャンバスを提供します。 Azure 全体から複数のデータ ソースを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。

ブックの作成に関するビデオ チュートリアルはこちらです。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>データ ソース

ブックでは、Azure 内の複数のソースからデータを照会できます。 ブックの作成者は、このデータを変換して、基になるコンポーネントの可用性、パフォーマンス、使用状況、全体的な正常性に関する分析情報を提供できます。 たとえば、仮想マシンのパフォーマンス ログを分析して、CPU 使用率が高いインスタンスやメモリが不足しているインスタンスを特定し、その結果をグリッドとして対話型レポートに表示します。
  
しかし、ブックの真の力は、1 つのレポート内でさまざまなソースのデータを結合できることにあります。 複合リソース ビューまたはリソース間の結合を作成できるため、他の方法では不可能な、より豊富なデータと分析情報が得られます。

ブックは現在、次のデータ ソースと互換性があります。

* [ログ](../visualize/workbooks-data-sources.md#logs)
* [Metrics](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [アラート (プレビュー)](../visualize/workbooks-data-sources.md#alerts-preview)
* [ワークロードの正常性](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>視覚化

ブックには、データを視覚化するための豊富な機能セットが用意されています。 各視覚化の種類の詳細な例については、以下のリンクを参照してください。

* [[テキスト]](../visualize/workbooks-text-visualizations.md)
* [グラフ](../visualize/workbooks-chart-visualizations.md)
* [グリッド](../visualize/workbooks-grid-visualizations.md)
* [タイル](../visualize/workbooks-tile-visualizations.md)
* [ツリー](../visualize/workbooks-tree-visualizations.md)
* [グラフ](../visualize/workbooks-graph-visualizations.md)
* [複合棒グラフ](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="ブックの視覚化の例" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

## <a name="getting-started"></a>作業の開始

ブックのエクスペリエンスを探索するには、最初に Azure Monitor サービスに移動します。 これを行うには、Azure portal の検索ボックスに「**Monitor**」と入力します。

次に、 **[ブック]** を選択します。

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="赤い枠で強調表示された [ブック] ボタンのスクリーンショット" border="false":::

### <a name="gallery"></a>[ギャラリー]

ギャラリーを使用すると、すべての種類のブックの整理、並べ替え、および管理が容易になります。

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="[すべて] タブのギャラリーのスクリーンショット。" lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>ギャラリーのタブ

ギャラリーには、ブックの種類の整理に役立つ 4 つのタブがあります。

| タブ              | 説明                                       |
|------------------|---------------------------------------------------|
| すべて | 各種類 (ブック、パブリック テンプレート、マイ テンプレート) の上位 4 項目が表示されます。 ブックは変更日順に並べ替えられているので、直近に変更された 8 つのブックが表示されます。|
| ブック | 自分が作成したか、共有されている利用可能なすべてのブックの一覧が表示されます。 |
| パブリック テンプレート | Microsoft によって公開され、すぐに使用でき、ブックの作業開始に役立つ利用可能なすべてのブック テンプレートの一覧が表示されます。 カテゴリ別にグループ化されます。 |
| マイ テンプレート | 自分が作成したか、共有されている利用可能なすべてのデプロイ済みブック テンプレートの一覧が表示されます。 カテゴリ別にグループ化されます。 |

#### <a name="features"></a>特徴

* 各タブには、ブックに関する情報を含むグリッドがあります。 これには、説明、最終更新日、タグ、サブスクリプション、リソース グループ、リージョン、および共有状態が含まれます。 また、この情報でブックを並べ替えることもできます。
* リソース グループ、サブスクリプション、ブックとテンプレートの名前、またはテンプレート カテゴリでフィルター処理します。
* 複数のブックを選択して、削除または一括削除します。
* 各ブックにはコンテキスト メニュー (省略記号、すなわち末尾の 3 つのドット) があり、これを選択すると、クイック アクションの一覧が表示されます。
    * [リソースを表示] - ブックのリソースのタブにアクセスして、ブックのリソース ID の表示、タグの追加、ロックの管理などを行うことができます。
    * ブックを削除または名前変更します。
    * ブックをダッシュボードにピン留めします。

### <a name="workbooks-versus-workbook-templates"></a>ブックとブック テンプレート

1 つの "_ブック_" が緑色、複数の "_ブック テンプレート_" が紫色で表示されています。 テンプレートは、複数のユーザーとチームが柔軟に再利用できるよう設計されている、精選されたレポートとして機能します。 テンプレートを開くと、テンプレートの内容が設定された一時的なブックが作成されます。

テンプレートベースのブックのパラメーターを調整して分析を行うことができます。その際、同僚の今後のレポート エクスペリエンスを損なう心配はありません。 テンプレートを開き、調整を行って保存アイコンを選択すると、このテンプレートは緑色で表示されるブックとして保存されます。元のテンプレートは変更されません。

内部的には、テンプレートは保存されたブックとも異なります。 ブックを保存すると、関連付けられた Azure Resource Manager リソースが作成されます。一方、テンプレートを開くだけで作成される一時的なブックには、一意のリソースは関連付けられていません。 ブックでアクセス制御を管理する方法の詳細については、[ブックのアクセス制御に関する記事](../visualize/workbooks-access-control.md)を参照してください。

### <a name="exploring-a-workbook-template"></a>ブック テンプレートの探索

**[Application Failure Analysis]\(アプリケーションの障害分析\)** を選択して、既定のアプリケーション ブック テンプレートの 1 つを表示します。

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="アプリケーションの障害分析テンプレートのスクリーンショット" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

前述したように、テンプレートを開くと一時的なブックが作成され、操作できるようになります。 既定では、ブックは読み取りモードで開かれます。この場合、元のテンプレートの作成者によって作成された所定の分析エクスペリエンスの情報のみが表示されます。

この特定のブックの場合、エクスペリエンスは対話型です。 サブスクリプション、ターゲット アプリ、表示したいデータの時間範囲を調整できます。 これらの選択を行うと、HTTP 要求のグリッドも対話型になります。個々の行を選択すると、レポートの下部にある 2 つのグラフに表示されるデータが変化します。

### <a name="editing-mode"></a>編集モード

このブック テンプレートがどのように構成されているかを理解するには、 **[編集]** を選択して編集モードに切り替える必要があります。

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="ブックの [編集] ボタンのスクリーンショット。" border="false" :::

編集モードに切り替えると、ブックの個々の側面に対応する多数の **[編集]** ボックスが右側に表示されます。

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="[編集] ボタンのスクリーンショット" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

要求データのグリッドのすぐ下にある [編集] ボタンを選択すると、ブックのこの部分が、Application Insights リソースのデータに対する Kusto クエリで構成されていることがわかります。

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="基になる Kusto クエリのスクリーンショット" border="false" lightbox="./media/workbooks-overview/kusto.png":::


右側にある他の **[編集]** ボタンをクリックすると、マークダウンベースの [テキスト ボックス](../visualize/workbooks-text-visualizations.md)、[パラメーターの選択](../visualize/workbooks-parameters.md) UI 要素、その他の [グラフまたは視覚化の種類](#visualizations)など、ブックを構成する主要なコンポーネントがいくつか表示されます。

作成済みのテンプレートを編集モードで調べ、ニーズに合わせて変更を加えたうえで独自のカスタム ブックを保存することは、Azure Monitor ブックでできることを知るための優れた方法です。

## <a name="pinning-visualizations"></a>視覚化のピン留め

ブック内のテキスト、クエリ、メトリックのステップは、ブックがピン モードのとき、またはブック作成者がその要素の設定を有効にしてピン アイコンが表示されるようにしている場合に、それらの項目のピン ボタンを使用してピン留めできます。

ピン モードにアクセスするには、 **[編集]** をクリックして編集モードに移行し、上部のバーにある青いピン アイコンを選択します。 画面の右側にある対応する各ブック パーツの *[編集]* ボックスの上に、個々のピン アイコンが表示されます。

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="ピン留めエクスペリエンスのスクリーンショット。" border="false":::

> [!NOTE]
> ブックの状態は、ピン留めの時点で保存されます。ダッシュボード上でピン留めされたブックは、基になるブックが変更された場合でも更新されません。 ピン留めされたブック パーツを更新するには、そのパーツを削除して、再度ピン留めする必要があります。

## <a name="dashboard-time-ranges"></a>ダッシュボードの時間範囲

ピン留めされた項目で "*時間範囲*" パラメーターを使用するよう構成されている場合、ピン留めされたブックのクエリ パーツではダッシュボードの時間範囲が考慮されます。 ダッシュボードの時間範囲値は、時間範囲パラメーターの値として使用されます。ダッシュボードの時間範囲を変更すると、ピン留めされた項目が更新されます。 ピン留めされたパーツでダッシュボードの時間範囲が使用されている場合、時間範囲が変更されるたびに、ピン留めされたパーツのサブタイトルが更新され、ダッシュボードの時間範囲が表示されます。

さらに、時間範囲パラメーターが使用されているピン留めされたブック パーツは、ダッシュボードの時間範囲によって決定される頻度で自動的に更新されます。 クエリが最後に実行された時刻が、ピン留めされたパーツのサブタイトルに表示されます。

ピン留めされたステップに時間範囲が明示的に設定されている (時間範囲パラメーターを使用していない) 場合は、ダッシュボードの設定に関係なく、その時間範囲が常にダッシュボードに使用されます。 ピン留めされたパーツのサブタイトルには、ダッシュボードの時間範囲は表示されません。また、クエリはダッシュボードで自動更新されません。 サブタイトルには、クエリが最後に実行された時刻が表示されます。

> [!NOTE]
> "*マージ*" データ ソースが使用されているクエリは現在、ダッシュボードへのピン留め時にサポートされません。

## <a name="sharing-workbook-templates"></a>ブック テンプレートの共有

独自のブック テンプレートの作成を開始したら、より広範なコミュニティで共有することができます。 詳細情報を確認したり、既定の Azure Monitor ギャラリー ビューに含まれていない他のテンプレートを探索したりするには、[GitHub リポジトリ](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)を参照してください。 既存のブックを参照するには、GitHub の[ブック ライブラリ](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks)にアクセスします。

## <a name="next-step"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](#visualizations)します。
* ブック リソースへのアクセスを[制御](../visualize/workbooks-access-control.md)し、共有します。
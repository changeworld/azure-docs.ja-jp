---
title: クイック スタート:Metrics Advisor Web ポータル
titleSuffix: Azure Cognitive Services
description: Metrics Advisor Web ポータルを使い始める方法について説明します。
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.custom: mode-portal
ms.openlocfilehash: ca4edde32a8a1eb490eb8c62d23c22061ea5c706
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026621"
---
# <a name="quickstart-monitor-your-first-metric-by-using-the-web-portal"></a>クイック スタート: Web ポータルを使用して初めてのメトリックを監視する

Azure Metrics Advisor のインスタンスをプロビジョニングするとき、サービスの操作には、API と Web ベースのワークスペースを使用できます。 Web ベースのワークスペースは使いやすく、サービスをすぐに利用することができます。 また、設定の構成、モデルのカスタマイズ、根本原因分析の実行のための視覚的な手段も用意されています。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [無料で作成できます](https://azure.microsoft.com/free/cognitive-services)。
* Azure サブスクリプションを作成したら、Azure portal で <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor リソースを作成"  target="_blank">Metrics Advisor リソースを作成</a>して、Metrics Advisor のインスタンスをデプロイします。  

    
> [!TIP]
> * Metrics Advisor リソースのデプロイには 10 分から 30 分ほどかかる場合があります。 正常にデプロイされたら、 **[リソースに移動]** を選択します。
> * REST API を使用してサービスを対話的に操作する場合は、作成したリソースのキーとエンドポイントが必要です。 それらは、作成したリソースの **[キーとエンドポイント]** タブで確認できます。


このドキュメントでは、初めてのモニターを作成する例として SQL データベースを使用します。

## <a name="sign-in-to-your-workspace"></a>ワークスペースにサインインする

リソースが作成されたら、ご自分の Active Directory アカウントで [Metrics Advisor ポータル](https://go.microsoft.com/fwlink/?linkid=2143774)にサインインします。 ランディング ページから、**ディレクトリ**、**サブスクリプション**、作成した **ワークスペース** を選択して、 **[Get started]\(開始する\)** を選択します。 時系列データを使用するには、左側のメニューから **[Add data feed]\(データ フィードの追加\)** を選択します。

 
現在、使用可能なリージョンごとに 1 つの Metrics Advisor リソースを作成できます。 Metrics Advisor ポータルでは、いつでもワークスペースを切り替えることができます。


## <a name="time-series-data"></a>時系列データ

Metrics Advisor には、Azure SQL Database、Azure Data Explorer、Azure Table Storage など、さまざまなデータ ソース用のコネクタが用意されています。 データを接続する手順は、コネクタが異なっていても似ています。ただし、いくつかの構成パラメーターが異なる可能性があります。 詳細については、[データ ソースの接続](../data-feeds-from-different-sources.md)に関するページを参照してください。

このクイックスタートは、例として SQL データベースを使用します。 同じ手順に従って独自のデータを取り込むこともできます。


### <a name="data-schema-requirements-and-configuration"></a>データ スキーマの要件と構成

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>接続設定とクエリを構成する

目的の時系列データ ソースに接続して[データ フィードを追加します](../how-tos/onboard-your-data.md)。 最初に次のパラメーターを選択します。

* **[Source Type]\(ソースの種類\)** : 時系列データを格納するデータ ソースの種類。
* **[Granularity]\(細分性\)** : 時系列データ内の連続するデータ ポイントの間隔 (年単位、月単位、日単位など)。 サポートされている最短の間隔は 60 秒です。
* **[Ingest data since (UTC)]\(データの取り込みの開始 (UTC)\)** : 取り込む対象の最初のタイムスタンプの開始時刻。 


:::image type="content" source="../media/connection-settings.png" alt-text="接続設定を示すスクリーンショット。" lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>データの読み込み

接続とクエリの文字列を入力したら、 **[データの読み込み]** を選択します。 Metrics Advisor によって、データを読み込むための接続とアクセス許可が確認され、クエリで使用される必要なパラメーターが確認され、データ ソースの列名がチェックされます。 

この手順でエラーが発生した場合:
1. 接続文字列が有効かどうかを確認します。 
1. 十分なアクセス許可があり、インジェスト ワーカーの IP アドレスにアクセス権が付与されていることを確認します。
1. 必要なパラメーター (`@IntervalStart` と `@IntervalEnd`) がクエリで使用されているかどうかを確認します。 

### <a name="schema-configuration"></a>スキーマの構成

クエリを実行してデータが読み込まれたら、適切なフィールドを選択します。


|[選択]  |説明  |メモ  |
|---------|---------|---------|
|**Timestamp**     | データ ポイントのタイムスタンプ。 タイムスタンプが省略されている場合、Metrics Advisor では、代わりにデータ ポイントが取り込まれた時点のタイムスタンプが使用されます。 データ フィードごとに、最大 1 列をタイムスタンプとして指定できます。        | 省略可能。 指定できるのは最大 1 列です。       |
|**測定値**     |  データ フィード内の数値。 データ フィードごとに、複数のメジャーを指定できますが、少なくとも 1 列をメジャーとして選択する必要があります。        | 少なくとも 1 列を指定する必要があります。        |
|**ディメンション**     | カテゴリ値。 異なる値を組み合わせることで、特定の単一ディメンションの時系列が識別されます。 例として、国、言語、テナントなどがあります。 0 個以上の任意の数の列をディメンションとして選択できます。 文字列以外の列をディメンションとして選択する場合は、ディメンションのエクスプロージョンに注意が必要です。 | 省略可能。        |
|**無視**     | 選択した列を無視します。        | 省略可能。 クエリを使用してデータを取得することをサポートしているデータ ソースの場合、[無視] オプションはありません。       |


:::image type="content" source="../media/schema-configuration.png" alt-text="スキーマ構成を示すスクリーンショット。" lightbox="../media/schema-configuration.png":::

スキーマを構成したら、 **[Verify schema]\(スキーマの確認\)** を選択します。 Metrics Advisor によって、次のチェックが実行されます。
- クエリ対象のデータのタイムスタンプが 1 つの間隔に収まっているかどうか。 
- 1 つのメトリック間隔内で、ディメンションの組み合わせが同じ重複した値が返されているかどうか。  

### <a name="automatic-roll-up-settings"></a>自動ロールアップ設定

> [!IMPORTANT]
> 根本原因分析およびその他の診断機能を有効にする場合は、自動ロールアップ設定を構成します。 分析を有効にした後に自動ロールアップ設定を変更することはできません。

Metrics Advisor を使用すると、インジェスト中に各ディメンションに対して自動的に集計を実行できます。 その後、このサービスによって、根本原因分析やその他の診断機能で使用できる階層が構築されます。 詳細については、「[自動ロール アップ設定](../how-tos/onboard-your-data.md#automatic-roll-up-settings)」を参照してください。

データ フィードのカスタム名を指定します。これは、ワークスペースに表示されます。 **[送信]** を選択します。 

## <a name="tune-detection-configuration"></a>検出構成を調整する

データ フィードが追加されると、Metrics Advisor によって、指定された開始日からメトリック データの取り込みが試行されます。 データが完全に取り込まれるまでには、しばらく時間がかかります。インジェストの状況は、データ フィード ページの上部にある **[Ingestion progress]\(インジェスト進行状況\)** を選択して確認できます。 データが取り込まれた場合、Metrics Advisor は検出を適用し、引き続きソースの新しいデータを監視します。

検出が適用されたら、データ フィードに一覧表示されるいずれかのメトリックを選択して **メトリックの詳細ページ** を探します。 ここでは、次のことを行うことができます。 
- このメトリック下にあるすべての時系列スライスの視覚化を表示する。
- 必要な結果が得られるように検出構成を更新する。
- 異常が検出された場合の通知を設定する。

:::image type="content" source="../media/metric-details.png" alt-text="メトリックの詳細を示すスクリーンショット。" lightbox="../media/metric-details.png":::

## <a name="view-diagnostic-insights"></a>診断の分析情報を表示する

検出構成を調整すると、検出された異常がデータ内の実際の異常を反映していることがわかります。 Metrics Advisor では、根本原因を特定するために、特定のディメンションに対して多次元メトリックの分析が実行されます。 また、メトリック グラフ機能を使用して、クロスメトリック分析も実行されます。 

診断の分析情報を表示するには、時系列の視覚化に表示される赤色のドットを選択します。 これらの赤色のドットは、検出された異常を表します。 ウィンドウが開いて、インシデント分析ページへのリンクが表示されます。 

:::image type="content" source="../media/incident-link.png" alt-text="インシデントのリンクを示すスクリーンショット。" lightbox="../media/incident-link.png":::

[インシデント分析] ページには、関連する異常と診断分析情報のグループが表示されます。 以降のセクションでは、インシデントを診断するための主要な手順について説明します。

### <a name="check-the-summary-of-the-current-incident"></a>現在のインシデントの概要を確認する

概要は、このインシデント分析ページの上部にあります。 この概要には、基本情報、アクションとトレース、および分析された根本原因が含まれています。 基本情報には、影響を受ける上位の系列と図、影響の開始および終了時刻、重大度、含まれる異常の総数が含まれています。

分析された根本原因は、自動的に分析された結果です。 Metrics Advisor により、同じタイムスタンプでディメンション値が異なり、1 つのメトリック内に含まれ、時系列でキャプチャされたすべての異常が分析されます。 次に、サービスにより、関連付けが実行され、グループに関する異常がまとめてクラスタリングされ、根本原因についてのアドバイスが生成されます。

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="インシデント診断の概要を示すスクリーンショット。" lightbox="../media/diagnostics/incident-summary.png":::

これらに基づいて、現在の異常状態、そのインシデントの影響、最も可能性の高い根本原因を簡単に、いち早く把握することができます。 そして、直ちに対処してインシデントを解決できます。 

### <a name="view-cross-dimension-diagnostic-insights"></a>クロスディメンション診断の分析情報を表示する

また、診断ツリー機能を使用して、同じメトリック内の他のディメンションの異常な状態について、包括的な方法で詳細情報を取得することもできます。

複数のディメンションを持つメトリックの場合、Metrics Advisor により、時系列が診断ツリーと呼ばれる階層に分類されます。 たとえば、revenue メトリックは、region と category という 2 つのディメンションで監視されます。 `SUM` などの集計ディメンション値が必要です。 そこで、`region = SUM` および `category = SUM` の時系列は、そのツリー内のルート ノードとして分類されます。 `SUM` ディメンションで異常がキャプチャされた場合はいつでも、それを分析して、親ノードの異常に最も影響している特定のディメンション値を突き止めることができます。 各ノードを選択して展開し、詳細情報を表示します。

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="インシデント診断のクロスディメンション ビューを示すスクリーンショット。" lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights"></a>クロスメトリック診断の分析情報を表示する

1 つのメトリックの異常状態を確認するだけでは問題を分析することが難しく、場合によっては複数のメトリックを関連付けて分析する必要があります。 これを行うには、メトリック間のリレーションシップを示すメトリック グラフを構成します。 

前のセクションで説明したクロスディメンション診断の結果を使用することで、根本原因が特定のディメンション値に限定されていることを特定できます。 次に、メトリック グラフを使用して分析された根本原因のディメンションでフィルター処理し、他のメトリックの異常状態を確認します。

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="インシデント診断のクロスメトリック分析を示すスクリーンショット。" lightbox="../media/diagnostics/cross-metrics-analysis.png":::

追加の機能を使用して、より多くの診断分析情報をピボットすることもできます。 これらの機能を使用すると、異常なディメンションのドリルダウン、類似した異常の表示、メトリック間の比較を行うことができます。 詳細については、[インシデントの診断](../how-tos/diagnose-an-incident.md)に関するページを参照してください。 

## <a name="get-notified-when-new-anomalies-are-found"></a>新しい異常が見つかったときに通知を受け取る

データに異常が検出されたときに通知を受け取る場合、1 つまたは複数のメトリックに関するサブスクリプションを作成することができます。 Metrics Advisor は、フックを使用してアラートを送信します。 email hook、Web hook、Azure DevOps の 3 種類のフックがサポートされています。 ここでは例として Web hook を使用します。 

### <a name="create-a-web-hook"></a>Web hook を作成する

Metrics Advisor では、Web hook を使用して、プログラムで異常を表面化させることができます。 このサービスでは、アラートがトリガーされたときにユーザーが指定した API が呼び出されます。 詳細については、「[フックを作成する](../how-tos/alerts.md#create-a-hook)」を参照してください。 

### <a name="configure-alert-settings"></a>アラート設定の構成

フックの作成後、どのアラート通知がどのように送信されるかは、アラートの設定によって決まります。 アラートの設定は、メトリックごとに複数用意することができます。 重要な設定は 2 つあります。1 つは、対象となる異常を指定する **[Alert for]\(アラート対象\)** で、もう 1 つは、アラートに含める異常を定義する **[Filter anomaly options]\(異常のフィルター処理オプション\)** です。 詳しくは、「[アラート設定の追加または編集](../how-tos/alerts.md#add-or-edit-alert-settings)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [メトリック データを Metrics Advisor に追加する](../how-tos/onboard-your-data.md)
    - [データ フィードを管理する](../how-tos/manage-data-feeds.md)
    - [さまざまなデータ ソースを接続する](../data-feeds-from-different-sources.md)
- [クライアント ライブラリまたは REST API を使用してソリューションをカスタマイズする](./rest-api-and-client-library.md)
- [メトリックを構成して検出構成を微調整する](../how-tos/configure-metrics.md)

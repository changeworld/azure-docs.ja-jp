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
ms.custom:
- mode-portal
ms.openlocfilehash: 324b84255bd5939940a7819c521a452c1c49c8e8
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342492"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>クイック スタート:Web ポータルを使用して初めてのメトリックを監視する

Metrics Advisor インスタンスをプロビジョニングするとき、サービスの操作には、API と Web ベースのワークスペースを使用できます。 Web ベースのワークスペースは使いやすく、サービスをすぐに利用することができます。 また、設定の構成、モデルのカスタマイズ、根本原因分析の実行のための視覚的な手段も用意されています。 

* メトリック データをオンボードする
* メトリックと視覚エフェクトを表示する
* 検出構成を微調整する
* 診断の分析情報を調べる
* 異常アラートを作成してサブスクライブする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを作成したら、Azure portal で <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor リソースを作成"  target="_blank">Metrics Advisor リソースを作成</a>して、Metrics Advisor インスタンスをデプロイします。  

    
> [!TIP]
> * Metrics Advisor リソースのデプロイには 10 分から 30 分ほどかかります。 デプロイに成功したら、 **[リソースに移動]** を選択します。
> * REST API を使用してサービスを対話的に操作したい場合は、作成したリソースのキーとエンドポイントが必要となります。 それらは、作成したリソースの **[キーとエンドポイント]** タブで確認できます。


このドキュメントでは、初めてのモニターを作成する例として SQL Database を使用します。

## <a name="sign-in-to-your-workspace"></a>ワークスペースにサインインする

リソースが作成されたら、Active Directory アカウントで [Metrics Advisor ポータル](https://go.microsoft.com/fwlink/?linkid=2143774)にサインインします。 ランディング ページから、**ディレクトリ**、**サブスクリプション**、作成した **ワークスペース** を選択して、 **[Get started]\(開始する\)** を選択します。 時系列データをオンボードする場合は、左側のメニューから **[Add data feed]\(データ フィードの追加\)** を選択します。

 
現在、使用可能なリージョンごとに 1 つの Metrics Advisor リソースを作成できます。 Metrics Advisor ポータルでは、いつでもワークスペースを切り替えることができます。


## <a name="onboard-time-series-data"></a>時系列データをオンボードする

Metrics Advisor には、SQL Database、Azure Data Explorer、Azure Table Storage など、さまざまなデータ ソース用のコネクタが用意されています。 データを接続する手順は、コネクタが異なっていても似ています。ただし、いくつかの構成パラメーターが異なる可能性があります。 さまざまなデータ接続設定については、[データをさまざまなデータ フィード ソースに接続する](../data-feeds-from-different-sources.md)方法に関するページを参照してください。

このクイックスタートは、例として SQL Database を使用します。 同じ手順に従って独自のデータを取り込むこともできます。


### <a name="data-schema-requirements-and-configuration"></a>データ スキーマの要件と構成

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>接続設定とクエリを構成する

目的の時系列データ ソースに接続して[データ フィードを追加します](../how-tos/onboard-your-data.md)。 最初に次のパラメーターを選択します。

* **[Source Type]\(ソースの種類\)** : 時系列データを格納するデータ ソースの種類。
* **[細分性]** : 時系列データ内の連続するデータ ポイントの間隔 (年単位、月単位、日単位など)。 カスタマイズでサポートされている最小間隔は 60 秒です。
* **[Ingest data since (UTC)]\(データの取り込みの開始 (UTC)\)** : 取り込む対象の最初のタイムスタンプの開始時刻。 


<!-- Next, specify the **Connection string** with the credentials for your data source, and a custom **Query**, see [how to write a valid query](../tutorials/write-a-valid-query.md) for more information. -->

:::image type="content" source="../media/connection-settings.png" alt-text="接続の設定" lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>データの読み込み

接続文字列とクエリ文字列を入力したら、 **[Load data]\(データの読み込み\)** を選択します。 この操作の中で、Metrics Advisor により、データを読み込むための接続とアクセス許可の確認、クエリで使用する必要のあるパラメーター (@IntervalStart と @IntervalEnd) の確認、データ ソースの列名の確認が行われます。 

この手順でエラーが発生した場合:
1. まず、接続文字列が有効かどうかを確認します。 
2. 次に、十分なアクセス許可があり、インジェスト ワーカーの IP アドレスにアクセス権が付与されていることを確認します。
3. 次に、必要なパラメーター (@IntervalStart と @IntervalEnd) がクエリで使用されているかどうかを確認します。 

### <a name="schema-configuration"></a>スキーマの構成

クエリを実行してデータが読み込まれ、次のように表示されたら、適切なフィールドを選択します。


|[選択]  |説明  |メモ  |
|---------|---------|---------|
|**Timestamp**     | データ ポイントのタイムスタンプ。 省略されている場合、代わりに、Metrics Advisor により、データ ポイントが取り込まれた時点のタイムスタンプが使用されます。 データ フィードごとに、最大 1 列をタイムスタンプとして指定できます。        | 省略可能。 指定できるのは最大 1 列です。       |
|**測定値**     |  データ フィード内の数値。 データ フィードごとに複数のメジャーを指定できますが、少なくとも 1 列をメジャーとして選択する必要があります。        | 少なくとも 1 列を指定する必要があります。        |
|**ディメンション**     | カテゴリ値。 異なる値を組み合わせることで、国、言語、テナントなどの特定の 1 次元の時系列が識別されます。 0 個以上の任意の数の列をディメンションとして選択できます。 注: 文字列以外の列をディメンションとして選択する場合は、ディメンションのエクスプロージョンに注意が必要です。 | 省略可能。        |
|**無視**     | 選択した列を無視します。        | 省略可能。 クエリを使用してデータを取得することをサポートしているデータ ソースの場合、[無視] オプションはありません。       |


:::image type="content" source="../media/schema-configuration.png" alt-text="スキーマの構成" lightbox="../media/schema-configuration.png":::

スキーマを構成したら、 **[Verify schema]\(スキーマの確認\)** を選択します。 この操作の中で、Metrics Advisor によって以下の確認が行われます。
- クエリ対象のデータのタイムスタンプが 1 つの間隔に収まっているかどうか。 
- 1 つのメトリック間隔内で、ディメンションの組み合わせが同じ重複した値が返されているかどうか。  

### <a name="automatic-roll-up-settings"></a>自動ロール アップ設定

> [!IMPORTANT]
> **根本原因分析** およびその他の診断機能を有効にする場合は、"自動ロール アップ設定" を構成する必要があります。 有効にすると、自動ロールアップ設定を変更できなくなります。

Metrics Advisor は、インジェスト中に各ディメンションで集計 (SUM、MAX、MIN など) を自動的に実行でき、ルート ケース分析やその他の診断機能で使用される階層を構築します。 詳細については、「[自動ロール アップ設定](../how-tos/onboard-your-data.md#automatic-roll-up-settings)」を参照してください。

データ フィードのカスタム名を指定します。これは、ワークスペースに表示されます。 **[送信]** を選択します。 

## <a name="tune-detection-configuration"></a>検出構成を調整する

データ フィードが追加されると、Metrics Advisor は、指定された開始日からメトリック データの取り込みを試みます。 データが完全に取り込まれるまでには、しばらく時間がかかります。インジェストの状況は、データ フィード ページの上部にある **[Ingestion progress]\(インジェスト進行状況\)** を選択して確認できます。 データが取り込まれた場合、Metrics Advisor は検出を適用し、引き続きソースの新しいデータを監視します。

検出が適用されたら、データ フィードに一覧表示されるいずれかのメトリックを選択して **メトリックの詳細ページ** を探し、次のことを行います。 
- このメトリック下にあるすべての時系列スライスの視覚エフェクトを表示する
- 必要な結果が得られるように検出構成を更新する
- 異常が検出された場合の通知を設定する

:::image type="content" source="../media/metric-details.png" alt-text="メトリックの詳細" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>診断の分析情報を表示する

検出構成の調整後、見つかった異常は、データ内の実際の異常を反映していると考えられます。 Metrics Advisor により、多次元メトリックの分析が行われ、根本原因が特定のディメンションに絞り込まれます。また、"メトリック グラフ" を使用してメトリック間の分析も行われます。 

診断の分析情報を表示するには、時系列の視覚エフェクトに表示される赤色のドットを選択します。これらは、検出された異常を表しています。 ウィンドウが開いて、インシデント分析ページへのリンクが表示されます。 

:::image type="content" source="../media/incident-link.png" alt-text="インシデント リンク" lightbox="../media/incident-link.png":::

リンクを選択すると、関連する異常のグループに関するインシデント分析ページが、さまざまな診断の分析情報と共に表示されます。 インシデントを診断するには、次の 3 つの主要な手順があります。

### <a name="check-summary-of-current-incident"></a>現在のインシデントの概要を確認する

上部には、基本情報、アクションとトレース、分析された根本原因などの概要が表示されます。 基本情報には、"影響を受ける上位の系列" と図、"影響の開始および終了時刻"、"インシデントの深刻度"、"含まれる異常の総数" などがあります。

分析された根本原因は、自動的に分析された結果です。 Metrics Advisor により、同じタイムスタンプでディメンション値が異なる 1 つのメトリックに含まれ、時系列でキャプチャされたすべての異常が分析されます。 次に、相関関係とクラスタリングを行い、関連する異常をグループ化し、根本原因のアドバイスを生成します。

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="インシデント診断の概要" lightbox="../media/diagnostics/incident-summary.png":::

これらの情報から、現在の異常状態、そのインシデントの影響、最も可能性の高い根本原因は何かを簡単に、いち早く把握することができます。 早急な対応を行うことで、可能な限り速やかにインシデントを解決することができます。 

### <a name="view-cross-dimension-diagnostic-insights"></a>クロスディメンション診断の分析情報を表示する

基本情報と自動分析の分析情報を得たら、 **"診断ツリー"** を使用して、同じメトリック内の他のディメンションの異常状態について、より詳細な情報を総合的に得ることができます。

複数のディメンションを持つメトリックの場合、Metrics Advisor により、時系列は "診断ツリー" と呼ばれる階層に分類されます。 たとえば、"収益" というメトリックは、"リージョン" と "カテゴリ" という 2 つのディメンションで監視されます。 具象ディメンション値ですが、 **"SUM"** のような **集計済み** ディメンション値が必要です。 そこで、"リージョン" の時系列 = **"SUM"** と、"カテゴリ" = **"SUM"** の時系列がツリー内のルート ノードとして分類されます。 **"SUM"** のディメンションで異常がキャプチャされた場合、それを掘り下げて分析し、どの特定の次元の値が親ノードの異常に最も影響しているかを突き止めることができます。 各ノードをクリックすると、詳細情報が展開されます。

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="インシデント診断のクロス ディメンション ビュー" lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights-using-metrics-graph"></a>"メトリック グラフ" を使用してクロスメトリック診断の分析情報を表示する

1 つのメトリックの異常状態を確認するだけでは問題を分析することが難しく、場合によっては複数のメトリックを関連付けて分析する必要があります。 お客様は、メトリック間の関係を示す "メトリック グラフ" を構成できます。 上のクロスディメンションの診断結果を利用して、根本原因を特定のディメンション値に絞り込むことができます。 次に、"メトリック グラフ" を使用し、分析された根本原因のディメンションでフィルター処理し、他のメトリックの異常状態を確認します。
リンクをクリックすると、対応する異常に関するインシデント分析ページが、さまざまな診断の分析情報と共に表示されます。 インシデントの詳細ページには、インシデントを診断するための 3 つの主要な手順に対応する 3 つのセクションがあります。 

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="インシデント診断のクロス メトリック分析" lightbox="../media/diagnostics/cross-metrics-analysis.png":::

ただし、さらに別の機能を活用して、ディメンション別に異常をドリルダウンしたり、同様の異常を確認したり、メトリック間で比較したりすれば、他のさまざまな診断分析情報の観点から分析を行うこともできます。 詳細については、[インシデントを診断する方法](../how-tos/diagnose-an-incident.md)に関するページを参照してください。 

## <a name="get-notified-when-new-anomalies-are-found"></a>新しい異常が見つかったときに通知を受け取る

データに異常が検出されたときに通知を受け取りたい場合、1 つまたは複数のメトリックに関するサブスクリプションを作成することができます。 Metrics Advisor は、フックを使用してアラートを送信します。 電子メール、Web、Azure DevOps の 3 種類のフックがサポートされています。 ここでは例として Web hook を使用します。 

### <a name="create-a-web-hook"></a>Web hook を作成する

Web hook は、Metrics Advisor サービスからプログラム的な手段によって通知された異常を取得するためのエントリ ポイントです。アラートがトリガーされると、そこから、ユーザーによって指定された API が呼び出されます。フックの作成方法の詳細については、[アラートを構成する方法とフックを使用して通知を受け取る方法](../how-tos/alerts.md#create-a-hook)に関するページの **フックの作成** に関するセクションを参照してください。 

### <a name="configure-alert-settings"></a>アラート設定の構成

フックの作成後、どのアラート通知がどのように送信されるかは、アラートの設定によって決まります。 アラートの設定は、メトリックごとに複数用意することができます。 重要な設定は 2 つあります。1 つは、対象となる異常を指定する **[Alert for]\(アラート対象\)** で、もう 1 つは、アラートに含める異常を定義する **[Filter anomaly options]\(異常のフィルター処理オプション\)** です。 詳細については、[アラートを構成する方法とフックを使用して通知を受け取る方法](../how-tos/alerts.md#add-or-edit-alert-settings)に関するページの **アラート設定を追加または編集する** 方法に関するセクションを参照してください。


## <a name="next-steps"></a>次のステップ

- [データ フィードをオンボードする](../how-tos/onboard-your-data.md)
    - [データ フィードを管理する](../how-tos/manage-data-feeds.md)
    - [さまざまなデータ ソースの構成](../data-feeds-from-different-sources.md)
- [REST API またはクライアント ライブラリを使用する](./rest-api-and-client-library.md)
- [メトリックを構成して検出構成を微調整する](../how-tos/configure-metrics.md)

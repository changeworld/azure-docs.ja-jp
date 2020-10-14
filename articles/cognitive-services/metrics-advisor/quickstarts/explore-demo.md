---
title: Metrics Advisor のデモを体験する
titleSuffix: Azure Cognitive Services
description: 用意したデモを使用して Metrics Advisor の Web インターフェイスについて説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 39748bacf784af0b3f162f960cd7d87cbd03a2eb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047592"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>クイック スタート:サンプル データを利用して Metrics Advisor のデモを体験する

> [!Note]
> Metrics Advisor デモは読み取り専用であり、ご自分のデータをオンボードすることはできません。 ご自分のデータでサービスを使用する場合は、最初に [Metrics Advisor リソース](web-portal.md)を作成してください。

この記事は、Metrics Advisor の主な機能を手軽に体験する目的でご利用ください。 フル機能の Web ポータルを体験していただけるよう、サンプル データとプリセット構成を使用したデモ サイトを用意しています。

デモ サイトにアクセスするには、[こちらのリンク](https://aka.ms/MetricsAdvisor/Demo)をクリックしてください。

## <a name="view-the-available-sample-data"></a>用意されているサンプル データを表示する

デモ サイトにサインインすると、 **[データ フィード]** ページが表示されます。 データ フィードは、データ ソースから照会される時系列データの論理上のグループです。 Metrics Advisor で使用される用語と概念の詳細については、[用語集](../glossary.md)を参照してください。 

いくつかのデータ フィードが一覧表示されています。これらは、Azure SQL データベースや Azure テーブルなど、各種のデータ ソースから取り込まれたものです。 それぞれ、関連するデータ ストアへの接続に使用されている構成設定がわずかに異なります。

:::image type="content" source="../media/sample-datafeeds.png" alt-text="サンプル データ一覧" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>データ フィードの構成を調べる

*Sample - Cost/Revenue - City/Category* データ フィードをクリックします。 このフィードについて、いくつかのセクションに詳しい情報が表示されます。

* データ フィード名とインジェスト状態。
* データ ソースから照会されたメトリックの一覧。 たとえば、"*コスト*" や "*収益*" があります。 
* データ フィードがいつ利用できなくなったかを示すアラート履歴。 
* データ フィードがいつ更新されたかを示すログ。   
* データ フィードの情報と設定。

:::image type="content" source="../media/data-feed-view.png" alt-text="サンプル データ一覧" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>時系列の視覚エフェクトと構成を表示する

*Sample - Cost/Revenue - City/Category* データ フィードの *cost* メトリックをクリックします。 ディメンションでスライスされた関連する時系列が、履歴のメトリック データに従い、視覚エフェクトと共に表示されます。 メトリック データの周りに表示される青色のバンドは、Metrics Advisor の機械学習モデルから予想される値の範囲を表します。 このバンドの範囲外にあるポイントは、赤色のドットとしてマークされます。これらは検出された異常です。 

:::image type="content" source="../media/series-visualization.png" alt-text="サンプル データ一覧" lightbox="../media/series-visualization.png":::

異常検出は、メトリック詳細ページの左側にある**検出構成**を調整することで構成できます。 複数の異常検出手法が用意されており、それらを組み合わせることができます。 加えて、さまざまな感度や検出方向などの構成を試すこともできます。 **検出構成**の下部にある **[Advanced configuration]\(詳細構成\)** リンクから、より複雑な、カスタマイズされた検出設定を作成可能です。それらは、グループや個々の系列で使用できます。 

また、検出アルゴリズムにフィードバックを与えることで異常検出を調整することもできます。 いずれかの異常をクリックし、 **[Add feedback]\(フィードバックの追加\)** パネルを使用して、その異常状態、季節性、変化点の状態を構成します。 このフィードバックは、今後発生するポイントの検出に組み込まれる予定です。  

**[Add feedback]\(フィードバックの追加\)** パネルの一番下には、 **[To incident hub]\(インシデント ハブに移動\)** リンクがあります。このリンクからインシデント分析ページに誘導され、インシデントの根本原因が分析されます。  

:::image type="content" source="../media/incident-link.png" alt-text="サンプル データ一覧" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>異常検出の結果を考察して根本原因分析を実行する

いずれかの異常から **[To incident hub]\(インシデント ハブに移動\)** リンクをクリックすると、インシデント分析ページが表示されます。このページには、重大度、関連する異常の数、開始時刻、終了時刻など、インシデントに関する診断の分析情報が表示されます。 **[根本原因]** セクションには、偏差、分散、上位の異常 (インシデントの根本原因である可能性があります) への寄与度を考慮しながらインシデント ツリーを分析することによって自動化されたアドバイスが表示されます。

**[診断]** セクションには、インシデントのツリーが、インシデントを診断するためのいくつかのタブと共に表示されます。

:::image type="content" source="../media/incident-diagnostic.png" alt-text="サンプル データ一覧" lightbox="../media/incident-diagnostic.png":::

インシデントの根本原因を特定することで、状況が悪化する前に対策を講じて問題を軽減できます。 用意されている他の診断機能をクリックして、その他の分析情報を考察することもできます。 

## <a name="next-steps"></a>次のステップ

- [Web ポータルを使用する](web-portal.md)
- [REST API の使用](rest-api.md)
- [データ フィードをオンボードする](../how-tos/onboard-your-data.md)
    - [データ フィードを管理する](../how-tos/manage-data-feeds.md)
    - [さまざまなデータ ソースの構成](../data-feeds-from-different-sources.md)

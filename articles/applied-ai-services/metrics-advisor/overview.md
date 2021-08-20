---
title: Azure Metrics Advisor サービスとは
titleSuffix: Azure Applied AI Services
description: Metrics Advisor とは
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: 295c166ec80eba638798dd452e27de945e41e9a6
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342524"
---
# <a name="what-is-azure-metrics-advisor"></a>Azure Metrics Advisor とは 

Metrics Advisor は、AI を使用してデータの監視と時系列データにおける異常検出を実行するサービスで、[Azure Applied AI Services](../../applied-ai-services/what-are-applied-ai-services.md) に含まれています。 このサービスは、データにモデルを適用するプロセスを自動化すると共に、機械学習の知識を得なくてもデータ インジェスト、異常検出、診断を行うことができる一連の API と Web ベースのワークスペースを備えています。 開発者は、AIOps、予測メンテナンス、およびビジネス監視アプリケーションをサービス上に構築できます。 Metrics Advisor を使用すると、次のことができます。

* 複数のデータ ソースからの多次元データを分析する
* 異常を識別して関連付ける
* 実際のデータで使用される異常検出モデルを構成して微調整する
* 異常を診断し、根本原因分析に役立てる

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Metrics Advisor の概要":::

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./Quickstarts/web-portal.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* [攻略ガイド](./how-tos/onboard-your-data.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [概念の記事](glossary.md)では、サービスの機能と特長について詳しく説明します。

## <a name="connect-to-a-variety-of-data-sources"></a>さまざまなデータ ソースに接続する

Metrics Advisor は、SQL Server、Azure Blob Storage、MongoDB など、さまざまなデータ ストアに接続して[多次元メトリック データを取り込む](how-tos/onboard-your-data.md)ことができます。

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>使いやすくカスタマイズ可能な異常検出

* Metrics Advisor では、データに最適なモデルが自動的に選択されるので、機械学習に関する知識は一切必要ありません。
* [多次元メトリック](glossary.md#multi-dimensional-metric)内のすべての時系列が自動的に監視されます。
* データに適用されるモデルと以降の異常検出の結果をカスタマイズするには、[パラメーターの調整](how-tos/configure-metrics.md)と[インタラクティブ フィードバック](how-tos/anomaly-feedback.md)を使用します。

## <a name="real-time-notification-through-multiple-channels"></a>複数のチャネルでのリアルタイム通知

Metrics Advisor では異常を検出するたびに、電子メール フック、Web hook、Teams フックや Azure DevOps フックなどのフックを使用し、複数のチャネルから[リアル タイム通知を送信](how-tos/alerts.md)できます。 通知を送信するタイミングや場所をカスタマイズし、アラートを柔軟に構成できます。

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>異常分析によるスマート診断分析情報

### <a name="analyze-root-cause-into-specific-dimension"></a>根本原因を特定のディメンションで分析する 

Metrics Advisor では、同じ多次元のメトリックで検出された異常を診断ツリーに結びつけ、根本原因を特定のディメンションに分析できるようにします。 また、各ディメンションの最大の貢献度を分析することで、自動的に分析された分析情報もあります。 

### <a name="cross-metrics-analysis-using-metrics-graph"></a>メトリック グラフを使用したメトリック間分析

[メトリック グラフ](./how-tos/metrics-graph.md)は、メトリック間の関係を示します。 メトリック間分析を有効にすると、関連するすべてのメトリック間の異常な状態を包括的に把握できます。 そして最終的に、決定的な根本原因を特定できるようになります。

詳細については、[インシデントの診断方法](./how-tos/diagnose-an-incident.md)に関するページを参照してください。

## <a name="typical-workflow"></a>一般的なワークフロー

ワークフローはシンプルです。データをオンボードした後、異常検出を微調整し、実際のシナリオに合った構成を作成します。

1. Metrics Advisor 用の [Azure リソースを作成](https://go.microsoft.com/fwlink/?linkid=2142156)します。 
2. Web ポータルを使用して初めてのモニターを作成します。
    1. [データをオンボードする](./how-tos/onboard-your-data.md)
    2. [異常検出構成を微調整する](./how-tos/configure-metrics.md)
    3. [通知のために異常をサブスクライブする](./how-tos/alerts.md)
    4. [診断の分析情報を表示する](./how-tos/diagnose-an-incident.md)
3. インスタンスをカスタマイズするには、REST API を使用します。

## <a name="video"></a>ビデオ
* [Metrics Advisor の紹介](https://www.youtube.com/watch?v=0Y26cJqZMIM)
* [Cognitive Services を初めて使用する](https://www.youtube.com/watch?v=7tCLJHdBZgM)

## <a name="data-retention--limitation"></a>データ保持と制限: 

Metrics Advisor により、現在のタイムスタンプから、データの有無にかかわらず、最大 **10,000** 期間保持されます ([期間とは](tutorials/write-a-valid-query.md#what-is-an-interval))。 この期間外のデータは削除されます。  メトリックの細分性が異なる場合のマッピングされた日数によるデータ保有期間: 

| 細分性 (分) |    リテンション期間 (日数) |
|------------------| ------------------|
|  1 | 6.94 |
|  5 | 34.72|
| 15 | 104.1|
| 60 (= 毎時) | 416.67 |
| 1440 (= 毎日)|10000.00|

これ以外にも制限があります。詳細については、[よくあるご質問](faq.yml#what-are-the-data-retention-and-limitations-of-metrics-advisor-)に関する記事を参照してください。 

## <a name="next-steps"></a>次のステップ

* クイックスタート「[Web で初めてのメトリックを監視する](quickstarts/web-portal.md)」を参照します。
* クイックスタート「[REST API を使用してソリューションをカスタマイズする](./quickstarts/rest-api-and-client-library.md)」を参照します。
---
title: Metrics Advisor サービスとは
titleSuffix: Azure Cognitive Services
description: Metrics Advisor とは
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: dfdd7286013bbb6462fb8e5b1bdf52e6ed738029
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384682"
---
# <a name="what-is-metrics-advisor-preview"></a>Metrics Advisor (プレビュー) とは 

Metrics Advisor は、AI を使用してデータの監視と時系列データにおける異常検出を実行するサービスで、Azure Cognitive Services に含まれています。 このサービスは、データにモデルを適用するプロセスを自動化すると共に、機械学習の知識を得なくてもデータ インジェスト、異常検出、診断を行うことができる一連の API と Web ベースのワークスペースを備えています。 開発者は、AIOps、予測メンテナンス、およびビジネス監視アプリケーションをサービス上に構築できます。 Metrics Advisor を使用すると、次のことができます。

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

## <a name="real-time-alerts-through-multiple-channels"></a>複数のチャネルを介したリアルタイム アラート

Metrics Advisor は異常が検出されるたびに、電子メール、Web、Azure DevOps などのフックを使用し、複数のチャネルを通じて[リアルタイム アラートを送信](how-tos/alerts.md)することができます。 柔軟なアラート ルールにより、どのアラートをどこに送信するかをカスタマイズできます。

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>異常分析によるスマート診断分析情報

多次元メトリックで検出された異常を分析し、可能性が特に高い根本原因や診断ツリー、メトリック詳細表示などを含んだ[スマート診断分析情報](how-tos/diagnose-incident.md)を生成します。 [メトリック グラフ](how-tos/metrics-graph.md)を構成してクロス メトリック分析を有効にすれば、インシデントを視覚化しやすくなります。


## <a name="typical-workflow"></a>一般的なワークフロー

ワークフローはシンプルです。データをオンボードした後、異常検出を微調整し、実際のシナリオに合った構成を作成します。

1. Metrics Advisor 用の [Azure リソースを作成](https://go.microsoft.com/fwlink/?linkid=2142156)します。 
2. Web ポータルを使用して初めてのモニターを作成します。
    1. データをオンボードする
    2. 異常検出を微調整する
    3. アラートをサブスクライブする
    4. 診断の分析情報を表示する
3. インスタンスをカスタマイズするには、REST API を使用します。

## <a name="next-steps"></a>次のステップ

* クイックスタート「[Web で初めてのメトリックを監視する](quickstarts/web-portal.md)」を参照します。
* クイックスタート「[REST API を使用してソリューションをカスタマイズする](./quickstarts/rest-api-and-client-library.md)」を参照します。

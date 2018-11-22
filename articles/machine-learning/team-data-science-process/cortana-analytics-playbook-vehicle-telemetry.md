---
title: 車両の状態と運転習慣の予測 - Azure | Microsoft Docs
description: Cortana Intelligence の機能を使用して、車両の状態と運転の習慣に関するリアルタイムの予測的な洞察を得ます。
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: 0594b9b40fdf8647f99946a31dbb47a95c9694ac
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300355"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>車両テレメトリ分析ソリューション プレイブック

かつて研究所にあったスーパーコンピューターは、今では車庫の中にあります。 スーパーコンピューターは、無数のセンサーを含む最先端の自動車の中に設置されつつあります。 このセンサーにより、自動車は毎秒数百万のイベントを追跡して監視できるようになります。 2020 年までに、これらの車両のほとんどがインターネットに接続されるでしょう。 豊富なデータを利用することで、安全性と信頼性が向上し、より良い運転体験が実現されます。 Microsoft は Cortana Intelligence により、この夢を現実のものにします。

Cortana Intelligence は、データをインテリジェントなアクションに変換できる、フル マネージドのビッグ データおよび高度な分析スイートです。 Cortana Intelligence 車両テレメトリ分析ソリューション テンプレートでは、車の販売代理店、自動車メーカー、保険会社が、車両の状態や運転の習慣に関するリアルタイムの予測分析をどのように実現できるかについて説明します。

ソリューションを[ラムダ アーキテクチャ パターン](https://en.wikipedia.org/wiki/Lambda_architecture) として実装することで、リアルタイムのバッチ処理において Cortana Intelligence プラットフォームが持つすべての可能性を示します。

## <a name="architecture"></a>アーキテクチャ
車両テレメトリ分析ソリューションのアーキテクチャを、次の図に示します。

![ソリューションのアーキテクチャ図](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


このソリューションには、次の Cortana Intelligence コンポーネントが含まれており、またそれらの統合について紹介しています。

* **Azure Event Hubs** では、数百万の車両テレメトリ イベントが Azure に取り込まれます。
* **Azure Stream Analytics** では、車両の状態に関するリアルタイムの分析情報を得て、より高度な一括分析のために、そのデータを長期保管します。
* **Azure Machine Learning** では、リアルタイムで異常を検出し、バッチ処理を使用して予測的な情報を提供します。
* **Azure HDInsight** は、大規模なデータ変換を行います。
* **Azure Data Factory** では、オーケストレーション、スケジュール設定、リソース管理、バッチ処理のパイプラインの監視が処理されます。
* **Power BI** このソリューションに機能豊富なダッシュボードを提供し、リアルタイムなデータおよび予測分析を視覚化します。

このソリューションでは、2 つの異なるデータ ソースにアクセスします。 

* **シミュレートされた車両の信号と診断**: 車両テレマティックス シミュレーターは診断情報、車両の状態に対応する信号、特定の時間における運転のパターンを出力します。 
* **車両カタログ**: この参照データ セットでは、車両識別番号 (VIN) をモデルにマップします。

## <a name="next-steps"></a>次の手順

このソリューションについて詳しくは、「[車両テレメトリ分析ソリューション プレイブック: ソリューションの詳細](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md)」をご覧ください。

このソリューション用に Power BI レポートとダッシュボードを構成する方法については、「[車両テレメトリ分析ソリューション テンプレートの Power BI ダッシュボードのセットアップ手順](cortana-analytics-playbook-vehicle-telemetry-powerbi.md)」をご覧ください。

---
title: Azure FarmBeats の概要
description: Azure FarmBeats の概要について説明します
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797158"
---
# <a name="overview-of-azure-farmbeats"></a>Azure FarmBeats の概要

Azure FarmBeats は、農業におけるインテリジェントなデータ駆動型ソリューションの迅速な構築を支援するように設計された Azure のサービスと機能のコレクションです。 Azure FarmBeats は、専門的な Data Engineering リソースに投資することなく、センサー、ドローン、カメラ、衛星などのさまざまなソースから農業関連のデータを取得、集計、処理できるようにする Azure Marketplace オファリングです。

> [!NOTE]
> 現在、Azure FarmBeats はパブリック プレビュー段階にあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 Azure FarmBeats は、サービス レベル アグリーメントなしに提供されます。 サポートについては、[Azure FarmBeats フォーラム](https://aka.ms/FarmBeatsMSDN )をご利用ください。

Azure FarmBeats を使用すると、センサー、衛星、ドローンなどのさまざまなソースから、すべてファームのコンテキスト (関心のある地理的な領域) でデータを取得できるようになります。

以下のことができます。

- さまざまなプロバイダーからの農業データセットを集約する
- さまざまなデータセットを合成して、人工知能/機械言語 (AI/ML) モデルを迅速に構築する

Azure FarmBeats には、以下を行う堅牢で簡単な方法が用意されています。

- GeoJSON ファイルを使用して、ファームのマップを作成します。
- 衛星画像に基づく植生指数と水指数を使用して、ファームの正常性を評価します。
- 使用するセンサーの数や配置場所についての推奨情報を取得します。
- さまざまなセンサー ベンダーのセンサーによって収集された地上データを視覚化して、ファームの状態を追跡します。
- 衛星およびセンサー データの合成に基づいて、土壌水分マップを取得します。
- 集約されたデータセットの上に AI/ML モデルを構築して、アクションにつながる分析情報を得ます。
- ファームの正常性の勧告を提供して、デジタル農業ソリューションを構築または強化します。

この記事の以下のセクションでは、Azure FarmBeat のコンポーネントについて説明します。

## <a name="data-hub"></a>データ ハブ

プロバイダー間でのさまざまな農業データセットの集計、正規化、コンテキスト化を可能にする API レイヤー。 このプレビューの段階では、2 つのセンサー プロバイダー ([Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/)、[Teralytic](https://teralytic.com/))、1 つの衛星画像プロバイダー ([Sentinel-2](https://sentinel.esa.int/web/sentinel/home))、2 つのドローン画像プロバイダー ([senseFly](https://www.sensefly.com/)、[SlantRange](https://slantrange.com/)) を利用できます。 データ ハブは API プラットフォームとして設計されており、Azure FarmBeats と統合するために多くのプロバイダーと連携しています。そのため、ソリューションを構築するときに、より多くの選択肢があります。

## <a name="accelerator"></a>アクセラレータ

データハブの上に構築されるサンプル ソリューションで、ユーザー インターフェイスとモデル開発を即座に開始します。 この Web アプリケーションでは、API を活用して、取り込まれたセンサー データをグラフとして視覚化し、モデル出力をマップとして視覚化する方法を示します。 たとえば、アクセラレータを使用すると、ファームをすばやく作成し、そのファームの植生指数マップやセンサー配置マップを簡単に取得できます。

## <a name="resources"></a>リソース

FarmBeats の[ブログ](https://aka.ms/AzureFarmBeats)と[フォーラム](https://aka.ms/FarmBeatsMSDN)をご覧ください。

## <a name="next-steps"></a>次の手順

Azure FarmBeats の使用を開始するには、[Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) にアクセスしてデプロイします。

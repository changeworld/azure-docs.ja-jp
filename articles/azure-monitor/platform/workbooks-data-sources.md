---
title: Azure Monitor ブックのデータソース | Microsoft docs
description: 複数のデータ ソースから作成された作成済みおよびパラメーター化されたカスタム Azure Monitor ブックを使用して、複雑なレポート作成を簡素化します
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658373"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor ブックのデータ ソース

ブックは、多数のデータ ソースと互換性があります。 この記事では、Azure Monitor ブックで現在使用できるデータ ソースについて説明します。

## <a name="logs"></a>ログ

ブックでは、次のソースのログを照会できます。

* Azure Monitor ログ (Application Insights リソースと Log Analytics ワークスペース)。
* リソース中心のデータ (アクティビティ ログ)

ブックの作成者は、基になるリソース データを変換する KQL クエリを使用して、テキスト、グラフ、またはグリッドとして視覚化できる結果セットを選択できます。

![ブックのログ レポート インターフェイスのスクリーンショット](./media/workbooks-overview/logs.png)

ブックの作成者は、複数のリソースを簡単に照会でき、完全に統合された優れたレポート エクスペリエンスを作成できます。

## <a name="metrics"></a>メトリック

Azure リソースは、ブックを介してアクセスできる[メトリック](data-platform-metrics.md)を生成します。 ターゲット リソース、目的のメトリック、およびそれらの集計を指定できる特別なコントロールを通じて、ブック内でメトリックにアクセスできます。 このデータは、グラフまたはグリッドにプロットできます。

![CPU 使用率のブック メトリック グラフのスクリーンショット](./media/workbooks-overview/metrics-graph.png)

![ブック メトリック インターフェイスのスクリーンショット](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

ブックでは、Azure Resource Graph (ARG) を使用したリソースとそのメタデータのクエリをサポートしています。 この機能は主に、レポートのカスタム クエリ スコープを構築するために使用されます。 リソース スコープは、ARG がサポートする KQL サブセットを介して表現されます。多くの場合、一般的なユース ケースではこれで十分です。

クエリ コントロールでこのデータ ソースを使用するには、[クエリの種類] ドロップダウンを使用して [Azure Resource Graph] を選択し、ターゲットとなるサブスクリプションを選択します。 クエリ コントロールを使用して、興味深いリソース サブセットを選択する ARG KQL サブセットを追加します。


![Azure Resource Graph KQL クエリのスクリーンショット](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>アラート (プレビュー)

ブックでは、リソースに関連するアクティブなアラートをユーザーが視覚化できます。 この機能により、通知データ (アラート) と診断情報 (メトリック、ログ) を 1 つにまとめたレポートを作成できます。 さらに、この情報を結合して、これらのデータ ソース全体の分析情報を組み合わせた高度なレポートを作成することもできます。

クエリ コントロールでこのデータ ソースを使用するには、[クエリの種類] ドロップダウンを使用して [アラート] を選択し、ターゲットとなるサブスクリプション、リソース グループ、またはリソースを選択します。 アラート フィルターのドロップダウンを使用して、ご自分の分析ニーズに合わせて興味深いアラートのサブセットを選択します。

![アラート クエリのスクリーンショット](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>ワークロードの正常性 (プレビュー)

Azure Monitor には、Windows または Linux ゲスト オペレーティング システムの可用性とパフォーマンスを予防的に監視する機能があります。 Azure Monitor では、主要なコンポーネントとそのリレーションシップ、それらのコンポーネントの正常性を測定する方法の基準、および異常な状態が検出されたときにアラートを通知するコンポーネントがモデル化されます。 ブックでは、ユーザーがこの情報を使用して高度な対話型レポートを作成できます。

クエリ コントロールでこのデータ ソースを使用するには、 **[クエリの種類]** ドロップダウンを使用して [ワークロードの正常性] を選択し、ターゲットとなるサブスクリプション、リソース グループ、または VM リソースを選択します。 正常性フィルターのドロップダウンを使用して、ご自分の分析ニーズに合わせて興味深い正常性インシデントのサブセットを選択します。

![アラート クエリのスクリーンショット](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure リソースの正常性 

ブックでは、Azure リソースの正常性を取得し、それを他のデータ ソースと組み合わせて、優れた対話型の正常性レポートを作成できます

クエリ コントロールでこのデータ ソースを使用するには、 **[クエリの種類]** ドロップダウンを使用して [Azure の正常性] を選択し、ターゲットとなるリソースを選択します。 正常性フィルターのドロップダウンを使用して、ご自分の分析ニーズに合わせて興味深いリソースの問題のサブセットを選択します。

![アラート クエリのスクリーンショット](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (プレビュー)

強力な [Kusto](https://docs.microsoft.com/azure/kusto/query/index) クエリ言語を使用できる [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) クラスターからのクエリがブックでサポートされるようになりました。   

![Kusto クエリ ウィンドウのスクリーンショット](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](workbooks-visualizations.md)します。
* ブック リソースへのアクセスを[制御](workbooks-access-control.md)し、共有します。

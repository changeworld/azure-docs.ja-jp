---
title: Azure Monitor のログ | Microsoft Docs
description: 監視データの高度な分析に使用される Azure Monitor のログについて説明します。
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032778"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor ログの概要
Azure Monitor ログは、さまざまなソースからログ データを収集して整理し、高度なクエリ言語を使用して分析できるようにする Azure Monitor の機能です。 さまざまなソースからのデータを 1 つのワークスペースに統合し、まとめて分析して、このようなタスクや傾向分析、アラート、視覚化を実行できます。

## <a name="relationship-to-azure-monitor-metrics"></a>Azure Monitor メトリックとのリレーションシップ
Azure Monitor メトリックでは、数値データが時系列データベースに格納されます。これにより、このデータは Azure Monitor ログより軽量になり、ほぼリアルタイムのシナリオをサポートできるため、アラートと問題の迅速な検出に特に役立ちます。 ただし、メトリックが特定の構造に数値データを格納することしかできないのに対し、ログは、それぞれ独自の構造を持つさまざまなデータ型を格納できます。 メトリック データの分析に使用できないログ クエリを使用して、ログ データで複雑な分析を実行することもできます。

多くの場合、数値データは、データ ソースから、メトリックに加えて、ログにも送信されます。 このデータをログに収集して保持するには追加料金がかかりますが、メトリック データをログ クエリに含め、他の監視データを使用して分析することができます。

## <a name="relationship-to-azure-data-explorer"></a>Azure Data Explorer とのリレーションシップ
Azure Monitor ログは、Azure Data Explorer に基づいています。 Log Analytics ワークスペースは Azure Data Explorer のデータベースとほぼ同じで、テーブルは同じように構造化され、どちらも同じ Kusto クエリ言語 (KQL) が使用されています。 Log Analytics を使用して Azure portal で Azure Monitor クエリを操作するエクスペリエンスは、Azure Data Explorer Web UI を使用したエクスペリエンスと似ています。 [Log Analytics ワークスペースのデータを Azure Data Explorer クエリに含める](/azure/data-explorer/query-monitor-data)こともできます。 


## <a name="structure-of-data"></a>データの構造
Azure Monitor ログによって収集されたデータは、それぞれが特定のソースのデータを格納する複数のテーブルを含む [Log Analytics ワークスペース](./design-logs-deployment.md)に格納されます。 ワークスペースでは、データの地理的な場所、データにアクセスできるユーザーを定義するアクセス権、価格レベルやデータ保有期間などの構成設定が定義されます。 1 つのワークスペースをすべての監視データに使用することも、要件に応じて複数のワークスペースを作成することもできます。 複数のワークスペースを作成する際の考慮事項については、「[Azure Monitor ログのデプロイの設計](design-logs-deployment.md)」を参照してください。

各ワークスペースには、複数のデータ行を含む別々の列に編成された複数のテーブルが含まれています。 各テーブルは、データ ソースによって提供されるデータ行によって共有される一意の列セットによって定義されます。 

[![Azure Monitor ログの構造](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights のログ データは、Azure Monitor ログにも格納されますが、アプリケーションの構成方法によってその格納方法は異なります。 ワークスペースベースのアプリケーションの場合、アプリケーション要求、例外、ページ ビューなどのデータを保持するために、データは標準のテーブ ルセットの Log Analytics ワークスペースに格納されます。 複数のアプリケーションで同じワークスペースを使用できます。 従来のアプリケーションでは、データは Log Analytics ワークスペースには格納されません。 同じクエリ言語が使用されているため、Azure portal の同じ Log Analytics ツールを使用してクエリを作成して実行します。 ただし、従来のアプリケーションのデータは、互いに独立して格納されます。 テーブル名と列名は異なっていますが、その一般的な構造は、ワークスペースベースのアプリケーションと同じです。 2 つの詳細な比較については、「[ワークスペースベース リソースの変更](../app/apm-tables.md)」を参照してください。


> [!NOTE]
> Application Insights のエクスペリエンスにおける Application Insights クラシック リソース クエリ、ブック、およびログベースのアラートについては、引き続き完全な下位互換性を提供します。 [新しいワークスペース ベースのテーブル構造またはスキーマ](../app/apm-tables.md)に対してクエリ/ビューを実行するには、まず Log Analytics ワークスペースに移動する必要があります。 プレビュー中に [Application Insights] ウィンドウの **[ログ]** を選択すると、従来の Application Insights クエリ エクスペリエンスにアクセスできます。 詳しくは、「[クエリ スコープ](../log-query/scope.md)」を参照してください。


[![Application Insights の Azure Monitor ログの構造](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>ログ クエリ
データは[ログ クエリ](../log-query/log-query-overview.md)を使用して Log Analytics ワークスペースから取得されます。これは、データを処理して結果を返す読み取り専用の要求です。 ログ クエリは、Azure Data Explorer で使用されるクエリ言語である [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/) で記述されます。 Azure portal のツールである Log Analytics を使用して、ログ クエリを編集して実行し、その結果を対話形式で分析します。 その後、作成したクエリを使用して、ログ クエリ アラートやブックなどの Azure Monitor の他の機能をサポートできます。


## <a name="sources-of-data-for-azure-monitor-logs"></a>Azure Monitor ログのデータのソース
Azure Monitor により、Azure Monitor のリソースや、仮想マシンで実行されているエージェントなど、さまざまなソースからログ データが収集されます。 Log Analytics ワークスペースにデータを送信するデータ ソースの完全なリストについては、「[Azure Monitor によって監視される内容](../monitor-reference.md)」を参照してください。



## <a name="next-steps"></a>次の手順

- Log Analytics ワークスペースからデータを取得して分析する[ログ クエリ](../log-query/log-query-overview.md)について説明します。
- [Azure Monitor でのメトリック](data-platform-metrics.md)を確認します。
- Azure のさまざまなリソースで[入手できる監視データ](data-sources.md)を確認します。


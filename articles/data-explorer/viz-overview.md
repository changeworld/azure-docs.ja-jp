---
title: Azure Data Explorer でのデータの視覚化
description: Azure Data Explorer のデータを視覚化するさまざまな方法について説明します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482053"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Azure Data Explorer でのデータの視覚化 

Azure Data Explorer は、膨大な量のデータの複雑な分析ソリューションを構築するために使用される、ログおよびテレメトリ データ用の高速でスケーラブルなデータ探索サービスです。 Azure Data Explorer にはさまざまな視覚化ツールが統合されているため、データを視覚化して、組織全体で結果を共有することができます。 このデータを、ビジネスに影響を与える実用的な分析情報に変換できます。

データの視覚化とレポートは、データ分析プロセスの重要な手順です。 Azure Data Explorer では多数の BI サービスがサポートされているため、シナリオや予算に最適なものを使用することができます。

* Azure Data Explorer での視覚化:[`render operator`](/azure/kusto/query/renderoperator) では、Kusto クエリ言語を使用して、クエリの結果を表すさまざまな視覚化の種類を提供しています。 クエリの視覚化は、異常検出、予測、機械学習などで役立ちます。

* [Power BI](https://powerbi.microsoft.com): Azure Data Explorer には、さまざまな方法を使用して Power BI に接続する機能があります。 

  * [組み込みのネイティブ Power BI コネクタ](/azure/data-explorer/power-bi-connector)

  * [Azure Data Explorer から Power BI へのクエリのインポート](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL クエリ](/azure/data-explorer/power-bi-sql-query)

* [Microsoft Excel](https://products.office.com/excel):Azure Data Explorer には、組み込みのネイティブ Excel コネクタを使用して Excel に接続したり、Azure Data Explorer から Excel にクエリをインポートしたりする機能があります。

* [Grafana](https://grafana.com):Grafana には、Azure Data Explorer のデータを視覚化できる Azure Data Explorer プラグインがあります。 [Grafana のデータ ソースとして Azure Data Explorer を設定し、データを視覚化します](/azure/data-explorer/grafana)。

* [Sisense](https://www.sisense.com):Azure Data Explorer には、JDBC コネクタを使用して Sisense に接続する機能があります。 [Sisense のデータ ソースとして Azure Data Explorer を設定し、データを視覚化します](/azure/data-explorer/sisense)。

* [Tableau](https://www.tableau.com):Azure Data Explorer には、Tableau に [ODBC コネクタを使用して接続し、Tableau でデータを視覚化する](/azure/data-explorer/connect-odbc)機能があります。

* [Qlik](https://www.qlik.com):Azure Data Explorer には、[ODBC コネクタ](/azure/data-explorer/connect-odbc)を使用して Qlik に 接続する機能があります。
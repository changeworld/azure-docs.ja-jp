---
title: Azure Monitor Log Analytics 言語 | Microsoft Docs
description: Log Analytics でクエリを記述する方法を学習するためのリソースへの参照。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 32e64ce7772d562ea34a0d74afbd737be27d247d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968870"
---
# <a name="log-analytics-query-language"></a>Log Analytics クエリ言語
Log Analytics は、Azure Monitor にログの収集と分析を提供します。 これは Azure Data Explorer 上に構築され、同じクエリ言語のバージョンを使用します。 [Azure Data Explorer クエリ言語のドキュメント](/azure/kusto/query)には、言語に関するすべての詳細が記載されているため、Log Analytics クエリを記述するためのプライマリ リソースとなります。 このページでは、クエリの記述方法を学習するためのその他のリソース、言語の Log Analytics の実装との違いに関する情報へのリンクを提供します。

## <a name="getting-started"></a>使用の開始

- 「[Get started with Log Analytics in the Azure portal](get-started-portal.md)」(Azure portal で Log Analytics の使用を開始する) は、Azure portal でのクエリの作成との結果の使用に関するレッスンです。
-  「[Get started with queries in Log Analytics](get-started-queries.md)」(Log Analytics のクエリの概要) は Log Analytics データを使用してクエリを記述するためのレッスンです。

## <a name="concepts"></a>概念
- 「[Analyze Log Analytics data in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)」(Azure Monitor で Log Analytics データを分析する) では、ログ クエリの簡単な概要を提供し、Log Analytics データがどのように構成されているかについて説明しています。
- 「[Viewing and analyzing data in Log Analytics](../../azure-monitor/log-query/portals.md)」(Log Analytics におけるデータの表示と分析) では、Log Analytics クエリを作成して実行するポータルについて説明しています。

## <a name="reference"></a>リファレンス

- [クエリ言語リファレンス](/azure/kusto/query)は、データ エクスプローラーのクエリ言語の完全な言語リファレンスです。
- 「[Log Analytics query language differences](data-explorer-difference.md)」 (Log Analytics クエリ言語の違い) では、データ エクスプローラーのクエリ言語のバージョンによる違いについて説明しています。
- 「[Standard properties in Log Analytics records](../../azure-monitor/platform/log-standard-properties.md)」(Log Analytics レコードでの標準プロパティ) では、Log Analytics のすべてのデータにとって標準のプロパティについて説明しています。
- 「[Perform cross-resource log searches in Log Analytics](../../azure-monitor/log-query/cross-workspace-query.md)」(Log Analytics でクロス リソースのログ検索を実行する) では、複数の Log Analytics ワークスペースと Application Insights アプリケーションからデータを使用するクエリを記述する方法について説明しています。


## <a name="examples"></a>例

- 「[Log Analytics query examples](examples.md)」(Log Analytics クエリの例) では、Log Analytics データを使用するクエリの例を提供しています。



## <a name="lessons"></a>レッスン

- 「[Working with strings in Log Analytics queries](string-operations.md)」(Log Analytics クエリ内の文字列の操作) では、文字列データを操作する方法について説明しています。
- 「[Working with date time values in Log Analytics queries](datetime-operations.md)」(Log Analytics クエリでの日時値の操作) では、日時データを操作する方法について説明しています。 
- 「[Aggregations in Log Analytics queries](aggregations.md)」(Log Analytics クエリの集計) と「[Advanced aggregations in Log Analytics queries](advanced-aggregations.md)」(Log Analytics クエリの高度な集計) では、データを集計、要約する方法について説明しています。
- 「[Joins in Log Analytics queries](joins.md)」(Log Analytics クエリの結合) では、複数のテーブルからデータを結合する方法について説明しています。
- 「[Working with JSON and data Structures in Log Analytics queries](json-data-structures.md)」(Log Analytics クエリ内の JSON とデータ構造の操作) では、json データを解析する方法について説明しています。
- 「[Writing advanced queries in Log Analytics](advanced-query-writing.md)」(Log Analytics での詳細なクエリの記述) では、複雑なクエリを作成し、コードを再利用するための方法について説明しています。
- 「[Creating charts and diagrams from Log Analytics queries](charts.md)」(Log Analytics クエリからのグラフと図の作成) では、クエリからデータを視覚化する方法について説明しています。

## <a name="cheatsheets"></a>チートシート

-  「[SQL to Log Analytics query language cheat sheet](sql-cheatsheet.md)」(SQL と Log Analytics のクエリ言語チート シート) は、SQL に精通しているユーザーの役に立ちます。
-  [Splunk と Log Analytics のクエリ言語チート シート](sql-cheatsheet.md)に関するページは、Splunk に精通しているユーザーの役に立ちます。
 
## <a name="next-steps"></a>次の手順

- 完全な[データ エクスプローラーのクエリ言語のリファレンス ドキュメント](/azure/kusto/query/)にアクセスします。
---
title: Azure Monitor Log Analytics 言語リファレンス |Microsoft Docs
description: Log Analytics によって使用される Kusto 言語に関するリファレンス情報。 Log Analytics に固有の追加の要素と、Log Analytics クエリでサポートされていない要素が含まれています。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451070"
---
# <a name="log-analytics-query-language-reference"></a>Log Analytics クエリ言語リファレンス
[Log Analytics クエリ](../log-analytics-queries.md)では、[Azure データ エクスプローラー](/azure/data-explorer/)で使用されるものと同じクエリ言語とエンジンが使用されます。 言語リファレンスと言語に関するその他の詳細には、[Kusto 言語リファレンス](/azure/kusto/query)に関するページからアクセスできます



## <a name="kusto-elements-not-support-in-log-analytics"></a>Log Analytics でサポートされていない Kusto 要素
Log Analytics クエリは Kusto の実装を使用しますが、次のセクションで説明されているように、一部の Kusto 要素はサポートされていません。

### <a name="statements-not-supported-in-log-analytics"></a>Log Analytics でサポートされていないステートメント

* [エイリアス](/kusto/query/aliasstatement)
* [クエリ パラメーター](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Log Analytics でサポートされていない関数

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Log Analytics でサポートされていない演算子

* [クラスター間の結合](/azure/kusto/query/joincrosscluster)
* [externaldata 演算子](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Log Analytics でサポートされていないプラグイン

* [sql_request プラグイン](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Log Analytics の追加の演算子
特定の Log Analytics 機能をサポートするために、Log Analytics の外部では使用できない、次のような追加の Kusto 演算子が提供されています。 

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>次の手順

- [Log Analytics](../log-analytics-queries.md) のクエリの詳細を確認します。
- [Log Analytics クエリ](/log-analytics/query-language/get-started-queries.md)の記述についてのレッスンを見ていきます。
- 完全な [Kusto のリファレンス ドキュメント](/azure/kusto/query/)にアクセスします。
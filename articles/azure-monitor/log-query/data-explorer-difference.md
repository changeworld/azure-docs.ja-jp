---
title: Azure Monitor Log Analytics 言語リファレンス |Microsoft Docs
description: Log Analytics によって使用されるデータ エクスプローラーのクエリ言語に関するリファレンス情報。 Log Analytics に固有の追加の要素と、Log Analytics クエリでサポートされていない要素が含まれています。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 645750ec40f0aba2ef58c096a72125fad2947719
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186261"
---
# <a name="log-analytics-query-language-differences"></a>Log Analytics クエリ言語の違い

[Log Analytics](log-query-overview.md) は [Azure データ エクスプローラー](/azure/data-explorer)に基づいて構築され、[同じクエリ言語](/azure/kusto/query)を使用しますが、言語のバージョンにはいくつかの違いがあります。 この記事では、データ エクスプローラーで使用される言語のバージョンと、Log Analytics のクエリに使用されるバージョンの間で異なる要素を識別します。

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Log Analytics でサポートされていないデータ エクスプローラー要素
次のセクションでは、Log Analytics でサポートされていないデータ エクスプローラーのクエリ言語の要素について説明します。

### <a name="statements-not-supported-in-log-analytics"></a>Log Analytics でサポートされていないステートメント

* [エイリアス](/azure/kusto/query/aliasstatement)
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
次の演算子は特定の Log Analytics 機能をサポートし、Log Analytics の外部では使用できません。

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>次の手順

- [Log Analytics クエリを記述するためのさまざまなリソース](query-language.md)の参照を取得します。
- 完全な[データ エクスプローラーのクエリ言語のリファレンス ドキュメント](/azure/kusto/query/)にアクセスします。

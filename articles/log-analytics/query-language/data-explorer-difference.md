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
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e6d097749dae49cf6f1d710bcf01cf99dcd98a4c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243897"
---
# <a name="log-analytics-query-language-differences"></a>Log Analytics クエリ言語の違い

[Log Analytics](../log-analytics-queries.md) は [Azure データ エクスプローラー](/azure//data-explorer)に基づいて構築され、[同じクエリ言語](/azure/kusto/query)を使用しますが、言語のバージョンにはいくつかの違いがあります。 この記事では、データ エクスプローラーで使用される言語のバージョンと、Log Analytics のクエリに使用されるバージョンの間で異なる要素を識別します。

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Log Analytics でサポートされていないデータ エクスプローラー要素
次のセクションでは、Log Analytics でサポートされていないデータ エクスプローラーのクエリ言語の要素について説明します。

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
次の演算子は特定の Log Analytics 機能をサポートし、Log Analytics の外部では使用できません。

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>次の手順

<<<<<<< HEAD:articles/log-analytics/query-language/data-explorer-difference.md
- [Log Analytics クエリを記述するためのさまざまなリソース](kusto.md)の参照を取得します。
- 完全な[データ エクスプローラーのクエリ言語のリファレンス ドキュメント](/azure/kusto/query/)にアクセスします。
=======
- [Log Analytics](../log-analytics-queries.md) のクエリの詳細を確認します。
- [Log Analytics クエリ](/log-analytics/query-language/get-started-queries.md)の記述についてのレッスンを見ていきます。
- 完全な [Kusto のリファレンス ドキュメント](/azure/kusto/query/)にアクセスします。
>>>>>>> 4bccab5ecb17c887658a4d2ed1bab6b22bf29ffd:articles/log-analytics/query-language/kusto.md

---
title: Azure Monitor ログ クエリの便利な演算子 | Microsoft Docs
description: Azure Monitor ログ クエリのさまざまなシナリオで使用する一般的な関数。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397723"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリの便利な演算子

下の表に、Azure Monitor ログ クエリのさまざまなシナリオで使用する一般的な関数の一部を示します。

## <a name="useful-operators"></a>便利な演算子

カテゴリ                                |関連する分析関数
----------------------------------------|----------------------------------------
選択と列の別名            |`project`、`project-away`、`extend`
一時テーブルと定数          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
比較演算子と文字列演算子         |`startswith`、`!startswith`、`has`, `!has` <br> `contains`、`!contains`、`containscs` <br> `hasprefix`、`!hasprefix`、`hassuffix`、`!hassuffix`、`in`、`!in` <br> `matches regex` <br> `==`、`=~`、`!=`, `!~`
一般的な文字列関数                 |`strcat()`、`replace()`、`tolower()`、`toupper()`、`substring()`、`strlen()`
一般的な算術関数                   |`sqrt()`, `abs()` <br> `exp()`､`exp2()`、`exp10()`、`log()`、`log2()`、`log10()`、`pow()` <br> `gamma()`, `gammaln()`
テキストの解析                            |`extract()`、`extractjson()`、`parse`, `split()`
出力の制限                         |`take`、`limit`、`top`, `sample`
データ関数                          |`now()`, `ago()` <br> `datetime()`、`datepart()`、`timespan` <br> `startofday()`、`startofweek()`、`startofmonth()`, `startofyear()` <br> `endofday()`、`endofweek()`、`endofmonth()`, `endofyear()` <br> `dayofweek()`、`dayofmonth()`、`dayofyear()` <br> `getmonth()`、`getyear()`、`weekofyear()`, `monthofyear()`
グループ化と集計                |`summarize by` <br> `max()`、`min()`、`count()`、`dcount()`、`avg()`、`sum()` <br> `stddev()`、`countif()`、`dcountif()`、`argmax()`、`argmin()` <br> `percentiles()`, `percentile_array()`
結合と共用体                        |`join kind=leftouter`、`inner`、`rightouter`、`fullouter`、`leftanti` <br> `union`
並べ替え、順序                             |`sort`, `order` 
動的オブジェクト (JSON と配列)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
論理演算子                       |`and`、`or`、`iff(condition, value_t, value_f)` <br> `binary_and()`、`binary_or()`、`binary_not()`, `binary_xor()`
機械学習                        |`evaluate autocluster`、`basket`、`diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>次のステップ

- [Azure Monitor でのログ クエリの記述](get-started-queries.md)に関するレッスンをご覧ください。

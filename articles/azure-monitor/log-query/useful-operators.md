---
title: Azure Log Analytics クエリでの役に立つ演算子 | Microsoft Docs
description: Log Analytics クエリのさまざまなシナリオでよく使用される関数です。
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
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 060b1e469a31c335f062ccd332157d13e64f9318
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183984"
---
# <a name="useful-operators-in-log-analytics-queries"></a>Log Analytics クエリでの役に立つ演算子

以下の表では、Log Analytics クエリのさまざまなシナリオでよく使用される関数を示します。

## <a name="useful-operators"></a>便利な演算子

Category                                |関連する分析関数
----------------------------------------|----------------------------------------
選択と列の別名            |`project`、`project-away`、`extend`
一時テーブルと定数          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
比較演算子と文字列演算子         |`startswith`、`!startswith`、`has`, `!has` <br> `contains`、`!contains`、`containscs` <br> `hasprefix`、`!hasprefix`、`hassuffix`、`!hassuffix`、`in`、`!in` <br> `matches regex` <br> `==`、`=~`、`!=`、`!~`
一般的な文字列関数                 |`strcat()`、`replace()`、`tolower()`、`toupper()`、`substring()`、`strlen()`
一般的な算術関数                   |`sqrt()`、`abs()` <br> `exp()`、`exp2()`、`exp10()`、`log()`、`log2()`、`log10()`、`pow()` <br> `gamma()`、`gammaln()`
テキストの解析                            |`extract()`、`extractjson()`、`parse`、`split()`
出力の制限                         |`take`、`limit`、`top`, `sample`
データ関数                          |`now()`、`ago()` <br> `datetime()`、`datepart()`、`timespan` <br> `startofday()`、`startofweek()`、`startofmonth()`, `startofyear()` <br> `endofday()`、`endofweek()`、`endofmonth()`, `endofyear()` <br> `dayofweek()`、`dayofmonth()`、`dayofyear()` <br> `getmonth()`、`getyear()`、`weekofyear()`, `monthofyear()`
グループ化と集計                |`summarize by` <br> `max()`、`min()`、`count()`、`dcount()`、`avg()`、`sum()` <br> `stddev()`、`countif()`、`dcountif()`、`argmax()`、`argmin()` <br> `percentiles()`、`percentile_array()`
結合と共用体                        |`join kind=leftouter`、`inner`、`rightouter`、`fullouter`、`leftanti` <br> `union`
並べ替え、順序                             |`sort`、`order` 
動的オブジェクト (JSON と配列)         |`parsejson()` <br> `makeset()`、`makelist()` <br> `split()`、`arraylength()` <br> `zip()`、`pack()`
論理演算子                       |`and`、`or`、`iff(condition, value_t, value_f)` <br> `binary_and()`、`binary_or()`、`binary_not()`, `binary_xor()`
機械学習                        |`evaluate autocluster`、`basket`、`diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>次の手順

- [Log Analytics でのクエリの記述](get-started-queries.md)に関するレッスンをご覧ください。

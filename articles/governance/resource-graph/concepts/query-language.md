---
title: Azure Resource Graph クエリ言語の概要
description: Azure Resource Graph のクエリ言語のしくみについて説明します。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951952"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph クエリ言語の概要

Azure Resource Graph のクエリ言語では、さまざまな演算子と関数がサポートされています。 それぞれは、Kusto Query Language (KQL) と同じように動作します。 しかし、Resource Graph のクエリ言語は [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators) と似ていますが同じではないことを理解しておくことが重要です。

> [!NOTE]
> KQL ドキュメントへのリンクでは、認証が必要な場合があります。

Resource Graph で使用されているクエリ言語について学習する最善の方法は、最初に KQL のドキュメントを読み、言語の構造と、サポートされているさまざまな演算子と関数の動作を理解することです。

## <a name="supported-tabular-operators"></a>サポートされているテーブル演算子

Resource Graph でサポートされているテーブル演算子の一覧を次に示します。

- [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [extend](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [limit](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [order by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [project](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [project-away](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [sample](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [sample-distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [sort by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [take](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [top](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [top-nested](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [top-hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>サポートされている関数

Resource Graph でサポートされている関数の一覧を次に示します。

- [ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [count()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [tostring()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>次の手順

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を参照してください
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を参照してください
- [その他のリソース](explore-resources.md)
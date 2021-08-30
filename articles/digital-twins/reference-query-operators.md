---
title: Azure Digital Twins クエリ言語リファレンス - 演算子
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語演算子のリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 06945efc6be8700955b1a475d00d21951102c214
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492795"
---
# <a name="azure-digital-twins-query-language-reference-operators"></a>Azure Digital Twins クエリ言語リファレンス: 演算子

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)の **演算子** に関するリファレンス情報が含まれています。

## <a name="comparison-operators"></a>比較演算子

比較ファミリの次の演算子がサポートされています。

* `=`、`!=`: 式が等しいかどうかを比較するために使用します。
* `<`、`>`: 式の順序付き比較に使用します。
* `<=`、`>=`: 等しいかどうかを含む式の順序付き比較に使用します。

### <a name="example"></a>例

`=`. を使用する例を次に示します。 次のクエリでは、Temperature の値が 80 に等しいツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="EqualityExample":::

`<`. を使用する例を次に示します。 次のクエリでは、Temperature の値が 80 未満のツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ComparisonExample":::

`<=`. を使用する例を次に示します。 次のクエリでは、Temperature の値が 80 以下のツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrderedComparisonExample":::

## <a name="contains-operators"></a>contains 演算子

contains ファミリの次の演算子がサポートされています。

* `IN`: 指定された値が値のセットに含まれる場合、true に評価されます。
* `NIN`: 指定された値が値のセットに含まれない場合、true に評価されます。

### <a name="example"></a>例

`IN`. を使用する例を次に示します。 次のクエリでは、`owner` プロパティがリストの複数のオプションの 1 つであるツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="InExample":::

## <a name="logical-operators"></a>論理演算子

論理ファミリの次の演算子がサポートされています。
* `AND`: 2 つの式を接続するために使用し、両方とも true の場合に true に評価されます。
* `OR`: 2 つの式を接続するために使用し、それらのうち 1 つ以上の式が true の場合に、true に評価されます。
* `NOT`: 式を否定するために使用し、式の条件が満たされない場合、true に評価されます。

### <a name="example"></a>例

`AND`. を使用する例を次に示します。 次のクエリでは、80 未満の Temperature と 50 未満の Humidity の両方の条件を満たすツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="AndExample":::

`OR`. を使用する例を次に示します。 次のクエリでは、80 未満の Temperature と 50 未満の Humidity の 1 つ以上の条件を満たすツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrExample":::

`NOT`. を使用する例を次に示します。 次のクエリでは、80 未満の Temperature の条件を満たしていないツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NotExample":::

## <a name="limitations"></a>制限事項

次の制限は、演算子を使用したクエリに適用されます。
* [IN/NIN の制限](#limit-for-innin)

詳細については、この後のセクションをご覧ください。

### <a name="limit-for-innin"></a>IN/NIN の制限

`IN` または `NIN` セットに含めることができる値の数の制限は 100 個の値です。

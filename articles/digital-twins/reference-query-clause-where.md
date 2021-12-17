---
title: Azure Digital Twins クエリ言語リファレンス - WHERE 句
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語 WHERE 句のリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: bf70d1aa4b7a8a3cdfd9d8bcd90a9952e83271bc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232832"
---
# <a name="azure-digital-twins-query-language-reference-where-clause"></a>Azure Digital Twins クエリ言語リファレンス: WHERE 句

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)の **WHERE 句** に関するリファレンス情報が含まれています。

WHERE 句は、クエリの最後の部分です。 特定の条件に基づいて返される項目をフィルター処理するために使用します。

この句はクエリの実行時に省略可能です。

## <a name="core-syntax-where"></a>コア構文: WHERE

WHERE 句は、クエリ結果をフィルター処理するブール値条件と共に使用します。 

条件を[関数](reference-query-functions.md)にしてブール値結果を評価させることができます。 また、comparison または contains 型の[演算子](reference-query-operators.md)を使用したツインおよびリレーションシップ (`.` でアクセス) のプロパティを使用して、独自のブール ステートメントを作成することもできます。

### <a name="syntax"></a>構文

プロパティと演算子を使用する場合:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereSyntax":::

関数を使用する場合:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionSyntax":::

### <a name="arguments"></a>引数

`Boolean` 値に評価される条件。

### <a name="examples"></a>例

プロパティと演算子を使用した例を次に示します。 次のクエリでは、`$dtId` 値が Room1 のツインのみを返すよう WHERE 句で指定しています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereExample":::

関数を使用した例を次に示します。 次のクエリでは、`IS_OF_MODEL` 関数を使用して、モデルが `dtmi:sample:Room;1` のツインのみを返すよう WHERE 句で指定しています。 `IS_OF_MODEL` 関数の詳細については、「[Azure Digital Twins クエリ言語リファレンス: 関数](reference-query-functions.md#is_of_model)」を参照してください。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionExample":::
---
title: Azure Digital Twins クエリ言語リファレンス - 関数
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語関数のリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 80b32907499d3c8588a7a565972f49923abbe5c7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108296475"
---
# <a name="azure-digital-twins-query-language-reference-functions"></a>Azure Digital Twins クエリ言語リファレンス: 関数

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)の **関数** に関するリファレンス情報が含まれています。

## <a name="endswith"></a>ENDSWITH

指定された文字列が特定の他の文字列で終了しているかどうかを判断するために使用される文字列関数。 

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithSyntax":::

### <a name="arguments"></a>引数

* `<string-to-check>`: 末尾をチェックするための文字列
* `<ending-string>`: チェック対象の末尾を表している文字列

### <a name="returns"></a>戻り値

1 つ目の文字列式が 2 つ目の文字列式で終了しているかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、ID が `-small` で終了しているすべてのデジタル ツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithExample":::

## <a name="is_defined"></a>IS_DEFINED

プロパティが定義されているかどうかをチェックするための型チェックとキャスト関数。

これは、プロパティ値がプリミティブ型である場合にのみサポートされます。 プリミティブ型には、文字列、ブール値、数値、または `null` が含まれます。 `DateTime`、オブジェクト型、配列はサポートされていません。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedSyntax":::

### <a name="arguments"></a>引数

`<property>`、定義されているかどうかを判断するためのプロパティ。 プロパティは、プリミティブ型である必要があります。

### <a name="returns"></a>戻り値

プロパティに値が代入されているかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、*Location* プロパティが定義されているすべてのデジタルツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedExample":::

## <a name="is_of_model"></a>IS_OF_MODEL

ツインが特定のモデル型であるかどうかを判断するための型チェックとキャスト関数。 指定したモデルから継承するモデルが含まれます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelSyntax":::

### <a name="arguments"></a>引数

必須:
* `<model-ID>`: チェック対象のモデル ID。

省略可能:
* `<twin-collection>`: 複数のツイン コレクションがある場合 (`JOIN` パラメーターが使用されている場合など) に、検索するツイン コレクションを指定します。
* `exact`: 完全一致が必要です。 このパラメーターが設定されていない場合、結果セットには、指定したモデルから継承したモデルがあるツインが含まれます。

### <a name="returns"></a>戻り値

指定したツインが指定したモデルの種類と一致するかどうかを示すブール値。

### <a name="example"></a>例

次のクエリは、モデルの種類が正確に `dtmi:example:room;1` である DT コレクションからツインを返します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelExample":::

## <a name="is_bool"></a>IS_BOOL

式にブール値があるかどうかを判断するための型チェックとキャスト関数。

この関数は、クエリ結果を処理するプログラムでブール値を必要とし、プロパティがブール値ではないケースを除外する場合に、他の述語と組み合わせてよく使用されます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolSyntax":::

### <a name="arguments"></a>引数

`<expression>`、ブール値であるかどうかをチェックする式。

### <a name="returns"></a>戻り値

指定した式の型がブール値であるかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、ブール型の `HasTemperature` プロパティを持つデジタル ツインを選択しています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolExample":::

次のクエリでは、上の例に基づいて、ブール型の `HasTemperature` プロパティを持ち、そのプロパティの値が `false` ではないデジタル ツインを選択しています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolNotFalseExample":::

## <a name="is_number"></a>IS_NUMBER

式に数値が含まれているかどうかを判断するための型チェックとキャスト関数。

この関数は、クエリ結果を処理するプログラムで数値を必要とし、プロパティが数値ではないケースを除外する場合に、他の述語と組み合わせてよく使用されます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberSyntax":::

### <a name="arguments"></a>引数

`<expression>`、数値であるかどうかをチェックする式。

### <a name="returns"></a>戻り値

指定した式の型が数値であるかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、数値の `Capacity` プロパティを持ち、その値が 0 と等しくないデジタル ツインを選択しています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberExample":::

## <a name="is_string"></a>IS_STRING

式に文字列値があるかどうかを判断するための型チェックとキャスト関数。 

この関数は、クエリ結果を処理するプログラムで文字列値を必要とし、プロパティが文字列ではないケースを除外する場合に、他の述語と組み合わせてよく使用されます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringSyntax":::

### <a name="arguments"></a>引数

`<expression>`、文字列であるかどうかをチェックする式。

### <a name="returns"></a>戻り値

指定した式の型が文字列であるかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、文字列プロパティ `Status` プロパティを持ち、その値が `Completed` と等しくないデジタル ツインを選択しています。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringExample":::

## <a name="is_null"></a>IS_NULL

式の値が `null` かどうかを判断するための型チェックとキャスト関数。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullSyntax":::

### <a name="arguments"></a>引数

`<expression>`、null であるかどうかをチェックする式。

### <a name="returns"></a>戻り値

指定した式の型が `null` であるかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、Temperature に null 値がないツインが返されます。 このクエリで使用される `NOT` 演算子の詳細については、「[Azure Digital Twins クエリ言語リファレンス: 演算子](reference-query-operators.md#logical-operators)」を参照してください。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullExample":::

## <a name="is_primitive"></a>IS_PRIMITIVE

式の値がプリミティブ型 (文字列、ブール値、数値、または `null`) であるかどうかを判断するための型チェックとキャスト関数。

この関数は、クエリ結果を処理するプログラムでプリミティブ型の値を必要とし、プロパティがプリミティブではないケースを除外する場合に、他の述語と組み合わせてよく使用されます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveSyntax":::

### <a name="arguments"></a>引数

`<expression>`、プリミティブ型であるかどうかをチェックする式。

### <a name="returns"></a>戻り値

指定した式の型がプリミティブ型 (文字列、ブール値、数値、または `null`) であるかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、`area` プロパティがプリミティブ型の場合にのみ、ID が 'ABC' のファクトリの `area` プロパティが返されます。 クエリ結果への特定の列の射影 (このクエリの`area` の処理のように) の詳細については、「[Azure Digital Twins クエリ言語リファレンス: SELECT 句](reference-query-clause-select.md#select-columns-with-projections)」を参照してください。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveExample":::

## <a name="is_object"></a>IS_OBJECT

式の値が JSON オブジェクト型であるかどうかを判断するための型チェックとキャスト関数。

この関数は、クエリ結果を処理するプログラムで JSON オブジェクトを必要とし、値が JSON オブジェクトでないケースを除外する場合に、他の述語と組み合わせてよく使用されます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectSyntax":::

### <a name="arguments"></a>引数

`<expression>`、オブジェクト型であるかどうかをチェックする式。

### <a name="returns"></a>戻り値

指定した式の型が JSON オブジェクトであるかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、すべてのデジタル ツインを選択しています。これは、`MapObject` と呼ばれるオブジェクトで、子プロパティ `TemperatureReading` を持ちません。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectExample":::

## <a name="startswith"></a>STARTSWITH

指定された文字列が特定の他の文字列で始まるかどうかを判断するために使用される文字列関数。 

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithSyntax":::

### <a name="arguments"></a>引数

* `<string-to-check>`: 先頭をチェックするための文字列
* `<beginning-string>`: チェック対象の先頭を表す文字列

### <a name="returns"></a>戻り値

1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値。

### <a name="example"></a>例

次のクエリでは、ID が `area1-` で始まるすべてのデジタル ツインが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithExample":::

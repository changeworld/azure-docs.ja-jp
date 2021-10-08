---
title: テンプレート関数 - 比較
description: Azure Resource Manager テンプレート (ARM テンプレート) で値を比較するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 0f9243cdc61ad5e7710663328eb4f85c9471fda5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812355"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM テンプレートの比較関数

Resource Manager には、Azure Resource Manager テンプレート (ARM テンプレート) で比較を行うための関数がいくつか用意されています。

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

パラメーターから最初の null 以外の値を返します。 空の文字列、空の配列、空のオブジェクトは null ではありません。

Bicep では、`??` 演算子を使用してください。 [「Coalesce ?? 」](../bicep/operators-logical.md#coalesce-)を参照してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数、文字列、配列、オブジェクト |null かどうかがテストされる最初の値。 |
| その他の引数 |いいえ |整数、文字列、配列、オブジェクト | null かどうかを確かめるその他の値。 |

### <a name="return-value"></a>戻り値

最初の null 以外のパラメーターの値。文字列、整数、配列、またはオブジェクトが返されます。 すべてのパラメーターが null の場合は null になります。

### <a name="example"></a>例

次のテンプレート例では、coalesce をさまざまな方法で使用したときの出力を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/coalesce.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| stringOutput | String | default |
| intOutput | int | 1 |
| objectOutput | Object | {"first": "default"} |
| arrayOutput | Array | [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

2 つの値が互いに等しいかどうかを確認します。

Bicep では、代わりに `==` 演算子を使用してください。 [「Equals == 」](../bicep/operators-comparison.md#equals-)を参照してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数、文字列、配列、オブジェクト |等しいかどうかを確認する 1 番目の値。 |
| arg2 |はい |整数、文字列、配列、オブジェクト |等しいかどうかを確認する 2 番目の値。 |

### <a name="return-value"></a>戻り値

値が等しい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="remarks"></a>注釈

equals 関数は、リソースがデプロイされているかどうかをテストするために、多くの場合 `condition` 要素と共に使用されます。

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

### <a name="example"></a>例

次の例では、さまざまな型の値が等しいかどうかを確認します。 すべての既定値は True を返します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/equals.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

次のテンプレート例では、[not](template-functions-logical.md#not) と **equals** を使用します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

1 番目の値が 2 番目の値より大きいかどうかを確認します。

Bicep では、代わりに `>` 演算子を使用してください。 [「次の値より大きい」](../bicep/operators-comparison.md#greater-than-)を参照してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |大きいかどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |大きいかどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値よりも大きい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の例では、ある値がもう一方の値よりも大きいかどうかを確認します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greater.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

1 番目の値が 2 番目の値以上かどうかを確認します。

Bicep では、代わりに `>=` 演算子を使用してください。 [「次の値以上」](../bicep/operators-comparison.md#greater-than-or-equal-)を参照してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |以上かどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |以上かどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以上の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の例では、ある値がもう一方の値以上であるかどうかを確認します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greaterorequals.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

1 番目の値が 2 番目の値より小さいかどうかを確認します。

Bicep では、代わりに `<` 演算子を使用してください。 [「次の値より小さい」](../bicep/operators-comparison.md#less-than-)を参照してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |小さいかどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |小さいかどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値よりも小さい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の例では、ある値がもう一方の値よりも小さいかどうかを確認します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/less.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

1 番目の値が 2 番目の値以下かどうかを確認します。

Bicep では、代わりに `<=` 演算子を使用してください。 [「次の値以下」](../bicep/operators-comparison.md#less-than-or-equal-)を参照してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |以下かどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |以下かどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以下の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の例では、ある値がもう一方の値以下であるかどうかを確認します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/lessorequals.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。

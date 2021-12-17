---
title: テンプレート関数 - 数値
description: Azure Resource Manager テンプレート (ARM テンプレート) で数値の操作に使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ccfed6794b81b7910310cc5a9fd02dcf0cdb0e0f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820426"
---
# <a name="numeric-functions-for-arm-templates"></a>ARM テンプレート用の数値関数

Resource Manager では、Azure Resource Manager テンプレート (ARM テンプレート) で整数を操作するために、次の関数が提供されています。

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

指定された 2 つ整数の合計を返します。

`add` 関数は、Bicep ではサポートされていません。 代わりに、[`+` 演算子](../bicep/operators-numeric.md#add-)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
|operand1 |はい |INT |加算する最初の整数。 |
|operand2 |はい |INT |加算する 2 つ目の整数。 |

### <a name="return-value"></a>戻り値

パラメーターの合計を格納する整数。

### <a name="example"></a>例

次の例では、2 つのパラメーターを加算します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/add.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| addResult | int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

反復処理のループのインデックスを返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| loopName | いいえ | string | 反復処理の取得対象となるループの名前。 |
| offset |いいえ |INT |0 から始まる反復値に追加する整数。 |

### <a name="remarks"></a>注釈

この関数は常に **copy** オブジェクトと共に使用されます。 **offset** の値が指定されていない場合、現在の反復値が返されます。 反復値は 0 から始まります。

copyIndex がリソースの反復処理を指すのかプロパティの反復処理を指すのかは、**loopName** プロパティで指定できます。 **loopName** に値を指定しなかった場合は、現在のリソース タイプの反復処理が使われます。 プロパティに対する反復では、**loopName** に値を指定してください。

copy の使い方の詳細については、次を参照してください。

* [ARM テンプレートでのリソースの反復処理](copy-resources.md)
* [ARM テンプレートでのプロパティの反復処理](copy-properties.md)
* [ARM テンプレートでの変数の反復処理](copy-variables.md)
* [ARM テンプレートでの出力の反復処理](copy-outputs.md)

### <a name="example"></a>例

次の例では、コピー ループと、名前に含まれるインデックス値を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/copyindex.json":::

### <a name="return-value"></a>戻り値

反復値の現在のインデックスを表す整数。

## <a name="div"></a>div

`div(operand1, operand2)`

指定された 2 つの整数の整数除算を返します。

`div` 関数は、Bicep ではサポートされていません。 代わりに、[`/` 演算子](../bicep/operators-numeric.md#divide-)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |INT |除算される整数。 |
| operand2 |はい |INT |除算に使用される整数。 0 にすることはできません。 |

### <a name="return-value"></a>戻り値

除算を表す整数。

### <a name="example"></a>例

次の例では、一方のパラメーターをもう一方のパラメーターで除算します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/div.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| divResult | int | 2 |

## <a name="float"></a>float

`float(arg1)`

値を浮動小数点数に変換します。 この関数は、ロジック アプリなどのアプリケーションにカスタム パラメーターを渡す場合にのみ使用します。

`float` 関数は、Bicep ではサポートされていません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |浮動小数点数に変換する値。 |

### <a name="return-value"></a>戻り値

浮動小数点数。

### <a name="example"></a>例

次の例では、ロジック アプリにパラメーターを渡すために浮動小数点数を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/float.json":::

## <a name="int"></a>INT

`int(valueToConvert)`

指定された値を整数に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい |文字列または整数 |整数に変換する値。 |

### <a name="return-value"></a>戻り値

変換された値の整数。

### <a name="example"></a>例

次のテンプレート例では、ユーザー指定のパラメーター値を整数に変換します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/int.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| intResult | int | 4 |

## <a name="max"></a>max

`max (arg1)`

整数の配列または整数のコンマ区切りリストから最大値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最大値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

コレクションの最大値を表す整数。

### <a name="example"></a>例

次の例では、max を配列および整数のリストと共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min (arg1)`

整数の配列または整数のコンマ区切りリストから最小値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最小値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

コレクションの最小値を表す整数。

### <a name="example"></a>例

次の例では、min を配列および整数のリストと共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

指定された 2 つの整数を使用した整数除算の剰余を返します。

`mod` 関数は、Bicep ではサポートされていません。 代わりに、[% 演算子](../bicep/operators-numeric.md#modulo-)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |INT |除算される整数。 |
| operand2 |はい |INT |除算に使用される整数。0 にすることはできません。 |

### <a name="return-value"></a>戻り値

剰余を表す整数。

### <a name="example"></a>例

次の例では、一方のパラメーターをもう一方のパラメーターで除算した剰余を返します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mod.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| modResult | int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

指定された 2 つの整数の乗算を返します。

`mul` 関数は、Bicep ではサポートされていません。 代わりに、[* 演算子](../bicep/operators-numeric.md#multiply-)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |INT |乗算する最初の整数。 |
| operand2 |はい |INT |乗算する 2 つ目の整数。 |

### <a name="return-value"></a>戻り値

乗算を表す整数。

### <a name="example"></a>例

次の例では、一方のパラメーターをもう一方のパラメーターで乗算します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mul.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| mulResult | int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

指定された 2 つの整数の減算を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |INT |減算される整数。 |
| operand2 |はい |INT |減算する整数。 |

### <a name="return-value"></a>戻り値

減算を表す整数。

### <a name="example"></a>例

次の例では、一方のパラメーターからもう一方のパラメーターを減算します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/sub.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| subResult | int | 4 |

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。
* ある種類のリソースを作成するときに、指定した回数だけ反復するには、「[ARM テンプレートでのリソースの反復](copy-resources.md)」を参照してください。

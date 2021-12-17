---
title: テンプレート関数 - 論理
description: Azure Resource Manager テンプレート (ARM テンプレート) で論理値を判定するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: b94f7aa38c708278f2ccf54a5592016873fcd285
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744357"
---
# <a name="logical-functions-for-arm-templates"></a>ARM テンプレート用の論理関数

Resource Manager には、Azure Resource Manager テンプレート (ARM テンプレート) で比較を行うための関数がいくつか用意されています。

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

## <a name="and"></a>および

`and(arg1, arg2, ...)`

すべてのパラメーター値が true かどうかを確認します。

`and` 関数は Bicep ではサポートされていません。代わりに [&& 演算子](../bicep/operators-logical.md#and-)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |true かどうかを確認する最初の値。 |
| arg2 |はい |boolean |true かどうかを確認する 2 番目の値。 |
| その他の引数 |いいえ |boolean |true かどうかを確認するその他の引数。 |

### <a name="return-value"></a>戻り値

すべての値が true の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="examples"></a>例

次の例では、論理関数を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>[bool]

`bool(arg1)`

パラメーターをブール値に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |ブール値に変換する値。 |

### <a name="return-value"></a>戻り値

変換後の値のブール値。

### <a name="remarks"></a>解説

[true ()](#true) と [false ()](#false) を使用してブール値を取得することもできます。

### <a name="examples"></a>例

次の例では、ブール値を文字列または整数と共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/bool.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="false"></a>false

`false()`

false を返します。

`false` 関数は、Bicep では使用できません。  代わりに、`false` キーワードを使用します。

### <a name="parameters"></a>パラメーター

false 関数では、パラメーターは受け入れられません。

### <a name="return-value"></a>戻り値

常に false のブール値です。

### <a name="example"></a>例

次の例では、false の出力値が返されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/false.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

条件が true か false かに基づいて値を返します。

`if` 関数は、Bicep ではサポートされていません。 代わりに、[?: 演算子](../bicep/operators-logical.md#conditional-expression--)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| condition |はい |boolean |true か false かどうかを確認する値。 |
| trueValue |はい | string、int、object、または array |条件が true の場合に返される値。 |
| falseValue |はい | string、int、object、または array |条件が false の場合に返される値。 |

### <a name="return-value"></a>戻り値

最初のパラメーターが **True** の場合、2 番目のパラメーターを返します。そうでない場合、3 番目のパラメーターを返します。

### <a name="remarks"></a>解説

条件が **True** の場合、true の値のみが評価されます。 条件が **False** の場合、false の値のみが評価されます。 `if` 関数の場合、条件付きで有効な式のみを含めることができます。 たとえば、1 つの条件の下で存在し、他の条件下では存在しないリソースを参照できます。 条件付きでの式の評価の例については、次のセクションを参照してください。

### <a name="examples"></a>例

次の例は、`if` 関数の使用法を示しています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/if.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| yesOutput | String | はい |
| noOutput | String | no |
| objectOutput | Object | { "test": "value1" } |

次の[テンプレート例](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json)では、条件付きで有効な式限定で、この関数を使用する方法を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

## <a name="not"></a>not

`not(arg1)`

ブール値をその反対の値に変換します。

`not` 関数は Bicep ではサポートされていません。代わりに [! 演算子](../bicep/operators-logical.md#not-)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |変換する値。 |

### <a name="return-value"></a>戻り値

パラメーターが **False** の場合、**True** を返します。 パラメーターが **True** の場合、**False** を返します。

### <a name="examples"></a>例

次の例では、論理関数を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

`not` を [equals](template-functions-comparison.md#equals) と共に使用する例を次に示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>または

`or(arg1, arg2, ...)`

いずれかのパラメーター値が true かどうかを確認します。

`or` 関数は Bicep ではサポートされていません。代わりに [|| 演算子](../bicep/operators-logical.md#or-)を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |true かどうかを確認する最初の値。 |
| arg2 |はい |boolean |true かどうかを確認する 2 番目の値。 |
| その他の引数 |いいえ |boolean |true かどうかを確認するその他の引数。 |

### <a name="return-value"></a>戻り値

いずれかの値が true の場合は **True** を返し、そうでない場合は **False** を返します。

### <a name="examples"></a>例

次の例では、論理関数を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/andornot.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="true"></a>true

`true()`

true を返します。

`true` 関数は、Bicep では使用できません。  代わりに、`true` キーワードを使用します。

### <a name="parameters"></a>パラメーター

true 関数では、パラメーターは受け入れられません。

### <a name="return-value"></a>戻り値

常に true のブール値です。

### <a name="example"></a>例

次の例では、true の出力値が返されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/true.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。

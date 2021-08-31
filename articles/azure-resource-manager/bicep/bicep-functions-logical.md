---
title: Bicep 関数 - 論理関数
description: Bicep ファイルで論理値の取得に使用する関数について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 8fe09057ea293300754fc35614c00a9b808da247
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744911"
---
# <a name="logical-functions-for-bicep"></a>Bicep の論理関数

Resource Manager では、Bicep 用に`bool`関数を用意しています。 

Azure Resource Manager テンプレート内の論理関数の多くは、Bicep の[論理演算子](./operators-logical.md)に置き換えられています。

## <a name="bool"></a>[bool]

`bool(arg1)`

パラメーターをブール値に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |ブール値に変換する値。 |

### <a name="return-value"></a>戻り値

変換後の値のブール値。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)では、ブール値を文字列または整数と共に使用する方法を示します。

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | [値] |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>次のステップ

* ほとんどの論理演算子については、「[Bicep 論理演算子](./operators-logical.md)」を参照してください。
* Bicep ファイルのセクションの説明は、[Bicep ファイルの構造と構文](./file.md)に関する記事をご覧ください。

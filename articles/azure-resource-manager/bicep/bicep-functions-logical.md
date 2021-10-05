---
title: Bicep 関数 - 論理関数
description: Bicep ファイルで論理値の取得に使用する関数について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: e3a811102c31301386135042c79c205eeb9f7f9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758429"
---
# <a name="logical-functions-for-bicep"></a>Bicep の論理関数

Bicep は、値をブール値に変換する `bool` 関数を提供します。

Azure Resource Manager テンプレート内の論理関数の多くは、Bicep の[論理演算子](./operators-logical.md)に置き換えられています。

## <a name="bool"></a>[bool]

`bool(arg1)`

パラメーターをブール値に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |ブール値に変換する値。 |

### <a name="return-value"></a>戻り値

変換後の値のブール値。

### <a name="examples"></a>例

次の例では、ブール値を文字列または整数と共に使用する方法を示します。

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>次のステップ

* 論理値に関連するその他のアクションについては、「[論理演算子](./operators-logical.md)」を参照してください。

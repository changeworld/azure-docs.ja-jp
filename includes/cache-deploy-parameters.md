---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67180984"
---
### <a name="cacheskuname"></a>cacheSKUName

新しい Azure Cache for Redis の価格レベルです。

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

テンプレートには、このパラメーターに指定できる値 (Basic、Standard、または Premium) を定義します。値が指定されない場合は既定値 (Basic) が割り当てられます。 Basic では、複数のサイズを最大 53 GB まで使用できる 1 つのノードが提供されます。 Standard では、複数のサイズを最大 53 GB まで使用できる 2 つのノード (プライマリとレプリカ) が提供され、99.9% の SLA が実現されます。

### <a name="cacheskufamily"></a>cacheSKUFamily

SKU のファミリです。

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

新しい Azure Cache for Redis インスタンスのサイズです。

Basic および Standard ファミリの場合:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Premium の値キャッシュ容量の定義は同じですが、許可される値が 0 から 6 ではなく 1 から 5 です。

テンプレートでは、このパラメーターに許可される整数値が定義されます。 (Basic および Standard ファミリでは 0 から 6、Premium ファミリでは 1 から 5)。 値が指定されないと、テンプレートによって既定値 (Basic と Standard では 0、Premium では 1) が割り当てられます。

値は次のキャッシュ サイズに対応します。

| 値 | Basic および Standard<br>キャッシュ サイズ | Premium<br>キャッシュ サイズ |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (既定)                 | 該当なし                   |
| 1     | 1 GB                             | 6 GB (既定)        |
| 2     | 2.5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | 該当なし                   |

---
title: リンター ルール - 補間を優先する
description: リンター ルール - 補間を優先する
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 952359c7c258c084e19d6faf152536a3e50212b2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164471"
---
# <a name="linter-rule---prefer-interpolation"></a>リンター ルール - 補間を優先する

このルールは、文字列補間で置き換えることができる concat 関数の使用を検出します。

## <a name="returned-code"></a>返されるコード

`prefer-interpolation`

## <a name="solution"></a>解決策

concat 関数の代わりに、文字列補間を使用します。

concat 関数が使用されているため、次の例はこのテストに不合格になります。

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)
```

これを修正するには、concat を文字列補間に置き換えます。 次の例は、このテストで合格になります。

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'
```

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

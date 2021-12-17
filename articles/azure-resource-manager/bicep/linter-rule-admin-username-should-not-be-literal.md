---
title: リンター ルール - 管理者ユーザー名をリテラルにすることはできません
description: リンター ルール - 管理者ユーザー名をリテラルにすることはできません
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: fe13ed227b0f7c7d5a7b738ed675f6c5d54eded2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167761"
---
# <a name="linter-rule---admin-user-name-should-not-be-literal"></a>リンター ルール - 管理者ユーザー名をリテラルにすることはできません

このルールは、管理者ユーザー名がリテラル値に設定されている場合を検索します。

## <a name="returned-code"></a>返されるコード

`adminusername-should-not-be-literal`

## <a name="solution"></a>解決策

リテラル値や、リテラル値に評価される式は使用しないでください。 代わりに、ユーザー名のパラメーターを作成し、それを管理者ユーザー名に割り当てます。

次の例では、ユーザー名がリテラル値であるため、このテストは失敗します。

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: 'adminUsername'
    }
  }
}
```

次の例では、既定値が使用されている場合に式がリテラル値に評価されるため、このテストは失敗します。

```bicep
var defaultAdmin = 'administrator'
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: defaultAdmin
    }
  }
}
```

次の例は、このテストに合格します。

```bicep
@secure()
param adminUsername string
param location string
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: adminUsername
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

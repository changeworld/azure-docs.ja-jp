---
title: リンター ルール - 補間を簡略化する
description: リンター ルール - 補間を簡略化する
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: e2e0f9eca607ececad22c025e89b7c976cd0d05a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165343"
---
# <a name="linter-rule---simplify-interpolation"></a>リンター ルール - 補間を簡略化する

このルールでは、文字列補間が不要な場合にそれを使用する構文が検索されます。

## <a name="returned-code"></a>返されるコード

`simplify-interpolation`

## <a name="solution"></a>解決策

値を結合するために式の一部ではない文字列補間を使用しないようにします。

次の例では、パラメーターを参照するだけなので、このテストは失敗します。

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

これは、文字列補間構文を削除することで修正できます。

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

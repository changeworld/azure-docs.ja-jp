---
title: リンター ルール - 補間を簡略化する
description: リンター ルール - 補間を簡略化する
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: aff3df9bd7b357dcfdfd62f87971580399b3f2ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699280"
---
# <a name="linter-rule---simplify-interpolation"></a>リンター ルール - 補間を簡略化する

リンターを使用すると、開発時にガイダンスを提供することで、コーディング標準を簡単に適用できます。 現在のリンター ルールのセットは最小限であり、[arm-ttk test cases](../templates/template-test-cases.md) から取得されます。

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

## <a name="code"></a>コード

`simplify-interpolation`

## <a name="description"></a>説明

パラメーターまたは変数を参照するために補間を使用する必要はありません。

## <a name="examples"></a>例

次の例は、このテストで不合格になります。

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

次の例は、このテストで合格になります。

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>次のステップ

Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。

---
title: リンター ルール - 未使用のパラメーターがない
description: リンター ルール - 未使用のパラメーターがない
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: e8304948c2b373d16a7fa48c38ac4bac9ec806df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699662"
---
# <a name="linter-rule---no-unused-parameters"></a>リンター ルール - 未使用のパラメーターがない

リンターを使用すると、開発時にガイダンスを提供することで、コーディング標準を簡単に適用できます。 現在のリンター ルールのセットは最小限であり、[arm-ttk test cases](../templates/template-test-cases.md) から取得されます。

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

## <a name="code"></a>コード

`no-unused-params`

## <a name="description"></a>説明

テンプレートの混乱を軽減するには、定義されていても使用されていないパラメーターを削除します。 このテストでは、テンプレートのどこでも使用されていないパラメーターが検索されます。 使用されていないパラメーターを削除すると、不要な値を指定する必要がないため、テンプレートのデプロイも容易になります。

## <a name="next-steps"></a>次のステップ

Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。

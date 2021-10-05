---
title: リンター ルール - セキュリティ保護されたパラメーターの既定値
description: リンター ルール - セキュリティ保護されたパラメーターの既定値
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4fa82fbbe74d9bf51d1eb498341b999a89e12978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699281"
---
# <a name="linter-rule---secure-parameter-default"></a>リンター ルール - セキュリティ保護されたパラメーターの既定値

リンターを使用すると、開発時にガイダンスを提供することで、コーディング標準を簡単に適用できます。 現在のリンター ルールのセットは最小限であり、[arm-ttk test cases](../templates/template-test-cases.md) から取得されます。

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

## <a name="code"></a>コード

`secure-parameter-default`

## <a name="description"></a>説明

ハードコーディングされた既定値は、空であるか [newGuid()](./bicep-functions-string.md#newguid) への呼び出しを含む式を使用している場合を除き、テンプレート内で[セキュリティ保護されたパラメーター](./parameters.md#secure-parameters)に指定しないでください。

パスワードのような機密性の高い値を含むパラメーターでは、@secure() デコレーターを使用します。 パラメーターでセキュリティ保護されたデコレーターが使用されていると、そのパラメーターの値はログに記録されず、デプロイ履歴に格納されません。 このアクションにより、悪意のあるユーザーが機密値を検出できなくなります。

ただし、セキュリティ保護されたパラメーターに既定値を指定すると、テンプレートまたはデプロイ履歴にアクセスできるすべてのユーザーが、その値を検出できます。

## <a name="examples"></a>例

次の例は、このテストで不合格になります。

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

次の例は、このテストに合格します。

```bicep
@secure()
param adminPassword string
```

```bicep
@secure()
param adminPassword string = ''
```

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>次のステップ

Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。

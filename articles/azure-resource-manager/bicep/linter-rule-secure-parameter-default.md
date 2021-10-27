---
title: リンター ルール - セキュリティ保護されたパラメーターの既定値
description: リンター ルール - セキュリティ保護されたパラメーターの既定値
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 139b19124f22d5cb42be71d6a6042830ad7809f7
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166479"
---
# <a name="linter-rule---secure-parameter-default"></a>リンター ルール - セキュリティ保護されたパラメーターの既定値

このルールでは、セキュリティ保護されたパラメーターのハードコーディングされた既定値が検索されます。

## <a name="returned-code"></a>返されるコード

`secure-parameter-default`

## <a name="solution"></a>解決策

ハードコーディングされた既定値は、空の文字列であるか、[newGuid()](./bicep-functions-string.md#newguid) 関数を呼び出す式である場合を除き、Bicep ファイル内で[セキュリティ保護されたパラメーター](./parameters.md#secure-parameters)に指定しないでください。

パスワードのような機密性の高い値が含まれるパラメーターでは、`@secure()` デコレーターを使用します。 パラメーターでセキュリティ保護されたデコレーターが使用されていると、そのパラメーターの値はログに記録されず、デプロイ履歴に格納されません。 このアクションにより、悪意のあるユーザーが機密値を検出できなくなります。

ただし、セキュリティ保護されたパラメーターに既定値を指定すると、テンプレートまたはデプロイ履歴にアクセスできるすべてのユーザーが、その値を検出できます。

次の例は、パラメーターにハードコーディングされた既定値が含まれるため、このテストに失敗します。

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

既定値を削除することで解決できます。

```bicep
@secure()
param adminPassword string
```

または、既定値に空の文字列を指定します。

```bicep
@secure()
param adminPassword string = ''
```

または、`newGuid()` を使用して既定値を生成します。

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>次のステップ

リンターの詳細については、「[Bicep リンターの使用方法](./linter.md)」を参照してください。

---
title: テンプレートのデータ型
description: Azure Resource Manager テンプレートで利用できるデータ型について説明します。
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 06/24/2021
ms.openlocfilehash: 4fc69126ee1f555e71e152a7c0369e4b81b8bf6a
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894232"
---
# <a name="data-types-in-arm-templates"></a>ARM テンプレートのデータ型

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) でサポートされるデータ型について説明します。

## <a name="supported-types"></a>サポートされている型

ARM テンプレート内では、次のデータ型を使用できます。

* array
* [bool]
* INT
* object
* secureObject
* secureString
* string

## <a name="arrays"></a>配列

配列は左大かっこ (`[`) で始めて、右大かっこ (`]`) で終わります。 配列は単一行または複数行で宣言できます。 各要素はコンマで区切られます。

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},

"outputs": {
  "arrayOutput": {
    "type": "array",
    "value": "[variables('exampleArray')]"
  },
  "firstExampleArrayElement": {
    "type": "int",
    "value": "[parameters('exampleArray')[0]]"
  }
}
```

配列の要素は同じ型にすることも、異なる型にすることもできます。

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}

"outputs": {
  "arrayOutput": {
    "type": "array",
    "value": "[variables('mixedArray')]"
  },
  "firstMixedArrayElement": {
    "type": "string",
    "value": "[variables('mixedArray')[0]]"
  }
}
```

## <a name="booleans"></a>ブール値

ブール値を指定するとき、`true` または `false` を使用します。 値を引用符で囲まないでください。

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

## <a name="integers"></a>整数

整数値を指定するとき、引用符を使用しないでください。

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

インライン パラメーターとして渡される整数の場合、値の範囲はデプロイに使用する SDK またはコマンドライン ツールによって制限されることがあります。 たとえば、PowerShell を使用してテンプレートをデプロイする場合、整数型は -2147483648 から 2147483647 の範囲で指定できます。 この制限を回避するには、[パラメーター ファイル](parameter-files.md)で大きな整数値を指定します。 リソースの種類によって、整数プロパティに独自の制限が適用されます。

## <a name="objects"></a>オブジェクト

オブジェクトは左中かっこ (`{`) で始めて、右中かっこ (`}`) で終わります。 オブジェクト内の各プロパティは `key` と `value` で構成されます。 `key` と `value` は二重引用符で囲まれ、コロン (`:`) で区切られています。 各プロパティはコンマで区切られます。

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

## <a name="strings"></a>文字列

文字列は二重引用符でマークされます。

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

## <a name="secure-strings-and-objects"></a>セキュリティで保護された文字列とオブジェクト

セキュリティで保護された文字列では文字列と同じ形式が使用され、セキュリティで保護されたオブジェクトではオブジェクトと同じ形式が使用されます。 パラメーターをセキュリティで保護された文字列またはセキュリティで保護されたオブジェクトに設定した場合、パラメーターの値はデプロイ履歴に保存されず、ログにも記録されません。 ただし、セキュリティで保護された値を、セキュリティで保護された値を想定していないプロパティに設定した場合、その値は保護されません。 たとえば、セキュリティで保護された文字列をタグに設定すると、その値はプレーンテキストとして格納されます。 パスワードとシークレットにはセキュリティで保護された文字列を使用します。

次の例は、セキュリティで保護された 2 つのパラメーターを示しています。

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

## <a name="next-steps"></a>次のステップ

テンプレート構文の詳細については、「[ARM テンプレートの構造と構文の詳細](./syntax.md)」を参照してください。
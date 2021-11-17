---
title: Bicep の変数
description: Bicep で変数を定義する方法について説明します
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 35175cff08b1470725da87015a3faef9f5bc3ed4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494788"
---
# <a name="variables-in-bicep"></a>Bicep の変数

この記事では、Bicep ファイルで変数を定義および使用する方法について説明します。 変数を使用して、Bicep ファイルの開発を簡略化します。 Bicep ファイル全体で複雑な式を繰り返すのではなく、複雑な式を含む変数を定義します。 次に、Bicep ファイル全体で、必要に応じてその変数を使用します。

Resource Manager は、デプロイ操作を開始する前に変数を解決します。 Bicep ファイルで変数が使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

## <a name="define-variable"></a>変数を定義する

変数を定義するための構文は次のとおりです。

```bicep
var <variable-name> = <variable-value>
```

変数の名前を変数、パラメーター、モジュール、またはリソースと同じにすることはできません。

その変数の[データ型](data-types.md)は指定しないことに注意してください。 型は値から推論されます。 次の例では、変数を文字列に設定します。

```bicep
var stringVar = 'example value'
```

変数を構築する際には、パラメーターまたは別の変数からの値を使用できます。

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'preset variable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'

output addToVar string = concatToVar
output addToParam string = concatToParam
```

上記の例では、以下が返されます。

```json
{
  "addToParam": {
    "type": "String",
    "value": "deployment parameterAddToParam"
  },
  "addToVar": {
    "type": "String",
    "value": "preset variableAddToVar"
  }
}
```

[Bicep 関数](bicep-functions.md)を使用すると、変数の値を作成できます。 次の例では、Bicep 関数を使用して、ストレージ アカウント名の文字列値を作成します。

```bicep
param storageNamePrefix string = 'stg'
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

output uniqueStorageName string = storageName
```

次の例では、以下のような値が返されます。

```json
"uniqueStorageName": {
  "type": "String",
  "value": "stghzuunrvapn6sw"
}
```

変数を定義するときに反復ループを使用できます。 次の例では、3 つのプロパティを持つオブジェクトの配列を作成します。

```bicep
param itemCount int = 3

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

出力には、次の値を含む配列が返されます。

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

変数で使用できるループの種類の詳細については、「[Bicep の反復ループ](loops.md)」を参照してください。

## <a name="use-variable"></a>変数を使用する

次の例は、リソース プロパティに変数を使用する方法を示しています。 変数名 `storageName` を指定することによって変数の値を参照します。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variableswithfunction.bicep" highlight="4,7,15" :::

ストレージ アカウント名では小文字を使用する必要があるため、`storageName` 変数は `toLower` 関数を使用して `storageNamePrefix` 値を小文字にします。 `uniqueString` 関数は、リソース グループ ID から一意の値を作成します。 値は文字列に連結されます。

## <a name="configuration-variables"></a>構成変数

環境を構成するための関連する値を保持する変数を定義できます。 変数は、値を持つオブジェクトとして定義します。 次の例は、**test** と **prod** という 2 つの環境の値を保持するオブジェクトを示しています。デプロイ中に、これらの値のいずれかを渡します。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>次の手順

- 変数に使用できるプロパティの詳細については、「[Bicep ファイルの構造と構文について](file.md)」をご覧ください。
- ループ構文の使用の詳細については、「[Bicep の反復ループ](loops.md)」を参照してください。
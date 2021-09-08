---
title: Bicep ファイルのパラメーター
description: Bicep ファイルでパラメーターを定義する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 901e95708be75ebd4415c90dbd51eeb46ba492c4
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450253"
---
# <a name="parameters-in-bicep"></a>Bicep のパラメーター

この記事では、Bicep ファイルでパラメーターを定義および使用する方法について説明します。 パラメーターに異なる値を指定することにより、さまざまな環境で Bicep ファイルを再利用できます。

Resource Manager は、デプロイ操作を開始する前にパラメーター値を解決します。 パラメーターが使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

各パラメーターは、いずれかの[データ型](data-types.md)に設定する必要があります。

## <a name="minimal-declaration"></a>最小限の宣言

各パラメーターには名前と型が必要です。 パラメーターに、同じスコープ内の変数、リソース、出力、またはその他のパラメーターと同じ名前を付けることはできません。

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="decorators"></a>デコレータ

パラメーターでは、制約またはメタデータにデコレーターを使用します。 デコレーターは `@expression` という形で使用し、パラメーターの宣言の上に配置されます。

```bicep
@expression
param stgAcctName string
```

この記事では、Bicep ファイルで使用できるデコレーターの使用方法について説明します。

## <a name="secure-parameters"></a>セキュリティで保護されたパラメーター

文字列またはオブジェクトのパラメーターをセキュリティで保護されたものとマークすることができます。 セキュリティで保護されたパラメーターの値はデプロイ履歴に保存されず、ログに記録されません。

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

## <a name="allowed-values"></a>使用できる値

パラメーターに使用できる値を定義できます。 使用できる値は配列で指定します。 使用できる値の 1 つではない値がパラメーターに渡された場合、検証時にデプロイは失敗します。

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

## <a name="default-value"></a>既定値

パラメーターの既定値を指定できます。 既定値は、デプロイ時に値が指定されない場合に使用されます。

```bicep
param demoParam string = 'Contoso'
```

パラメーターの他のプロパティと共に既定値を指定するには、次の構文を使用します。

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

既定値を指定した式を使用できます。 他のパラメーター プロパティと共に式を使用することはできません。 パラメーター セクションでは、[reference](bicep-functions-resource.md#reference) 関数も、いずれの [list](bicep-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイムの状態を取得します。パラメーターが解決されるとき、デプロイの前に実行することはできません。

```bicep
param location string = resourceGroup().location
```

別のパラメーター値を使用して既定値を作成できます。 次のテンプレートを使用すると、サイト名からホスト プラン名が作成されます。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterswithfunctions.bicep":::

## <a name="length-constraints"></a>長さの制限

文字列と配列のパラメーターの最小長と最大長を指定できます。 一方または両方の制約を設定できます。 文字列の場合、長さは文字数を示します。 配列の場合、長さは配列内の項目数を示します。

次の例では、2 つのパラメーターを宣言しています。 1 つ目のパラメーターは、文字数が 3-24 である必要があるストレージ アカウント名用です。 もう 1 つのパラメーターは、項目数が 1-5 個である必要がある配列です。

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

## <a name="integer-constraints"></a>整数の制約

整数パラメーターの最小値と最大値を設定できます。 一方または両方の制約を設定できます。

```bicep
@minValue(1)
@maxValue(12)
param month int
```

## <a name="description"></a>説明

指定する値をユーザーが理解できるように、パラメーターに説明を追加します。 ポータルからテンプレートをデプロイする場合、説明のテキストがそのパラメーターのヒントとして自動的に使用されます。 パラメーター名から推測できる情報よりもテキストからわかる情報の方が多い場合にのみ、説明を追加します。

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

## <a name="use-parameter"></a>パラメーターを使用する

パラメーターの値を参照するには、パラメーター名を使用します。 次の例では、キー コンテナー名のパラメーター値を使用します。

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

## <a name="objects-as-parameters"></a>パラメーターとしてのオブジェクト

関連する値は 1 つのオブジェクトとして渡すことにより整理しやすくなります。 この方法により、テンプレート内のパラメータ数も減少します。

次の例は、オブジェクトであるパラメーターを示しています。 既定値は、オブジェクトの予想されるプロパティを示しています。 これらのプロパティは、デプロイするリソースを定義するときに使用されます。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterobject.bicep":::


## <a name="next-steps"></a>次のステップ

- パラメーターに使用できるプロパティの詳細については、「[Bicep ファイルの構造と構文について](file.md)」をご覧ください。
- パラメーター値をファイルとして渡す方法については、「[Bicep パラメーター ファイルの作成](parameter-files.md)」を参照してください。

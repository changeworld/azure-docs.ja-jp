---
title: Bicep ファイルのパラメーター
description: Bicep ファイルでパラメーターを定義する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4345269d9c1191545a28998a38aeedb14b37e0bc
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486611"
---
# <a name="parameters-in-bicep"></a>Bicep のパラメーター

この記事では、Bicep ファイルでパラメーターを定義および使用する方法について説明します。 パラメーターに異なる値を指定することにより、さまざまな環境で Bicep ファイルを再利用できます。

Resource Manager は、デプロイ操作を開始する前にパラメーター値を解決します。 パラメーターが使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

各パラメーターは、いずれかの[データ型](data-types.md)に設定する必要があります。

### <a name="microsoft-learn"></a>Microsoft Learn

パラメーターの詳細とハンズオン ガイダンスについては、**Microsoft Learn** で「[パラメーターを使用して再利用可能な Bicep テンプレートを構築する](/learn/modules/build-reusable-bicep-templates-parameters)」を参照してください。

## <a name="declaration"></a>宣言

それぞれのパラメーターには、名前と[データ型](data-types.md)があります。 パラメーターには、必要に応じて既定値を指定できます。

```bicep
param <parameter-name> <parameter-data-type> = <default-value>
```

パラメーターに、同じスコープ内の変数、リソース、出力、またはその他のパラメーターと同じ名前を付けることはできません。

次に示したのは、基本的なパラメーター宣言の例です。

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="default-value"></a>既定値

パラメーターの既定値を指定できます。 既定値は、デプロイ時に値が指定されない場合に使用されます。

```bicep
param demoParam string = 'Contoso'
```

既定値を指定した式を使用できます。 他のパラメーター プロパティと共に式を使用することはできません。 パラメーター セクションでは、[reference](bicep-functions-resource.md#reference) 関数も、いずれの [list](bicep-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイムの状態を取得します。パラメーターが解決されるとき、デプロイの前に実行することはできません。

```bicep
param location string = resourceGroup().location
```

別のパラメーター値を使用して既定値を作成できます。 次のテンプレートを使用すると、サイト名からホスト プラン名が作成されます。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterswithfunctions.bicep" highlight="2":::

## <a name="decorators"></a>デコレータ

パラメーターでは、制約またはメタデータにデコレーターを使用します。 デコレーターは `@expression` という形で使用し、パラメーターの宣言の上に配置されます。 パラメータが機密であることをマークしたり、使用できる値を指定したり、文字列の最小長と最大長を設定したり、整数の最小値と最大値を設定したり、パラメータの説明を記載したりすることができます。

次の例は、デコレーターの 2 つの一般的な用途を示しています。

```bicep
@secure()
param demoPassword string

@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

次の表では、使用できるデコレーターとそれらの使用方法について説明します。

| デコレーター | [適用対象] | 引数 | 説明 |
| --------- | ---- | ----------- | ------- |
| [[許可]](#allowed-values) | all | array | パラメーターに使用できる値。 このデコレーターを使用して、ユーザーが正しい値を指定するようにします。 |
| [description](#description) | all | string | パラメーターの使用方法を説明するテキスト。 説明は、ポータルを通じてユーザーに表示されます。 |
| [maxLength](#length-constraints) | array、string | INT | 文字列および配列のパラメーターの最大長。 この値は包含値です。 |
| [maxValue](#integer-constraints) | INT | INT | 整数パラメーターの最大値。 この値は包含値です。 |
| metadata | all | object | パラメーターに適用するカスタム プロパティ。 description デコレーターに相当する description プロパティを含めることができます。 |
| [minLength](#length-constraints) | array、string | INT | 文字列および配列のパラメーターの最小長。 この値は包含値です。 |
| [minValue](#integer-constraints) | INT | INT | 整数パラメーターの最小値。 この値は包含値です。 |
| [secure](#secure-parameters) | string、object | なし | パラメーターを、セキュリティで保護されているとしてマークします。 セキュリティで保護されたパラメーターの値はデプロイ履歴に保存されず、ログに記録されません。 詳細については、「[セキュリティで保護された文字列とオブジェクト](data-types.md#secure-strings-and-objects)」を参照してください。 |

デコレーターは、[sys 名前空間](bicep-functions.md#namespaces-for-functions)にあります。 このデコレーターを同じ名前の別の項目と区別する必要がある場合は、デコレータの前に「`sys`」を付けます。 たとえば、Bicep ファイルに `description` という名前のパラメーターが含まれている場合、**description** デコレーターを使用するときに、sys 名前空間を追加する必要があります。

```bicep
@sys.description('The name of the instance.')
param name string
@sys.description('The description of the instance to display.')
param description string
```

使用可能なデコレーターについては、次のセクションで説明します。

### <a name="secure-parameters"></a>セキュリティで保護されたパラメーター

文字列またはオブジェクトのパラメーターをセキュリティで保護されたものとマークすることができます。 セキュリティで保護されたパラメーターの値はデプロイ履歴に保存されず、ログに記録されません。

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

### <a name="allowed-values"></a>使用できる値

パラメーターに使用できる値を定義できます。 使用できる値は配列で指定します。 使用できる値の 1 つではない値がパラメーターに渡された場合、検証時にデプロイは失敗します。

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

### <a name="length-constraints"></a>長さの制限

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

### <a name="integer-constraints"></a>整数の制約

整数パラメーターの最小値と最大値を設定できます。 一方または両方の制約を設定できます。

```bicep
@minValue(1)
@maxValue(12)
param month int
```

### <a name="description"></a>説明

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

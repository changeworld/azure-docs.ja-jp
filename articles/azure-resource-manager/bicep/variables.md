---
title: Bicep の変数
description: Bicep で変数を定義する方法について説明します
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 99c7d945f251319bf1b655ecd02730b62e3ed2e4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026937"
---
# <a name="variables-in-bicep"></a>Bicep の変数

この記事では、Bicep ファイルで変数を定義および使用する方法について説明します。 変数を使用して、Bicep ファイルの開発を簡略化します。 Bicep ファイル全体で複雑な式を繰り返すのではなく、複雑な式を含む変数を定義します。 次に、Bicep ファイル全体で、必要に応じてその変数を使用します。

Resource Manager は、デプロイ操作を開始する前に変数を解決します。 Bicep ファイルで変数が使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

## <a name="define-variable"></a>変数を定義する

変数を定義するときは、その変数の[データ型](data-types.md)は指定しません。 代わりに、値またはテンプレート式を指定します。 変数の型は解決済みの値から推定されます。 次の例では、変数を文字列に設定します。

```bicep
var stringVar = 'example value'
```

変数を構築する際には、パラメーターまたは別の変数からの値を使用できます。

```bicep
param inputValue string = 'deployment Parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'
```

[Bicep 関数](bicep-functions.md)を使用すると、変数の値を作成できます。 変数を宣言するときに、[参照](bicep-functions-resource.md#reference)関数と[リスト](bicep-functions-resource.md#list)関数が有効です。

次の例では、ストレージ アカウント名に文字列値を作成します。 Bicep 関数をいくつか使用してパラメーター値を取得し、連結して一意の文字列にします。

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

## <a name="use-variable"></a>変数を使用する

次の例は、リソース プロパティに変数を使用する方法を示しています。 変数名 `storageName` を指定することによって変数の値を参照します。

```bicep
param rgLocation string = resourceGroup().location
param storageNamePrefix string = 'STG'

var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}

output stgOutput string = storageName
```

ストレージ アカウント名では小文字を使用する必要があるため、`storageName` 変数は `toLower` 関数を使用して `storageNamePrefix` 値を小文字にします。 `uniqueString` 関数は、リソース グループ ID から一意の値を作成します。 値は文字列に連結されます。

## <a name="example-template"></a>テンプレートの例

次のテンプレートでは、リソースはデプロイされません。 さまざまな型の変数を宣言するいくつかの方法を示します。

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

## <a name="configuration-variables"></a>構成変数

環境を構成するための関連する値を保持する変数を定義できます。 変数は、値を持つオブジェクトとして定義します。 次の例は、**test** と **prod** という 2 つの環境の値を保持するオブジェクトを示しています。デプロイ中に、これらの値のいずれかを渡します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

## <a name="next-steps"></a>次の手順

- 変数に使用できるプロパティの詳細については、「[Bicep ファイルの構造と構文について](file.md)」をご覧ください。

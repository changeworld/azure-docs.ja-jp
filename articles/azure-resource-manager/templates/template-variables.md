---
title: テンプレートにおける変数
description: Azure Resource Manager テンプレート (ARM テンプレート) で変数を定義する方法について説明します。
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874436"
---
# <a name="variables-in-arm-template"></a>ARM テンプレートでの変数

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) で変数を定義および使用する方法について説明します。 変数を使用してテンプレートを簡略化します。 テンプレート全体で複雑な式を繰り返すのではなく、複雑な式を含む変数を定義します。 次に、テンプレート全体で、必要に応じてその変数を参照します。

Resource Manager は、デプロイ操作を開始する前に変数を解決します。 テンプレートで変数が使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

## <a name="define-variable"></a>変数を定義する

変数を定義する際は、[データ型](template-syntax.md#data-types)に解決される値またはテンプレート式を指定します。 変数を構築する際には、パラメーターまたは別の変数からの値を使用できます。

変数宣言では[テンプレート関数](template-functions.md)を使用できますが、[reference](template-functions-resource.md#reference) 関数、または [list](template-functions-resource.md#list) 関数のいずれかは使用できません。 これらの関数は、リソースのランタイム状態を取得します。これらの関数は、変数が解決されるときに、デプロイ前に実行することはできません。

変数を定義する例を以下に示します。 これにより、ストレージ アカウント名の文字列値が作成されます。 テンプレート関数をいくつか使用してパラメーター値を取得し、連結して一意の文字列にします。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>変数を使用する

テンプレートでは、[variables](template-functions-deployment.md#variables) 関数を使用してパラメーターの値を参照します。 次の例は、リソース プロパティに変数を使用する方法を示しています。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>テンプレートの例

次のテンプレートでは、リソースはデプロイされません。 変数を宣言するいくつかの方法を示すのみです。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>構成変数

環境を構成するための関連する値を保持する変数を定義できます。 変数は、値を持つオブジェクトとして定義します。 次の例は、**test** と **prod** という 2 つの環境の値を保持するオブジェクトを示しています。デプロイ中にこれらの値のいずれかを渡します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>次の手順

* 変数に使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」をご覧ください。
* 変数の作成に関する推奨事項については、[ベスト プラクティス - 変数](template-best-practices.md#variables)に関する記事をご覧ください。
* セキュリティ規則をネットワーク セキュリティ グループに割り当てるテンプレートの例については、[ネットワーク セキュリティ規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)に関するページ、および[パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)に関するページを参照してください。

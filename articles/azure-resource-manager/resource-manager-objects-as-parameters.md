---
title: "Azure Resource Manager テンプレート - パラメーターとしてのオブジェクト | Microsoft Docs"
description: "Azure Resource Manager テンプレートの機能を拡張して、オブジェクトをパラメーターとして使用する方法について説明します"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 617c24ea999aef78696ff08add4b9616e3dac589
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Azure Resource Manager テンプレートの機能の拡張パターン ‐ パラメーターとしてのオブジェクト

Azure Resource Manager テンプレートでは、リソースのデプロイをカスタマイズするための値を指定するパラメーターがサポートされています。 この便利な機能により複雑なデプロイが可能になりますが、1 つのテンプレートで使用できるパラメーターは 255 個に制限されています。 大規模なデプロイでリソースの各プロパティにパラメーターを使用すると、パラメーターが不足する可能性があります。

## <a name="create-object-as-parameter"></a>オブジェクトをパラメーターとして作成する

この問題を解決する 1 つの方法として、オブジェクトをパラメーターとして使用します。 それには、テンプレートでパラメーターをオブジェクトとして指定し、そのオブジェクトを値または値の配列として指定するパターンを使います。 そのサブプロパティは、`parameter()` 関数とドット演算子を使用して参照します。 次に例を示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('VNetSettings').name]",
      "location":"[resourceGroup().location]",
      "properties": {
          "addressSpace":{
              "addressPrefixes": [
                  "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ],          
  "outputs": {}
}

```

対応するパラメーター ファイルは、次のようになります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "VNetSettings":{
          "value":{
              "name":"VNet1",
              "addressPrefixes": [
                  { 
                      "name": "firstPrefix",
                      "addressPrefix": "10.0.0.0/22"
                  }
              ],
              "subnets":[
                  {
                      "name": "firstSubnet",
                      "addressPrefix": "10.0.0.0/24"
                  },
                  {
                      "name":"secondSubnet",
                      "addressPrefix":"10.0.1.0/24"
                  }
              ]
          }
      }
  }
}
```

この例では、VNet に対して指定されたすべての値が、1 つのパラメーター `VNetSettings` を基にしています。 特定のリソースの値すべてが 1 つのオブジェクトに保持されるため、このパターンはプロパティ値の管理に適しています。 また、この例では、オブジェクトをパラメーターとして使用しながら、オブジェクトの配列もパラメーターとして使用することができます。 そのオブジェクトは、配列へのインデックスを使用して参照します。

## <a name="use-object-instead-of-multiple-arrays"></a>複数の配列の代わりにオブジェクトを使用する

同様のパターンを使用して、複数のプロパティ値の配列を作成し、各配列を反復処理することで、複数のリソース インスタンスを作成し、値を選択していることがあります。 このパターンは、同じ種類の複数のリソースを作成するときは適切に機能しますが、子リソースの作成にこれを使用すると面倒になる可能性があります。 

この問題の原因は 2 つあります。 まず、Resource Manager は並行して子リソースをデプロイしようとしますが、2 つの子リソースが親を同時に更新すると、デプロイは失敗します。 

さらに、各プロパティ値の配列が並行して反復処理されている場合、各配列の構造が同じでないと、エラーが発生します。 たとえば、次のプロパティ配列を考えてみましょう。

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two"
    ]
}
```

こうした値をコピー ループ内のプロパティに割り当てるときに使用する一般的なパターンでは、`variables()` 関数を使用してプロパティにアクセスし、`copyIndex()` を、配列へのインデックスとして使用します。 次に例を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```
コピー ループの `count` は `firstProperty` 配列のプロパティの数に基づいていますが、 `secondProperty` 配列のプロパティの数は同じでありません。 `secondProperty` 配列の長さが同じでないため、このテンプレートの検証は失敗します。

しかし、すべてのプロパティを 1 つのオブジェクトに含めると、値が不足していることを、はるかに確認しやすくなります。 For example:

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

## <a name="try-the-template"></a>テンプレートを試行する

このテンプレートを試す場合は、次の手順に従います。

1.    Azure Portal に移動し、[+] アイコンを選択して、"テンプレートのデプロイ" のリソースの種類を検索します。 検索結果に見つかった場合は、選択します。
2.    [テンプレートのデプロイ] ページが表示されたら、**[作成]** を選択します。 このボタンにより、[カスタム デプロイ] ブレードが開きます。
3.    **[テンプレートの編集]** を選択します。
4.    右側のペインで空のテンプレートを削除します。
5.    サンプル テンプレートをコピーして、右側のウィンドウに貼り付けます。
6.    **[保存]** を選択します。
7.    [カスタム デプロイ] ウィンドウに戻り、**[パラメーターの編集]** を選択します。
8.  [パラメーターの編集] ブレードで、既存のテンプレートを削除します。
9.  上記のサンプル パラメーター テンプレートをコピーして、貼り付けます。
10. **[保存]** を選択します。これにより、[カスタム デプロイ] ブレードに戻ります。
11. [カスタム デプロイ] ブレードで、サブスクリプションを選択し、新しいリソース グループを作成するか、既存のリソース グループを使用して、場所を選択します。 使用条件を確認し、[同意する] チェックボックスをオンにします。
12.    **[購入]** を選択します。

## <a name="next-steps"></a>次のステップ

必要なパラメーター数が、デプロイあたりの最大許容数である 255 個を超える場合、このパターンを使って、より少ないパラメーターをテンプレートで指定します。 このパターンを使用して、リソースのプロパティをより簡単に管理し、シーケンシャル ループ パターンによる競合を発生させずにデプロイすることもできます。

* `parameter()` 関数、および配列の使用の概要については、「[Azure Resource Manager のテンプレートの関数](resource-group-template-functions.md)」を参照してください。
* このパターンは、[テンプレート構成ブロックのプロジェクト](https://github.com/mspnp/template-building-blocks)と [Azure 参照アーキテクチャ](/azure/architecture/reference-architectures/)で実装することもできます。

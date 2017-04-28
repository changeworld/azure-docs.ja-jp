---
title: "Azure テンプレート内の連続ループ | Microsoft Docs"
description: "リソース種類の複数のインスタンスのデプロイ時に、Azure Resource Manager テンプレートの機能を拡張して、連続ループを実装する方法について説明します。"
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/19/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>Azure Resource Manager テンプレート - 連続ループの機能を拡張するためのパターン

Azure Resource Manager テンプレートでは、コピー ループによる同様のリソースのグループのデプロイをサポートします。 リソース オブジェクトのコピー ループは、リソースの一意の名前を生成するために使用される文字列の配列を反復処理するのに使用できます。 リソース オブジェクトのコピー ループは、リソースを記述する変数の配列を反復処理するのに使用することもできます。

これらのパターンはすべて適切に機能しますが、グループの各メンバーの間の依存関係がない場合にのみ機能します。 反復処理のループ中、Resource Manager は、リソースを並列にデプロイしようとします。 最初のリソースが 2 番目のリソースに依存する場合、Resource Manager が最初のリソースをデプロイする前に 2 番目のリソースをデプロイすると、デプロイが失敗する場合があります。

実際の問題は、Resource Manager では、反復処理のループ内の `dependsOn` を現在サポートしていないことです。 ただし、この機能は、既存の Resource Manager 機能と、創造的なリソースの名前付けを使用して実装できます。 

## <a name="sequential-looping-pattern"></a>連続ループ パターン

パターンは次のようになります。最初のリソースは、名プレフィックスと `0` を連結したものを使用して (または、ループの最初のインデックスがどのようなものであっても) 名前が付けられます。 2 番目のリソースには、コピー ループが含まれています。コピー ループでは、次のリソース名は、名のプレフィックスと `copyIndex(1)` 関数の結果 (現在の `copyIndex()` に 1 を加算) を連結したものです。 2 番目のリソースには、名のプレフィックスと `copyIndex()` 関数の結果を連結したものを参照する `dependsOn` 要素も含まれます。 このアプローチでは、次のリソースから前のリソースに戻る `dependsOn` 関係が作成されます。 Resource Manager は、前のリソースがデプロイされるまで、次のリソースをデプロイするのを待機します。

次のテンプレートでは、このパターンを示します。 Microsoft.Resources/デプロイ リソースの種類は、実際には何もデプロイしない入れ子になったテンプレートだけです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
        }
      }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex(1))]",
      "dependsOn": [
        "[concat('loop-', copyIndex())]"
      ],
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
このテンプレートでは、最初のリソース オブジェクトの名前は `loop-0` になります。 次に、2 番目のリソース オブジェクトで、次のリソースの名前は、単語 `loop-` と `copyIndex(1)` 関数: `loop-1` の結果を連結したものになります。 `dependsOn` 要素は、前のリソースを参照しますが、これは、前のリソースが単語 `loop-` と `copyIndex()` 関数: `loop-0` の結果を連結したものになるためです。 2 番目のリソース オブジェクトのパターンは、`count` に達し、`loop-3` を `dependsOn` する `loop-4` という名前のリソースで終了するまで繰り返されます。 `count` は、`numberToDeploy` パラメーターから `1` を減算してから、ゼロから始まる数を正しく保持する変数です。

## <a name="try-the-template"></a>テンプレートを試行する

このテンプレートを実験する場合は、次の手順に従います。

1.    Azure Portal に移動し、[+] アイコンを選択して、"テンプレートのデプロイ" のリソースの種類を検索します。 検索結果に見つかった場合は、選択します。
2.    [テンプレートのデプロイ] ページが表示されたら、[作成] を選択します。 [カスタム デプロイ] ブレードに移動し、テンプレートにリソースがないことを確認します。
3.    [編集] アイコンを選択します。
4.    右側のペインで空のテンプレートを削除します。
5.    前述のサンプル テンプレートをコピーして、右側のペインに貼り付けます。
6.    [保存] を選択します。
7.    [カスタム テンプレート] ペインに戻りますが、ここでは、ドロップダウン ボックスがいくつか表示されます。 サブスクリプションを選択し、新しいリソース グループを作成するか、既存のリソース グループを使用して、場所を選択します。 使用条件を確認し、[同意する] をクリックします。
8.    [購入] をクリックします。

リソースが順番にデプロイされていることを確認するには、[リソース グループ] を選択し、前に選択したリソース グループを選択します。 [リソース グループ] ブレードで [デプロイ] を選択すると、対応するタイムスタンプの順番で、デプロイされたリソースが表示されます。

![Azure Resource Manager での連続ループ テンプレートのデプロイ](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>次のステップ

入れ子になったテンプレートに、リソースを追加して、テンプレート内のこのパターンを使用します。 `Microsoft.Resources/deployments` リソースのテンプレート要素に直接作成するか、`templateLink` 要素を使用してリンクできます。 例の中のリソースの種類は、入れ子になったテンプレートですが、任意のリソースの種類をデプロイできます。 唯一の例外は、反復処理のループ内から子リソースを参照できないことです。

* リソースの複数のインスタンスを作成するための概要は、「[Azure Resource Manager テンプレート内における複数のリソースのインスタンスのデプロイ](resource-group-create-multiple.md)」を参照してください。
* このパターンは、[テンプレート構成ブロックのプロジェクト](https://github.com/mspnp/template-building-blocks)と [Azure 参照アーキテクチャ](/azure/architecture/reference-architectures/)で実装することもできます。

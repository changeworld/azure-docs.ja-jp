---
title: Azure ブループリントでのデプロイ シーケンスについて
description: ブループリントが経過するライフサイクルと各ステージの詳細について説明します。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955454"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Azure ブループリントでのデプロイ シーケンスについて

Azure ブループリントでは、ブループリントの割り当てを処理するときに、**シーケンス順序**を使用してリソースの作成順序を決定します。 この記事では、既定で使用されるシーケンス順序、順序をカスタマイズする方法、カスタマイズされた順序が処理される方法について説明します。

JSON の例では、次の変数を独自の値で置き換える必要があります。

- `{YourMG}` - 実際の管理グループの名前に置き換えます

## <a name="default-sequencing-order"></a>既定のシーケンス順序

ブループリントに成果物をデプロイする順序を指定するディレクティブが含まれていない場合、またはそのディレクティブが null の場合は、次の順序が使用されます。

- サブスクリプション レベルの**ロールの割り当て**成果物が、成果物の名前で並べ替えられます
- サブスクリプション レベルの**ポリシーの割り当て**成果物が、成果物の名前で並べ替えられます
- サブスクリプション レベルの **Azure Resource Manager テンプレート**成果物が、成果物の名前で並べ替えられます
- **リソース グループ**成果物 (子成果物を含む) が、プレースホルダーの名前で並べ替えられます

各**リソース グループ**内の成果物が処理されるときには、そのリソース グループ内に作成される成果物に対して次のようなシーケンス順序が適用されます。

- リソース グループの子の**ロールの割り当て**成果物が、成果物の名前で並べ替えられます
- リソース グループの子の**ポリシー割り当て**成果物が、成果物の名前で並べ替えられます
- リソース グループの子の**Azure Resource Manager テンプレート**成果物が、成果物の名前で並べ替えられます

## <a name="customizing-the-sequencing-order"></a>シーケンス順序のカスタマイズ

大規模なブループリントを作成するときは、リソースを、別のリソースとの関係で特定の順序で作成することが必要になる場合があります。 これの最も一般的な使用パターンは、ブループリントに複数の Azure Resource Manager テンプレートが含まれている場合です。 ブループリントでは、シーケンス順序を定義することで、これを処理します。

これは、JSON 内で `dependsOn` プロパティを定義することで実現します。 (リソース グループの) ブループリントと成果物オブジェクトでのみ、このプロパティがサポートされます。 `dependsOn` は、特定の成果物が作成される前に作成する必要がある成果物の名前で構成される文字列配列です。

### <a name="example---blueprint-with-ordered-resource-group"></a>例 - リソース グループの順序で並べたブループリント

このブループリントの例には、`dependsOn` の値を宣言することでカスタムのシーケンス順序を定義されたリソース グループと、標準のリソース グループの両方が含まれています。 この場合、**assignPolicyTags** という名前の成果物が、**ordered-rg** リソース グループの前に処理されます。
**standard-rg** は、既定のシーケンス順序ごとに処理されます。

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>例 - カスタムの順序で並べられた成果物

これは、Azure Resource Manager テンプレートに依存するポリシー成果物の例です。 既定の順序では、Azure Resource Manager テンプレートの前に、ポリシー成果物が作成されます。 この例では、ポリシー成果物は Azure Resource Manager テンプレートが作成されるまで待機します。

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

## <a name="processing-the-customized-sequence"></a>カスタマイズされたシーケンスの処理

作成プロセスでは、トポロジカル ソートを使用して、ブループリントとその成果物の依存関係グラフが作成されます。 これにより、リソース グループと成果物の複数レベルの依存関係がサポートされます。

依存関係がブループリント、または既定の順序を変更しない成果物によって宣言されている場合、シーケンス順序に変更は加えられません。 たとえば、サブスクリプション レベルのポリシーに依存しているリソース グループか、または 'standard rg' 子ポリシーの割り当てがリソース グループ 'standard rg' 子ロールの割り当てに依存しているリソース グループがそれに当たります。 どちらの場合も、`dependsOn` によって既定のシーケンス順序が変更されることはなく、何の変更も加えられません。

## <a name="next-steps"></a>次の手順

- [ブループリントのライフサイクル](lifecycle.md)について
- [静的および動的パラメーター](parameters.md)の使い方
- [ブループリントによるリソース ロック](resource-locking.md)の使い方
- [既存の割り当てを更新する](../how-to/update-existing-assignments.md)方法
- ブループリントの割り当て中に発生した問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する
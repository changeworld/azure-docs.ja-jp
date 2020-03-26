---
title: パターン:ポリシー定義の効果
description: この Azure Policy パターンでは、ポリシー定義のさまざまな効果を使用する方法の例を示します。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: b86a24bc0af6c9bdd7b29bb0a931d6c78865218b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170460"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy パターン: 効果

Azure Policy には、準拠していないリソースにサービスがどのように反応するかを決定する[効果](../concepts/effects.md)が多数あります。 ポリシー定義にプロパティを追加する必要のない単純な効果もあれば、複数のプロパティを必要とする効果もあります。

## <a name="sample-1-simple-effect"></a>サンプル 1:単純な効果

このポリシー定義では、パラメーター **tagName** に定義されているタグが、評価対象のリソースに存在するかどうかを確認します。 タグがまだ存在しない場合は、[modify](../concepts/effects.md#modify) 効果がトリガーされ、パラメーター **tagValue** の値を持つタグが追加されます。

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>サンプル 1:説明

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="30-34":::

**modify** 効果には、**roleDefinitionIds** および **operations** を定義する **policyRule.then.details** ブロックが必要です。 これらのパラメーターによって、タグを追加してリソースを修復するために必要なロールと、実行するするべき**変更**操作が Azure Policy に通知されます。 この例では、**操作**が "_追加_" され、パラメーターを使用してタグとその値が設定されます。

## <a name="sample-2-complex-effect"></a>サンプル 2:複雑な効果

このポリシー定義では、パラメーター **publisher** および **type** で定義されている拡張機能が存在しない場合に、各仮想マシンの監査を行います。 [auditIfNotExists](../concepts/effects.md#auditifnotexists) を使用して、仮想マシンに関連するリソースをチェックし、定義したパラメーターに一致するインスタンスが存在するかどうかを確認します。 この例では、**extensions** の種類をチェックします。

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>サンプル 2:説明

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

**auditIfNotExists** 効果には、検索する **type** および **existenceCondition** の両方を定義する **policyRule.then.details** が必要です。 **existenceCondition** は、[論理演算子](../concepts/definition-structure.md#logical-operators)などのポリシー言語要素を使用して、一致する関連リソースが存在するかどうかを判断します。 この例では、各[別名](../concepts/definition-structure.md#aliases)に対してチェックされる値はパラメーターで定義されています。

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
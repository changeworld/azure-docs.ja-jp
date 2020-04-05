---
title: パターン:ポリシー定義の論理演算子
description: この Azure Policy のパターンでは、ポリシー定義の論理演算子を使用する方法の例を示します。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170240"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy パターン: 論理演算子

ポリシー定義には、条件付きステートメントをいくつか含めることができます。 各ステートメントが true である必要がある場合や、一部のステートメントのみが true である必要がある場合があります。 これらのニーズに対応するために、言語には、**not**、**allOf**、**anyOf** の[論理演算子](../concepts/definition-structure.md#logical-operators)があります。 これらは省略可能であり、入れ子にして複雑なシナリオを作成することもできます。

## <a name="sample-1-one-logical-operator"></a>サンプル 1:1 つの論理演算子

このポリシー定義は、CosmosDB アカウントを評価して、自動フェールオーバーと複数の書き込み場所が構成されているかどうかを確認します。 そうでない場合は、[audit](../concepts/effects.md#audit) がトリガーされ、非対応のリソースが作成または更新されるときにログ エントリが作成されます。

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>サンプル 1:説明

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**policyRule.if** ブロックで、1 つの **allOf** を使用して、3 つの条件すべてが true であることを確認します。
これらのすべての条件が true と評価された場合にのみ、**audit** 効果がトリガーされます。

## <a name="sample-2-multiple-logical-operators"></a>サンプル 2:複数の論理演算子

このポリシー定義は、リソースの名前付けパターンを評価します。 リソースが一致しない場合は、[拒否されます](../concepts/effects.md#deny)。

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>サンプル 2:説明

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

この **policyRule.if** ブロックにも 1 つの **allOf** が含まれていますが、各条件は **not** 論理演算子でラップされています。 **not** 論理演算子内で条件が最初に評価され、次に **not** が評価されて、句全体が true であるか、false であるかどうかが判定されます。 両方の **not** 論理演算子が true と評価されると、ポリシー効果がトリガーされます。

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
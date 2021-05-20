---
title: 'パターン: ポリシー定義の count 演算子'
description: この Azure Policy パターンでは、ポリシー定義内で count 演算子を使用する方法の例を示します。
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: 4b4a2615491632caf1a7abddd1b6ace96ccc608d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108752527"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy パターン: count 演算子

[count](../concepts/definition-structure.md#count) 演算子は、\[\*\] 別名のメンバーを評価します。

## <a name="sample-policy-definition"></a>ポリシー定義の例

このポリシー定義では、受信リモート デスクトップ プロトコル (RDP) トラフィックを許可するように構成されたネットワーク セキュリティ グループを[監査](../concepts/effects.md#audit)します。

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>説明

**count** 演算子のコア コンポーネントは _field_、_where_、および条件です。 以下のスニペットでは、それらが強調表示されています。

- _field_ は、メンバーを評価する [別名](../concepts/definition-structure.md#aliases)を count に指示します。 ここでは、ネットワーク セキュリティ グループの **securityRules\[\*\]** 別名 "_配列_" を確認します。
- _where_ は、ポリシー言語を使用して、条件を満たす "_配列_" メンバーを定義します。 この例では、**allOf** 論理演算子によって、別名の "_配列_" プロパティに対する 3 つの異なる条件での評価 (_direction_、_access_、_destinationPortRange_) がグループ化されています。
- この例の count の条件は、**greater** です。 別名 "_配列_" の 1 つまたは複数のメンバーが _where_ 句と一致する場合、count は true に評価されます。

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。

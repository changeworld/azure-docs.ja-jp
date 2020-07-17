---
title: リソースのタグ付けのポリシー
description: タグの準拠を保証するために割り当てることができる Azure のポリシーについて説明します。
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147862"
---
# <a name="assign-policies-for-tag-compliance"></a>タグの準拠のためのポリシーを割り当てる

タグ付けのルールと規則を強制するには、[Azure Policy](../../governance/policy/overview.md) を使用します。 ポリシーを作成することで、組織で必要とされるタグが存在しないサブスクリプションにリソースがデプロイされるというシナリオを回避します。 タグの適用や非準拠リソースの検索を手動で行う代わりに、デプロイ時に必要なタグを自動的に適用するポリシーを作成します。 また、新しい [Modify](../../governance/policy/concepts/effects.md#modify) 効果と[修復タスク](../../governance/policy/how-to/remediate-resources.md)を使用して、既存のリソースにタグを適用することもできるようになりました。 次のセクションでは、タグのポリシーの例を示します。

## <a name="policies"></a>ポリシー

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>次のステップ

* リソースにタグを付ける方法については、「[タグを使用した Azure リソースの整理](tag-resources.md)」を参照してください。
* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。

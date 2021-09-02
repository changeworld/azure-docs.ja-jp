---
title: リソースにタグを付けするためのポリシー定義
description: タグの準拠を保証するために割り当てることができる Azure Policy の定義について説明します。
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: f7af799423b0aceac66dfa97e92c92d25c3916db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456259"
---
# <a name="assign-policy-definitions-for-tag-compliance"></a>タグの準拠のためのポリシー定義を割り当てる

タグ付けのルールと規則を強制するには、[Azure Policy](../../governance/policy/overview.md) を使用します。 ポリシーを作成することで、組織で必要とされるタグが存在しないサブスクリプションにリソースがデプロイされるというシナリオを回避します。 タグの適用や非準拠リソースの検索を手動で行う代わりに、デプロイ時に必要なタグを自動的に適用するポリシーを作成します。 また、新しい [Modify](../../governance/policy/concepts/effects.md#modify) 効果と[修復タスク](../../governance/policy/how-to/remediate-resources.md)を使用して、既存のリソースにタグを適用することもできるようになりました。 次のセクションでは、タグのポリシー定義の例を示します。

## <a name="policy-definitions"></a>ポリシーの定義

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>次のステップ

* リソースにタグを付ける方法については、「[タグを使用した Azure リソースの整理](tag-resources.md)」を参照してください。
* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。

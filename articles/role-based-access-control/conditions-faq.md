---
title: Azure ロール割り当て条件のよくあるご質問 (プレビュー)
description: Azure ロールの割り当て条件に関してよく寄せられる質問 (プレビュー)
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/13/2021
ms.author: rolyon
ms.openlocfilehash: df1b585fb0f84dafe26c954386f487e3ceff2bc7
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2021
ms.locfileid: "109847330"
---
# <a name="faq-for-azure-role-assignment-conditions-preview"></a>Azure ロール割り当て条件のよくあるご質問 (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Azure portal の条件ビルダーで、ストレージ コンテナー名または BLOB パスを選択できますか。**

条件には、ストレージ コンテナー名、BLOB パス、タグ名、または値を書き込む必要があります。 属性値にはピッキング エクスペリエンスがありません。

**式をグループ化できますか。**

対象となるアクションに対して 3 つ以上の式を追加する場合は、コード エディター、Azure PowerShell、または Azure CLI でこれらの式の論理的なグループを定義する必要があります。 `a AND b OR c` の論理的なグループには、`(a AND b) OR c` または `a AND (b OR c )` を指定できます。

**管理グループ スコープでロールの割り当て条件を追加できますか。**
  
Azure portal では、管理グループ スコープで条件を編集または表示することはできません。 **条件** 列は、管理グループ スコープに対しては表示されません。 Azure PowerShell と Azure CLI では、管理グループ スコープで条件を追加することができます。

**プレビュー段階で、Azure リソースの Privileged Identity Management (PIM) を介して条件はサポートされていますか。**

はい。 詳細については、「[Privileged Identity Management で Azure リソース ロールを割り当てる](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)」を参照してください。

**従来の管理者には条件がサポートされていますか。**

いいえ。 

**カスタム ロールの割り当てに条件を追加できますか。**

はい。カスタム ロールに[条件をサポートするアクション](conditions-format.md#actions)が含まれている場合に限ります。
 
**条件により、ストレージ BLOB へのアクセスの待機時間が増加しますか。**

いいえ。ベンチマーク テストに基づくと、条件によってユーザーが認識できる待機時間が追加されることはありません。

**条件をサポートするために、ロールの割り当てスキーマにどのような新しいプロパティが導入されましたか。**

新しい条件のプロパティを次に示します。

- `condition`: ロール定義と属性から 1 つ以上のアクションを使用して構築された条件文。
- `conditionVersion`: 条件バージョン番号。 既定値は 2.0 で、パブリックにサポートされている唯一のバージョンです。

ロールの割り当ての新しい説明プロパティも用意されています。

- `description`: 条件の説明に使用できるロールの割り当ての説明。

**条件は、ロールの割り当て全体と特定のアクションのどちらに適用されますか。**

条件は、特定の対象となるアクションにのみ適用されます。

**条件の上限は何ですか。**

条件の長さは最大 8 KB です。

**説明の上限は何ですか。**

説明の長さは最大 2 KB です。

**条件の有無にかかわらず、セキュリティ プリンシパル、ロール定義、スコープの同じタプルを使用してロール割り当てを作成することは可能ですか。**

いいえ。このロールの割り当てを作成しようとすると、エラーが表示されます。

**ロールの割り当ての条件は、明示的な Deny 効果を提供していますか。**

いいえ。ロールの割り当ての条件は、明示的な Deny 効果を提供しません。 ロールの割り当ての条件により、ロールの割り当てで付与されたアクセスがフィルター処理され、アクセスが許可されなくなる可能性があります。 明示的な Deny 効果は、拒否の割り当ての一部です。

## <a name="next-steps"></a>次のステップ

- [Azure ロールの割り当て条件の形式と構文 (プレビュー)](conditions-format.md)
- [Azure ロール割り当て条件のトラブルシューティング (プレビュー)](conditions-troubleshoot.md)

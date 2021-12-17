---
title: Azure ロール割り当て条件のトラブルシューティング (プレビュー)
description: Azure ロール割り当て条件のトラブルシューティング (プレビュー)
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: eddebb6742a50ce729d436fc19ce4ff32491f75f
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489756"
---
# <a name="troubleshoot-azure-role-assignment-conditions-preview"></a>Azure ロール割り当て条件のトラブルシューティング (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロール割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="symptom---condition-is-not-enforced"></a>現象 - 条件が適用されない

**原因 1**

セキュリティ プリンシパルに、同じかそれ以上のスコープの 1 つ以上のロールが割り当てられています。

**解決策 1**

セキュリティ プリンシパルに、条件適用の阻害につながる、同じデータ アクションへのアクセスを許可する複数のロール割り当て (条件の有無は問わず) がないことを確認します。 評価ロジックの詳細については、「[ユーザーがリソースへのアクセス権を持っているどうかを Azure RBAC が特定する方法](overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)」を参照してください。

**原因 2**

ロールの割り当てに、アクセス許可を付与するアクションが複数存在し、指定した条件がすべてのアクションを対象としていません。 たとえば、`/blobs/write` または `/blobs/add/action` データ アクションがある場合は、BLOB を作成できます。 ロールの割り当てに両方のデータ アクションが含まれており、その内 1 つだけが条件に含まれる場合、ロールの割り当てによって、BLOB を作成して条件をバイパスする権限が付与されます。

**解決策 2**

ロールの割り当てにアクセス許可を付与するアクションが複数ある場合は、関連するすべてのアクションを対象にするようにしてください。

**原因 3**

ロールの割り当てに条件を追加すると、条件が適用されるまで最大 5 分かかる場合があります。 条件を追加すると、リソース プロバイダー (Microsoft.Storage など) に更新が通知されます。 リソース プロバイダーでは、ロール割り当てが最新になるよう、ローカル キャッシュがすぐに更新されます。 このプロセスは 1 ～ 2 分で完了しますが、最大で 5 分かかることがあります。

**解決策 3**

5 分間待ってから、条件をもう一度テストします。

## <a name="symptom---condition-is-not-valid-error-when-adding-a-condition"></a>現象 - 条件を追加すると、条件が有効ではないことを示すエラーが発生する

条件を指定したロールの割り当てを追加しようとすると、次のようなエラーが表示されます。

`The given role assignment condition is invalid.`

**原因**

条件が正しく書式設定されていません。 

**ソリューション**

[条件形式または構文](conditions-format.md)の問題を修正します。 あるいは、[Azure portal のビジュアル エディター](conditions-role-assignments-portal.md)を使用して条件を追加します。

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-powershell"></a>現象 - Azure PowerShell を使用して条件を指定しようとすると、リソース属性が有効ではないことを示すエラーが発生する

Azure PowerShell を使用して条件を指定したロールの割り当てを追加しようとすると、次のようなエラーが表示されます。

```
New-AzRoleAssignment : Resource attribute
Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**原因**

条件にドル記号 ($) が含まれている場合は、その前にバッククォート (\`) を付ける必要があります。

**ソリューション**

ドル記号の前にバッククォート (\`) を追加します。 次に例を示します。 PowerShell の引用符の規則の詳細については、[引用符の規則](/powershell/module/microsoft.powershell.core/about/about_quoting_rules)に関するページを参照してください。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-cli"></a>現象 - Azure CLI を使用して条件を指定しようとすると、リソース属性が有効ではないことを示すエラーが発生する

Azure CLI を使用して条件を指定したロールの割り当てを追加しようとすると、次のようなエラーが表示されます。

```
Resource attribute Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**原因**

条件にドル記号 ($) が含まれている場合は、その前に円記号 (\\) を付ける必要があります。

**ソリューション**

ドル記号の前に円記号 (\\) を追加します。 次に例を示します。 Bash の引用符の規則の詳細については、[二重引用符](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html)に関するページを参照してください。

```azurecli
condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
```

## <a name="symptom---error-when-assigning-a-condition-string-to-a-variable-in-bash"></a>現象 - Bash の変数に条件文字列を割り当てるときにエラーが発生する

Bash の変数に条件文字列を割り当てようとすると、`bash: !: event not found` というメッセージが表示されます。

**原因**

Bash では、履歴展開が有効になっている場合、感嘆符 (!) が原因で `bash: !: event not found` というメッセージが表示されることがあります。

**ソリューション**

コマンド `set +H` を使用して、履歴展開を無効にします。 履歴展開を再び有効にするには、`set -H` を使用します。

## <a name="symptom---unrecognized-arguments-error-when-adding-a-condition-using-azure-cli"></a>現象 - Azure CLI を使用して条件を追加すると引数が認識できないことを示すエラーが発生する

Azure CLI を使用して条件を指定したロールの割り当てを追加しようとすると、次のようなエラーが表示されます。

`az: error: unrecognized arguments: --description {description} --condition {condition} --condition-version 2.0`

**原因**

ロールの割り当て条件パラメーターがサポートされていない、以前のバージョンの Azure CLI を使用している可能性があります。

**ソリューション**

Azure CLI の最新バージョン (2.18 以降) に更新します。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="symptom---condition-not-recognized-in-visual-editor"></a>現象 - ビジュアル エディターで条件が認識されない

コード エディターを使用した後にビジュアル エディターに切り替えると、次のようなメッセージが表示されます。

`The current expression cannot be recognized. Switch to the code editor to edit the expression or delete the expression and add a new one.`

**原因**

ビジュアル エディターでは解析できない条件の更新が行われました。

**ソリューション**

[条件形式または構文](conditions-format.md)の問題を修正します。 あるいは、条件を削除して再試行することもできます。

## <a name="symptom---error-when-copying-and-pasting-a-condition"></a>現象 - 条件をコピーして貼り付けるときにエラーが発生する

**原因**

ドキュメントから条件をコピーすると、エラーを引き起こす特殊文字が含まれる場合があります。 一部のエディター (Microsoft Word など) では、表示されないテキストの書式設定を行う際にコントロール文字が追加されます。

**ソリューション**

条件が正しいことがわかっている場合は、すべての空白と改行を削除してから、関連するスペースを再び追加します。

## <a name="next-steps"></a>次のステップ

- [Azure ロール割り当て条件の形式と構文 (プレビュー)](conditions-format.md)
- [Azure ロール割り当て条件のよくあるご質問 (プレビュー)](conditions-faq.md)
- [Azure ロール割り当て条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)

---
title: Azure ロールの割り当て条件の形式と構文 (プレビュー) - Azure RBAC
description: Azure 属性ベースのアクセス制御 (Azure ABAC) の Azure ロールの割り当て条件の形式と構文の概要について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: b271da3cf8e591df8557133abcff248a737645e5
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489816"
---
# <a name="azure-role-assignment-condition-format-and-syntax-preview"></a>Azure ロールの割り当て条件の形式と構文 (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

条件は、必要に応じてロールの割り当てに追加して、よりきめ細かなアクセス制御を提供できるようにする追加のチェックです。 たとえば、オブジェクトを読み取るために、そのオブジェクトに特定のタグが必要であるという条件を追加できます。 この記事では、ロールの割り当て条件の形式と構文について説明します。

## <a name="condition-format"></a>条件の形式

ロールの割り当て条件をよりよく理解するには、形式を確認することが役立ちます。

### <a name="simple-condition"></a>単純な条件

最も基本的な条件は、対象となるアクションと式で構成されます。 アクションは、あるリソースの種類に対してユーザーが実行できる操作です。 式は、true または false に評価されるステートメントであり、アクションの実行が許可されるかどうかを決定します。

単純な条件の形式を次に示します。

![1 つのアクションと 1 つの式を含む単純な条件の形式。](./media/conditions-format/format-simple.png)

次の条件には、"BLOB を読み取る" というアクションがあります。 式では、コンテナー名が blobs-example-container であるかどうかがチェックされます。

![BLOB 読み取りアクションとコンテナー名の式を含む単純な条件の例。](./media/conditions-format/format-simple-example.png)

![特定のコンテナー名を持つ BLOB に対する読み取りアクセスを示す図。](./media/conditions-format/format-simple-example-diagram.png)

### <a name="how-a-condition-is-evaluated"></a>条件の評価方法

ユーザーがロールの割り当て内の、`<action>` でないアクションを実行しようとする場合、`!(ActionMatches)` は true に評価され、全体的な条件は true に評価されて、アクションの実行が許可されます。

ユーザーがロールの割り当て内の `<action>` を実行しようとする場合、`!(ActionMatches)` は false に評価され、それにより、式が評価されます。 式が true に評価された場合、全体的な条件は true に評価され、`<action>` の実行が許可されます。 それ以外の場合は、`<action>` の実行は許可されません。

次の擬似コードは、この条件を読み取ることができるもう 1 つの方法を示しています。

```
if a user tries to perform an action in the role assignment that does not match <action>
{
    Allow action to be performed
}
else
{
    if <attribute> <operator> <value> is true
    {
        Allow <action> to be performed
    }
    else
    {
        Do not allow <action> to be performed
    }
}
```

### <a name="suboperations"></a>副操作

一部のアクションには副操作があります。 たとえば、"BLOB の読み取り" データ アクションには、"タグ条件を使用して BLOB からコンテンツを読み取る" という副操作があります。 副操作を含む条件の形式は次のとおりです。

![副操作を含むアクションの形式。](./media/conditions-format/format-suboperation.png)

### <a name="multiple-actions"></a>複数のアクション

条件には、その条件が true の場合に許可する複数のアクションを含めることができます。 1 つの条件に対して複数のアクションを選択すると、選択したアクション全体で属性が使用できる必要があるため、条件のために選択する属性の数は少なくなる可能性があります。

![条件が true の場合に許可する複数のアクションの形式。](./media/conditions-format/format-multiple-actions.png)

### <a name="multiple-expressions"></a>複数の式

条件には複数の式を含めることができます。 演算子に応じて、属性を複数の値に対してチェックできます。

![ブール演算子と複数の値を使用した複数の式の形式。](./media/conditions-format/format-multiple-expressions.png)

### <a name="multiple-conditions"></a>複数の条件

条件を組み合わせて複数のアクションを対象にすることもできます。

![ブール演算子を使用した複数の条件の形式。](./media/conditions-format/format-multiple-conditions.png)

## <a name="condition-syntax"></a>条件の構文

ロールの割り当て条件の構文を次に示します。

```
(
    (
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
(
    (
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        !(ActionMatches{'<action>'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'<subOperation>'})
        AND
        ...
    )
    OR
    (
        <attribute> <operator> {<value, <value>, ...}
        AND | OR
        <attribute> <operator> {<value>, <value>, ...}
        AND | OR
        ...
    )
)
AND
...
```

## <a name="actions"></a>アクション

現在、ストレージ BLOB データ アクションを含む組み込みまたはカスタムのロールの割り当てに条件を追加できます。 これらには、以下の組み込みロールが含まれます。

- [ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)
- [ストレージ BLOB データ所有者](built-in-roles.md#storage-blob-data-owner)
- [ストレージ BLOB データ閲覧者](built-in-roles.md#storage-blob-data-reader)

条件で使用できるストレージ BLOB アクションの一覧については、「[Azure Storage での Azure ロール割り当て条件のアクションと属性 (プレビュー)](../storage/common/storage-auth-abac-attributes.md)」を参照してください

## <a name="attributes"></a>属性

選択したアクションによっては、属性が異なる場所にある場合があります。 1 つの条件に対して複数のアクションを選択すると、選択したアクション全体で属性が使用できる必要があるため、条件のために選択する属性の数は少なくなる可能性があります。 属性を指定するには、ソースをプレフィックスとして含める必要があります。

> [!div class="mx-tableFixed"]
> | 属性ソース | 説明 | コード |
> | --- | --- | --- |
> | リソース | 属性がリソースにあることを示します (コンテナー名など)。 | `@Resource` |
> | Request | 属性がアクション要求の一部であることを示します (BLOB インデックス タグの設定など)。 | `@Request` |

条件で使用できるストレージ BLOB 属性の一覧については、「[Azure Storage での Azure ロール割り当て条件のアクションと属性 (プレビュー)](../storage/common/storage-auth-abac-attributes.md)」を参照してください

## <a name="operators"></a>演算子

次の表に、条件を構成するために使用できる演算子を一覧で示します。

| カテゴリ | 演算子 | 説明 |
| --- | --- | --- |
| 論理比較 |`AND`<br/>`&&` | AND 演算子。 |
|  | `OR`<br/>`||` | OR 演算子。 |
|  | `NOT`<br/>`!` | NOT または否定演算子。 |
| 文字列の比較 | `StringEquals`<br/>`StringEqualsIgnoreCase` | 大文字と小文字を区別する (または大文字と小文字を区別しない) 一致。 値は文字列と正確に一致している必要があります。 |
|  | `StringNotEquals`<br/>`StringNotEqualsIgnoreCase` | `StringEquals` (または `StringEqualsIgnoreCase`) 演算子の否定 |
|  | `StringStartsWith`<br/>`StringStartsWithIgnoreCase` | 大文字と小文字を区別する (または大文字と小文字を区別しない) 一致。 値は文字列で始まります。 |
|  | `StringNotStartsWith`<br/>`StringNotStartsWithIgnoreCase` | `StringStartsWith` (または `StringStartsWithIgnoreCase`) 演算子の否定 |
|  | `StringLike`<br/>`StringLikeIgnoreCase` | 大文字と小文字を区別する (または大文字と小文字を区別しない) 一致。 値では、文字列内の任意の場所に複数文字の一致ワイルドカード (`*`) または 1 文字の一致ワイルドカード (`?`) を含めることができ ます。 必要であれば、円記号 `\*` と `\?` を追加してこれらの文字をエスケープできます。 |
|  | `StringNotLike`<br/>`StringNotLikeIgnoreCase` | `StringLike` (または `StringLikeIgnoreCase`) 演算子の否定 |
| 数値比較 | `NumericEquals`<br/>`NumericNotEquals`<br/>`NumericLessThan`<br/>`NumericLessThanEquals`<br/>`NumericGreaterThan`<br/>`NumericGreaterThanEquals` | 現在、整数のみがサポートされています。 |
| 上位の関数 | `ActionMatches` | Action[ID] の値が指定したアクション パターンと一致するかどうかをチェックします。 この演算子は、アクションをアクセス許可内のアクション パターンと比較するときに SDK で使用される、アクション一致ロジックと同等です。 |
| クロス積比較 | `ForAnyOfAnyValues:StringEquals`<br/>`ForAnyOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotEquals`<br/>`ForAnyOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAnyValues:StringLike`<br/>`ForAnyOfAnyValues:StringLikeIgnoreCase`<br/>`ForAnyOfAnyValues:StringNotLike`<br/>`ForAnyOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAnyValues:NumericEquals`<br/>`ForAnyOfAnyValues:NumericNotEquals`<br/>`ForAnyOfAnyValues:NumericGreaterThan`<br/>`ForAnyOfAnyValues:NumericGreaterThanEquals`<br/>`ForAnyOfAnyValues:NumericLessThan`<br/>`ForAnyOfAnyValues:NumericLessThanEquals` | 左側の少なくとも 1 つの値が右側の少なくとも 1 つの値との比較を満たす場合、式は true に評価されます。 形式は `ForAnyOfAnyValues:<BooleanFunction>` です。 複数の文字列と数値をサポートします。 |
|  | `ForAllOfAnyValues:StringEquals`<br/>`ForAllOfAnyValues:StringEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringNotEquals`<br/>`ForAllOfAnyValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAnyValues:StringLike`<br/>`ForAllOfAnyValues:StringLikeIgnoreCase`<br/>`ForAllOfAnyValues:StringNotLike`<br/>`ForAllOfAnyValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAnyValues:NumericEquals`<br/>`ForAllOfAnyValues:NumericNotEquals`<br/>`ForAllOfAnyValues:NumericGreaterThan`<br/>`ForAllOfAnyValues:NumericGreaterThanEquals`<br/>`ForAllOfAnyValues:NumericLessThan`<br/>`ForAllOfAnyValues:NumericLessThanEquals` | 左側のすべての値が右側の少なくとも 1 つの値との比較を満たす場合、式は true に評価されます。 形式は `ForAllOfAnyValues:<BooleanFunction>` です。 複数の文字列と数値をサポートします。 |
|  | `ForAnyOfAllValues:StringEquals`<br/>`ForAnyOfAllValues:StringEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringNotEquals`<br/>`ForAnyOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAnyOfAllValues:StringLike`<br/>`ForAnyOfAllValues:StringLikeIgnoreCase`<br/>`ForAnyOfAllValues:StringNotLike`<br/>`ForAnyOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAnyOfAllValues:NumericEquals`<br/>`ForAnyOfAllValues:NumericNotEquals`<br/>`ForAnyOfAllValues:NumericGreaterThan`<br/>`ForAnyOfAllValues:NumericGreaterThanEquals`<br/>`ForAnyOfAllValues:NumericLessThan`<br/>`ForAnyOfAllValues:NumericLessThanEquals` | 左側の少なくとも 1 つの値が右側のすべての値との比較を満たす場合、式は true に評価されます。 形式は `ForAnyOfAllValues:<BooleanFunction>` です。 複数の文字列と数値をサポートします。 |
|  | `ForAllOfAllValues:StringEquals`<br/>`ForAllOfAllValues:StringEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringNotEquals`<br/>`ForAllOfAllValues:StringNotEqualsIgnoreCase`<br/>`ForAllOfAllValues:StringLike`<br/>`ForAllOfAllValues:StringLikeIgnoreCase`<br/>`ForAllOfAllValues:StringNotLike`<br/>`ForAllOfAllValues:StringNotLikeIgnoreCase`<br/>`ForAllOfAllValues:NumericEquals`<br/>`ForAllOfAllValues:NumericNotEquals`<br/>`ForAllOfAllValues:NumericGreaterThan`<br/>`ForAllOfAllValues:NumericGreaterThanEquals`<br/>`ForAllOfAllValues:NumericLessThan`<br/>`ForAllOfAllValues:NumericLessThanEquals` | 左側のすべての値が右側のすべての値との比較を満たす場合、式は true に評価されます。 形式は `ForAllOfAllValues:<BooleanFunction>` です。 複数の文字列と数値をサポートします。 |

## <a name="operator-examples"></a>演算子の例

> [!div class="mx-tableFixed"]
> | 例 | 結果 |
> | --- | --- |
> | `ActionMatches{'Microsoft.Authorization/roleAssignments/*'}` | チェックされているアクションが "Microsoft.Authorization/roleAssignments/write" と等しい場合は true |
> | `ActionMatches{'Microsoft.Authorization/roleDefinitions/*'}` | チェックされているアクションが "Microsoft.Authorization/roleAssignments/write" と等しい場合は false |
> | `Resource[name1] StringLike 'a*c?'` | Resource[name1] が "abcd" と等しい場合は true |
> | `Resource[name1] StringLike 'A*C?'` | Resource[name1] が "abcd" と等しい場合は false |
> | `Resource[name1] StringLike 'a*c'` | Resource[name1] が "abcd" と等しい場合は false |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'blue', 'green'}` | true |
> | `{'red', 'blue'} ForAnyOfAnyValues:StringEquals {'orange', 'green'}` | false |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'orange', 'red', 'blue'}` | true |
> | `{'red', 'blue'} ForAllOfAnyValues:StringEquals {'red', 'green'}` | false |
> | `{10, 20} ForAnyOfAllValues:NumericLessThan {15, 18}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {5, 15, 18}` | false |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {25, 30}` | true |
> | `{10, 20} ForAllOfAllValues:NumericLessThan {15, 25, 30}` | false |

## <a name="special-characters"></a>特殊文字

| 文字 | 説明 |
| --- | --- |
| `*` | アスタリスク (*) は、`Like` 演算子で使用できる複数文字のワイルドカード一致を表します。 必要であれば、円記号 `\*` を追加してアスタリスクをエスケープできます。 |
| `?` | 疑問符 (?) は、`Like` 演算子で使用できる 1 文字のワイルドカード一致を表します。 必要であれば、円記号 `\?` を追加して疑問符をエスケープできます。 |
| `$` | ドル記号 ($) は、タグ キーを区切るために使用します。 Azure PowerShell では、二重引用符 (") で囲まれた文字列にドル記号が含まれている場合、バックティック (\`) でプレフィックスを付ける必要があります。 (例: ``tags:Project<`$key_case_sensitive`$>``)。 | 

## <a name="grouping-and-precedence"></a>グループ化と優先順位

条件内で式をグループ化し、優先順位を定義するには、かっこ `()` を使用します。 対象となるアクションに対して 3 つ以上の式がある場合は、かっこを追加して、式の評価順序を定義する必要があります。 かっこで囲まれた式は、より高い優先順位になります。 たとえば、次のような式があるとします。

```
a AND b OR c
```

次のいずれかの方法でかっこを追加する必要があります。

```
(a AND b) OR c
```

```
a AND (b OR c)
```

## <a name="next-steps"></a>次のステップ

- [Azure ロールの割り当て条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)
- [Azure Storage での Azure ロールの割り当て条件のアクションと属性 (プレビュー)](../storage/common/storage-auth-abac-attributes.md)

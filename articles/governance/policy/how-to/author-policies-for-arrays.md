---
title: リソースの配列プロパティのポリシーを作成する
description: Azure Policy 定義ルールを使用して、配列パラメーターおよび配列の言語式を処理し、[*] エイリアスを評価し、要素を付加する方法について説明します。
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 18afbee0ca8b1c488e3bd3ce50dacc726bd2ef25
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305193"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure リソースの配列プロパティのポリシーを作成する

通常、Azure Resource Manager のプロパティは文字列とブール値として定義されます。 一対多のリレーションシップが存在するときには、複雑なプロパティは代わりに配列として定義されます。 Azure Policy では、配列は以下の複数の方法で使用されます。

- 複数のオプションを提供する、[定義パラメーター](../concepts/definition-structure.md#parameters)の型
- **in** または **notIn** の条件を使用した [ポリシー ルール](../concepts/definition-structure.md#policy-rule)の一部
- 条件を満たす配列メンバーの数をカウントするポリシー ルールの一部
- 既存の配列を更新するための [append](../concepts/effects.md#append) と [modify](../concepts/effects.md#modify) 効果の中

この記事では、Azure Policy によるそれぞれの使用について説明し、複数の定義例を紹介します。

## <a name="parameter-arrays"></a>パラメーター配列

### <a name="define-a-parameter-array"></a>パラメーター配列を定義する

配列としてパラメーターを定義すると、複数の値が必要なときにポリシーを柔軟に使用することができます。
このポリシー定義により、パラメーター **allowedLocations** が表す単一の場所がどこでも可能になり、既定では _eastus2_ になります。

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

**type** が _string_ だったので、ポリシーの割り当て時には 1 つの値しか設定できません。 このポリシーが割り当てられると、スコープ内のリソースは、単一の Azure リージョン内でのみ許可されます。 ほとんどのポリシー定義は、_eastus2_、_eastus_、_westus2_ を許可するなど、承認済みのオプションの一覧を許可する必要があります。

複数のオプションを許可するポリシー定義を作成するには、_array_ **type** を使用します。 同じポリシーは、次のように書き換えることができます。

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> ポリシー定義が保存されると、パラメーターの **type** プロパティは変更できません。

この新しいパラメーター定義は、ポリシーの割り当て中に複数の値を取ります。 配列プロパティ **allowedValues** が定義されている場合、割り当て中に利用できる値は、事前に定義された選択肢の一覧にさらに制限されます。 **allowedValues** の使用は省略可能です。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>割り当て中にパラメーター配列に値を渡す

Azure portal からポリシーを割り当てるときに、**type** _array_ のパラメーターは単一のテキストボックスとして表示されます。 ヒントには「値を区切るには ; を使用してください (例: ロンドン;New York)」と示されます。 _eastus2_、_eastus_、および _westus2_ の許可されている場所の値をパラメーターに渡すには、次の文字列を使用します。

`eastus2;eastus;westus2`

パラメーター値の形式は、Azure CLI、Azure PowerShell、または REST API を使用する場合で異なります。 値は、パラメーターの名前も含んだ JSON 文字列で渡されます。

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

各 SDK でこの文字列を使用するには、次のコマンドを使用します。

- Azure CLI:コマンド [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) とパラメーター **params**
- Azure PowerShell:コマンドレット [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) とパラメーター **PolicyParameter**
- REST API:要求本文の一部としての _PUT_ [作成](/rest/api/policy/policyassignments/create)操作において、**properties.parameters** プロパティの値として

## <a name="using-arrays-in-conditions"></a>条件内で配列を使用する

### <a name="in-and-notin"></a>in と notIn

`in` と `notIn` の条件は、配列の値に対してのみ機能します。 これらは、配列内に値が存在するかどうかを確認します。 配列には、リテラル JSON 配列または配列パラメーターへの参照を指定できます。 以下に例を示します。

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>値のカウント

[値のカウント](../concepts/definition-structure.md#value-count)式は、条件を満たしている配列メンバーの数をカウントします。 これにより、反復ごとに異なる値を使用して、同じ条件を複数回評価できます。 たとえば、次の条件では、リソース名がパターンの配列のいずれかのパターンと一致するかどうかを確認します。

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

式を評価するため、Azure Policy により `[ "test*", "dev*", "prod*" ]` のメンバーごとに 1 回ずつ `where` 条件が 3 回評価され、`true` に評価された回数がカウントされます。 各反復では、現在の配列メンバーの値が、`count.name`によって定義された `pattern` インデックス名とペアになります。 これでこの値は、特殊なテンプレート関数 `current('pattern')` を呼び出すことで `where` 条件内で参照できるようになります。

| イテレーション | `current('pattern')` 戻り値 |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

結果のカウントが 0 より大きい場合にのみ、条件は true になります。

上記の条件をより汎用的にするには、リテラル配列ではなく、パラメーター参照を使用します。

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

**値のカウント** 式が他のどの **カウント** 式にも含まれていない場合、`count.name` は省略可能になり、`current()` 関数は引数を指定せずに使用できます。

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

**値のカウント** では複合オブジェクトの配列もサポートされるため、より複雑な条件を指定できます。 たとえば、次の条件では、各名前パターンに必要なタグ値が定義され、リソース名がパターンに一致するかどうかが確認されますが、必要なタグ値がありません。

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

有用な例については、「[値のカウントの例](../concepts/definition-structure.md#value-count-examples)」を参照してください。

## <a name="referencing-array-resource-properties"></a>配列リソース プロパティの参照

多くのユース ケースでは、評価されたリソースで配列プロパティを操作する必要があります。 一部のシナリオでは、配列全体を参照する必要があります (たとえば、その長さを確認するなど)。 それ以外では、各配列メンバーに条件を適用する必要があります (たとえば、すべてのファイアウォール規則によってインターネットからのアクセスがブロックされるようにします)。 Azure Policy がリソース プロパティを参照できるさまざまな方法と、これらの参照が配列プロパティを参照するときの動作を理解することは、これらのシナリオに対応する条件を記述するための鍵となります。

### <a name="referencing-resource-properties"></a>リソース プロパティの参照

リソース プロパティは、[エイリアス](../concepts/definition-structure.md#aliases)を使用して Azure Policy によって参照できます。Azure Policy 内のリソース プロパティの値を参照する方法は 2 つあります。

- [フィールド](../concepts/definition-structure.md#fields)条件を使用して、選択した **すべての** リソース プロパティが条件を満たしているかどうかを確認します。 例:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- プロパティの値にアクセスするには、`field()` 関数を使用します。 例:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

フィールド条件には、"すべての" という暗黙的な動作があります。 エイリアスが値のコレクションを表す場合は、すべての個別の値が条件を満たしているかどうかが確認されます。 `field()` 関数は、エイリアスによって表される値をそのまま返します。これはその後、他のテンプレート関数によって操作できます。

### <a name="referencing-array-fields"></a>配列フィールドの参照

配列リソース プロパティは、通常、2 つの異なる種類のエイリアスによって表されます。 1 つの "標準" エイリアスと、それに `[*]` が付いた[配列のエイリアス](../concepts/definition-structure.md#understanding-the--alias)です。

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>配列の参照

最初のエイリアスは、要求内容の `stringArray` プロパティの値である 1 つの値を表します。 そのプロパティの値は配列であるため、ポリシー条件では役に立ちません。 次に例を示します。

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

この条件は、`stringArray` 配列全体を 1 つの文字列値と比較します。 `equals` を含むほとんどの条件は文字列値しか受け入れないため、配列と文字列を比較することはあまり意味がありません。 配列プロパティを参照することが有用な主なシナリオは、それが存在するかどうかを確認する場合です。

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

`field()` 関数を使用すると、返される値は要求内容からの配列になります。これは、配列の引数を受け入れる[サポート対象のテンプレート関数](../concepts/definition-structure.md#policy-functions)のいずれでも使用できます。 たとえば、次の条件では `stringArray` の長さが 0 より大きいかどうかを確認します。

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>配列メンバー コレクションの参照

`[*]` 構文を使用するエイリアスは、**配列プロパティから選択されたプロパティ値のコレクション** を表します。これは、配列プロパティ自体の選択とは異なります。 `Microsoft.Test/resourceType/stringArray[*]` の場合、`stringArray` のすべてのメンバーを含むコレクションが返されます。 前述のように、`field` 条件では、選択したすべてのリソース プロパティが条件を満たしているかどうかが確認されるため、次の条件が true になるのは、`stringArray` のメンバーの **すべて** が「"value"」 と等しい場合のみです。

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

配列にオブジェクトが含まれている場合は、`[*]` エイリアスを使用して、各配列メンバーから特定のプロパティの値を選択できます。 例:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

`objectArray` 内のすべての `property` プロパティの値が `"value"` と等しい場合、この条件は true です。 その他の例については、「[その他の \[\*\] エイリアスの例](#additional--alias-examples)」を参照してください。

`field()` 関数を使用して配列エイリアスを参照すると、返される値は、選択された値すべての配列になります。 この動作は、テンプレート関数をリソース プロパティ値に適用する機能である `field()` 関数の一般的なユース ケースが限られていることを意味します。 この場合に使用できるテンプレート関数は、配列の引数を受け入れるものだけです。 たとえば、`[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` で配列の長さを取得することはできます。 ただし、テンプレート関数を各配列メンバーに適用し、それを目的の値と比較するといった、より複雑なシナリオは `count` 式を使用した場合にのみ可能です。 詳細については、「[フィールドのカウント式](#field-count-expressions)」を参照してください。

要約として、次のリソース コンテンツの例と、さまざまなエイリアスによって返される選択された値を参照してください。

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

この例のリソース コンテンツに対してフィールド条件を使用すると、結果は次のようになります。

| エイリアス | 選択された値 |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | 値の空のコレクション。 |
| `Microsoft.Test/resourceType/missingArray[*].property` | 値の空のコレクション。 |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

この例のリソース コンテンツに対して `field()` 関数を使用すると、結果は次のようになります。

| Expression | 戻り値 |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>フィールドのカウント式

[フィールドのカウント](../concepts/definition-structure.md#field-count)式は、ある条件を満たす配列メンバーの数をカウントし、その数を対象の値と比較します。 `Count` は、`field` 条件と比較して、配列の評価においてより直感的で幅広い用途があります。 の構文は次のとおりです。

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

`where` 条件なしで使用する場合、`count` は単に配列の長さを返します。 前のセクションのリソース コンテンツの例では、`stringArray` には 3 つのメンバーが含まれるため、次の `count` 式は `true` に評価されます。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

この動作は、入れ子になった配列でも動作します。 たとえば、次の `count` 式は、`nestedArray` 配列に 4 つの配列メンバーがあるため、`true` に評価されます。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

`count` の累乗が `where` 条件に含まれます。 これが指定されている場合、Azure Policy によって配列メンバーが列挙され、それぞれが条件に対して評価されて、`true` に評価された配列メンバーの数がカウントされます。 具体的には、`where` 条件の評価の各反復において、Azure Policy により 1 つの配列メンバー ***i** _ が選択され、 _****i**_ が配列の唯一のメンバーであるかのように_* `where` 条件に対してリソース コンテンツが評価されます。 各反復で使用できる配列メンバーを 1 つだけにすると、個々の配列メンバーに複雑な条件を適用する方法を使用できます。

例:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```

`count` 式を評価するため、Azure Policy により `stringArray` のメンバーごとに 1 回ずつ `where` 条件が 3 回評価され、`true` に評価された回数がカウントされます。
`where` 条件で `Microsoft.Test/resourceType/stringArray[*]` 配列メンバーが参照される場合は、`stringArray` のすべてのメンバーが選択されるのではなく、毎回 1 つの配列メンバーのみが選択されることになります。

| 反復 | 選択された `Microsoft.Test/resourceType/stringArray[*]` 値 | `where` の評価結果 |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

`count` からは `1` が返されます。

より複雑な式を次に示します。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| 反復 | 選択された値 | `where` の評価結果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

`count` からは `1` が返されます。

(現在列挙されている配列メンバーのみに対する変更を含む) 要求内容 **全体** に対して `where` 式が評価されるという事実は、`where` 条件が配列外部のフィールドも参照できるということを意味します。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  },
  "equals": 0
}
```

| 反復 | 選択された値 | `where` の評価結果 |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

入れ子になった count 式を使用すると、入れ子になった配列フィールドに条件を適用できます。 たとえば、次の条件では、`objectArray[*]` 配列に、1 つ以上のメンバーを含む `nestedArray[*]` を持つメンバーが厳密に 2 つあることが確認されます。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| 反復 | 選択された値 | 入れ子になったカウントの評価結果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` には 2 つのメンバーが含まれる => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` には 2 つのメンバーが含まれる => `true` |

`objectArray[*]` の両方のメンバーには 2 つのメンバーを含む子配列 `nestedArray[*]` があるため、外側のカウント式からは `2` が返されます。

より複雑な例: `objectArray[*]` 配列に、`2` または `3` と等しいメンバーを含む `nestedArray[*]` を持つメンバーが厳密に 2 つあることを確認します。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
        "where": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "in": [ 2, 3 ]
        }
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| 反復 | 選択された値 | 入れ子になったカウントの評価結果
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` には `2` => `true` が含まれる |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` には `3` => `true` が含まれる |

`objectArray[*]` の両方のメンバーには `2` または `3` を含む子配列 `nestedArray[*]` があるため、外側のカウント式は `2` を返します。

> [!NOTE]
> 入れ子になったフィールドのカウント式で参照できるのは、入れ子になった配列のみです。 たとえば、`Microsoft.Test/resourceType/objectArray[*]` を参照している count 式には、入れ子になった配列 `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` をターゲットにする入れ子になった count を含めることができますが、`Microsoft.Test/resourceType/stringArray[*]` をターゲットにする入れ子になった count 式を含めることはできません。

#### <a name="accessing-current-array-member-with-template-functions"></a>テンプレート関数を使用して現在の配列メンバーにアクセスする

テンプレート関数を使用する場合は、`current()` 関数を使用して、現在の配列メンバーの値、またはそのいずれかのプロパティの値にアクセスします。 現在の配列メンバーの値にアクセスするには、`count.field` で定義されたエイリアスまたはそのいずれかの子エイリアスを、引数として `current()` 関数に渡します。 以下に例を示します。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| イテレーション | `current()` 戻り値 | `where` の評価結果 |
|:---|:---|:---|
| 1 | `objectArray[*]` の最初のメンバーの `property` の値: `value1` | `true` |
| 2 | `objectArray[*]` の最初のメンバーの `property` の値: `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>where 条件内の field 関数

`field()` 関数は、**count** 式が **存在条件** の内部にない限り、現在の配列メンバーの値へのアクセスにも使用できます (`field()` 関数は常に、**if** 条件で評価されたリソースを参照します)。 評価された配列を参照する場合の `field()` の動作は、次の概念に基づいています。

1. 配列エイリアスは、すべての配列メンバーから選択された値のコレクションに解決されます。
1. 配列エイリアスを参照する `field()` 関数は、選択された値を含む配列を返します。
1. カウントされた配列エイリアスを `where` 条件内で参照すると、現在の反復で評価される配列メンバーから選択された 1 つの値を含むコレクションが返されます。

この動作は、カウントされた配列メンバーを `where` 条件内の `field()` 関数で参照すると、**1 つのメンバーを含む配列が返される** ことを意味します。 この動作は直観的ではないかもしれませんが、配列エイリアスは、選択されたプロパティのコレクションを必ず返すという考えと一致しています。 次に例を示します。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| 反復 | 式の値 | `where` の評価結果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

したがって、`field()` 関数を使用して、カウントされた配列エイリアスの値にアクセスする必要がある場合、これを行う方法は、`first()` テンプレート関数を使用してそれをラップすることです。

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| 反復 | 式の値 | `where` の評価結果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

有用な例については、「[フィールドのカウントの例](../concepts/definition-structure.md#field-count-examples)」を参照してください。

## <a name="modifying-arrays"></a>配列の変更

[append](../concepts/effects.md#append) と [modify](../concepts/effects.md#modify) により、作成時または更新時にリソースのプロパティが変更されます。 配列プロパティを操作する場合、これらの効果の動作は、操作で **\[\*\]** エイリアスを変更しようとしているかどうかによって異なります。

> [!NOTE]
> エイリアスによる `modify` 効果の使用は、現在 **プレビュー** 段階にあります。

|エイリアス |結果 | 結果 |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | 欠落している場合、Azure Policy により、効果の詳細に指定された配列全体が追加されます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `add` 操作による `modify` | 欠落している場合、Azure Policy により、効果の詳細に指定された配列全体が追加されます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `addOrReplace` 操作による `modify` | 欠落している場合、Azure Policy により、効果の詳細に指定された配列全体が追加されるか、既存の配列が置き換えられます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy により、効果の詳細に指定された配列メンバーが追加されます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `add` 操作による `modify` | Azure Policy により、効果の詳細に指定された配列メンバーが追加されます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `addOrReplace` 操作による `modify` | Azure Policy により、既存のすべての配列メンバーが削除され、効果の詳細に指定された配列メンバーが追加されます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy により、各配列メンバーの `action` プロパティに値が追加されます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `add` 操作による `modify` | Azure Policy により、各配列メンバーの `action` プロパティに値が追加されます。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `addOrReplace` 操作による `modify` | Azure Policy により、各配列メンバーの既存の `action` プロパティが追加されるか、または置き換えられます。 |

詳細については、「[Append の例](../concepts/effects.md#append-examples)」を参照してください。

## <a name="additional--alias-examples"></a>その他の [*] エイリアスの例

要求コンテンツ内の配列の "すべて" または "いずれか" のメンバーが条件を満たしているかどうかを確認するには、[フィールドのカウント式](#field-count-expressions)を使用することをお勧めします。 ただし、「[配列メンバー コレクションの参照](#referencing-the-array-members-collection)」で説明されているように、一部の単純な条件では、配列エイリアスを指定したフィールド アクセサーを使用することで同じ結果を得ることができます。 このパターンは、許容された **count** 式の制限を超えるポリシー ルールで役立つ可能性があります。 一般的なユース ケースの例を次に示します。

下のシナリオ テーブルのポリシー ルール例:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

下のシナリオ テーブルの **IpRules** 配列は次のようになります。

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

下の条件例ごとに、`<field>` を `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` に置き換えます。

以下の結果は、条件と、上記のポリシー ルール例および既存の値の配列とを組み合わせたものです。

|条件 |結果 | シナリオ |説明 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |なし |一致なし |1 つの配列要素が false (127.0.0.1 != 127.0.0.1) として、もう 1 つが true (127.0.0.1 != 192.168.1.1) として評価されるので、**notEquals** 条件が _false_ になり、効果はトリガーされません。 |
|`{<field>,"notEquals":"10.0.4.1"}` |ポリシー効果 |一致なし |両方の配列要素が true (10.0.4.1 != 127.0.0.1 および 10.0.4.1 != 192.168.1.1) として評価されるので、**notEquals** 条件が _true_ になり、効果はトリガーされます。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |ポリシー効果 |1 つまたは複数の一致あり |1 つの配列要素が false (127.0.0.1 != 127.0.0.1) として、もう 1 つが true (127.0.0.1 != 192.168.1.1) として評価されるので、**notEquals** 条件は _false_ になります。 論理演算子は true (**not** _false_) として評価されるので、効果はトリガーされます。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |なし |1 つまたは複数の一致あり |両方の配列要素が true (10.0.4.1 != 127.0.0.1 および 10.0.4.1 != 192.168.1.1) として評価されるので、**notEquals** 条件は _true_ になります。 論理演算子は false (**not** _true_) として評価されるので、効果はトリガーされません。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |ポリシー効果 |一部一致なし |1 つの配列要素が true (127.0.0.1 == 127.0.0.1) として、もう 1 つが false (127.0.0.1 == 192.168.1.1) として評価されるので、**Equals** 条件が _false_ になります。 論理演算子は true (**not** _false_) として評価されるので、効果はトリガーされます。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |ポリシー効果 |一部一致なし |両方の配列要素が false (10.0.4.1 == 127.0.0.1 および 10.0.4.1 == 192.168.1.1) として評価されるので、**Equals** 条件は _false_ になります。 論理演算子は true (**not** _false_) として評価されるので、効果はトリガーされます。 |
|`{<field>,"Equals":"127.0.0.1"}` |なし |すべて一致 |1 つの配列要素が true (127.0.0.1 == 127.0.0.1) として、もう 1 つが false (127.0.0.1 == 192.168.1.1) として評価されるので、**Equals** 条件が _false_ になり、効果はトリガーされません。 |
|`{<field>,"Equals":"10.0.4.1"}` |なし |すべて一致 |両方の配列要素が false (10.0.4.1 == 127.0.0.1 および 10.0.4.1 == 192.168.1.1) として評価されるので、**Equals** 条件が _false_ になり、効果はトリガーされません。 |

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [準拠していないリソースを修復する](remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。

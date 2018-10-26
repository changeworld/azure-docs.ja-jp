---
title: Azure Policy の定義の構造
description: Azure Policy でリソース ポリシー定義を使用して、ポリシーが適用されるタイミングとその効果を示すことで、組織でのリソースの規則を確立する方法について説明します。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: e3770fe29d6f1073a0ca6507fdf57059cbd3727e
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067538"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy の定義の構造

Azure Policy で使用されるリソース ポリシー定義を利用して、ポリシーが適用されるタイミングとその効果を示し、組織でのリソースの規則を確立することができます。 規則を定義することによって、コストを制御し、リソースをより簡単に管理することができます。 たとえば、特定の種類の仮想マシンのみを許可するように指定することができます。 また、すべてのリソースに特定のタグが指定されていることを必須にすることができます。 ポリシーは、すべての子リソースが継承します。 したがって、リソース グループに適用されたポリシーは、そのリソース グループのすべてのリソースに適用されます。

Azure Policy で使用されるスキーマについては、[https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json) を参照してください。

ポリシー定義を作成するには、JSON を使用します。 ポリシー定義には、以下のものに対する要素が含まれています。

- モード
- parameters
- 表示名
- description
- ポリシー規則
  - 論理評価
  - 効果

たとえば、次の JSON は、リソースがデプロイされる場所を制限するポリシーを示しています。

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Azure Policy のサンプルはすべて「[Azure Policy のサンプル](../samples/index.md)」にあります。

## <a name="mode"></a>Mode

**mode** では、ポリシーに対して評価されるリソースの種類を決定します。 サポートされているモードは次のとおりです。

- `all`: リソース グループとすべてのリソースの種類を評価します
- `indexed`: タグと場所をサポートするリソースの種類のみを評価します

ほとんどの場合、**mode** は `all` に設定することをお勧めします。 ポータルを使用して作成されるポリシーの定義はすべて、`all` モードを使用します。 PowerShell または Azure CLI を使用する場合、**mode** パラメーターを手動で指定することができます。 ポリシー定義に **mode** の値が含まれていない場合、下位互換性のために既定で、Azure PowerShell では `all` に、Azure CLI では `null` (`indexed` に相当) になります。

タグまたは場所を適用するポリシーを作成するときには、`indexed` を使用する必要があります。 これは必須ではありませんが、タグや場所をサポートしていないリソースが、コンプライアンス結果に非準拠として表れないようになります。 これの例外の 1 つは、**リソース グループ**です。 リソース グループに場所またはタグを適用しようとしているポリシーでは、**mode** を `all` に設定し、明確に `Microsoft.Resources/subscriptions/resourceGroup` 型をターゲットにする必要があります。 例については、[リソース グループのタグを適用する](../samples/enforce-tag-rg.md)ことに関する記事を参照してください。

## <a name="parameters"></a>parameters

パラメーターによって、ポリシー定義の数を減らし、ポリシーの管理を単純化できます。 1 つのフォームにあるフィールドのようなパラメーター `name`、`address``city``state` を考えてみてください。 これらのパラメーターは常に同じままですが、その値はフォームの個々の入力に基づいて変わります。
パラメーターは、ポリシーの作成時と同じように機能します。 ポリシー定義にパラメーターを含めることで、別の値を使用してさまざまなシナリオについてポリシーを再利用できます。

> [!NOTE]
> ポリシーまたはイニシアティブ定義のパラメーター定義は、ポリシーまたはイニシアティブの初期作成時にのみ構成できます。 パラメーター定義を後で変更することはできません。
> これにより、ポリシーまたはイニシアティブの既存の割り当てが間接的に無効になることを防ぎます。

たとえば、リソースのデプロイ先の場所を制限するために、リソース プロパティのポリシーを定義できます。 この場合、ポリシーを作成するときに、次のパラメーターを宣言します。

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

パラメーターの型は、文字列または配列のどちらも可能です。 メタデータ プロパティは、Azure Portal などのツールでわかりやすい情報を表示するために、使用されます。

メタデータ プロパティの中で **strongType** を使用して、Azure portal 内にオプションの複数選択リストを用意できます。 現時点で **strongType** で使用できる値には、以下が含まれます。

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

ポリシー規則では、次に示す `parameters` 関数とデプロイ値の構文でパラメーターを参照します。

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>定義の場所

イニシアティブまたはポリシー定義を作成するときに、定義の場所を指定することが重要です。

定義の場所で、イニシアティブまたはポリシー定義を割り当てることができるスコープが決まります。 場所は、管理グループまたはサブスクリプションとして指定することができます。

> [!NOTE]
> このポリシー定義を複数のサブスクリプションに適用する場合、この場所は、イニシアティブまたはポリシーを割り当てるサブスクリプションを含む管理グループである必要があります。

## <a name="display-name-and-description"></a>表示名と説明

ポリシー定義を識別し、定義が使用される際のコンテキストを指定するために、**displayName** と **description** を使用できます。

## <a name="policy-rule"></a>ポリシー規則

ポリシー規則は、**If** と **Then** ブロックで構成されています。 **If** ブロックでは、いつポリシーが適用されるかを指定する、1 つ以上の条件を定義します。 これらの条件に論理演算子を適用して、ポリシーのシナリオを細かく定義することができます。

**Then** ブロックでは、**If** 条件が満たされる場合に生じる効果を定義します。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>論理演算子

サポートされている論理演算子は、次のとおりです。

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 構文は、条件の結果を反転します。 **allOf** 構文 (**And** 論理演算に似ています) では、すべての条件が真である必要があります。 **anyOf** 構文 (**Or** 論理演算に似ています) では、1 つ以上の条件が真である必要があります。

論理演算子は、入れ子にすることができます。 次の例は、**allOf** 演算内で入れ子になっている **not** 演算を示しています。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>条件

条件は、**フィールド**が特定の基準を満たすかどうかを評価します。 サポートされている条件は次のとおりです。

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

**like** 条件や **notLike** 条件を使用する場合は、値にワイルドカード (`*`) を指定できます。
値に複数のワイルドカード (`*`) を含めることはできません。

**match** 条件や **notMatch** 条件を使うときは、任意の数字を表す `#` や任意の文字を表す `?` のほか、具体的な文字を指定することができます。 例については、「[複数の名前パターンを許可する](../samples/allow-multiple-name-patterns.md)」を参照してください。

### <a name="fields"></a>フィールド

条件は、フィールドを使用して構成されます。 フィールドは、リソースの状態の記述に使用されるリソース要求ペイロード内のプロパティを表します。

次のフィールドがサポートされています。

- `name`
- `fullName`
  - リソースのフル ネームを返します。 リソースのフル ネームは、親リソースの名前が付加されたリソース名です ("myServer/myDatabase" など)。
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - **\<tagName\>** は、条件を検証するタグの名前です。
  - 例: `tags.CostCenter`。**CostCenter** がタグの名前です。
- `tags[<tagName>]`
  - このかっこ構文は、ピリオドを含むタグ名をサポートします。
  - **\<tagName\>** は、条件を検証するタグの名前です。
  - 例: `tags[Acct.CostCenter]`。**Acct.CostCenter** がタグの名前です。
- プロパティのエイリアス: 一覧については、「[エイリアス](#aliases)」を参照してください。

### <a name="effect"></a>効果

ポリシーでは、次の種類の効果がサポートされています。

- **deny** は監査ログでイベントを生成し、要求は失敗します。
- **audit** は監査ログで警告イベントを生成しますが、要求は失敗しません。
- **append** は定義済みのフィールド セットを要求に追加します。
- **AuditIfNotExists** は、リソースが存在しない場合に監査を有効にします。
- **DeployIfNotExists** は、リソースが存在しない場合にリソースをデプロイします。

**append** の場合、次のように詳細を指定する必要があります。

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

値には文字列または JSON 形式オブジェクトを指定できます。

**AuditIfNotExists** および **DeployIfNotExists** を使用すると、関連するリソースの存在を評価したうえで、そのリソースが存在しない場合に、ルールと該当する効果を適用することができます。 たとえば、すべての仮想ネットワークを対象に Network Watcher のデプロイを要求することができます。 仮想マシン拡張機能がデプロイされていない場合の監査の例については、[拡張機能が存在しない場合の監査](../samples/audit-ext-not-exist.md)に関するページを参照してください。

各効果の詳細、評価の順序、プロパティ、例については、「[Policy の効果について](effects.md)」を参照してください。

### <a name="policy-functions"></a>ポリシー関数

[Resource Manager テンプレート関数](../../../azure-resource-manager/resource-group-template-functions.md)のサブセットをポリシー規則内で使用できます。 現在サポートされている関数は次のとおりです。

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [サブスクリプション](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

さらに、`field` 関数もポリシー規則で使用できます。 この関数は、主に **AuditIfNotExists** や **DeployIfNotExists** で、評価されているリソースのフィールドを参照するために使用されます。 例については、[DeployIfNotExists の例](effects.md#deployifnotexists-example)をご覧ください。

#### <a name="policy-function-examples"></a>ポリシー関数の例

このポリシー規則の例では、`resourceGroup` リソース関数を使用して **name** プロパティを取得します。ここでは、`concat` 配列およびオブジェクト関数と組み合わせて、リソース グループ名で始まるリソース名を指定する `like` 条件を作成します。

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

このポリシー規則の例では、`resourceGroup` リソース関数を使用して、リソース グループの **CostCenter**タグの **tags** プロパティ配列値を取得し、それを新しいリソースの **CostCenter** タグに付加します。

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>エイリアス

リソースの種類に固有のプロパティにアクセスするには、プロパティのエイリアスを使用します。 エイリアスを使用すると、リソースのプロパティに使用できる値または条件を制限できます。 各エイリアスは、特定のリソースの種類について異なる API バージョンのパスにマップされます。 ポリシーの評価時に、ポリシー エンジンはその API バージョンのプロパティ パスを取得します。

エイリアスの一覧は常に拡大しています。 Azure Policy で現在サポートされているエイリアスを見つけるには、次のいずれかの方法を使用します。

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  # Use Get-AzureRmPolicyAlias to list available providers
  Get-AzureRmPolicyAlias -ListAvailable

  # Use Get-AzureRmPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzureRmPolicyAlias -NamespaceMatch 'automation'
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>[*] エイリアスについて

利用できるいくつかのエイリアスには、'normal' という名前で表示されるバージョンと、それに **[\*]** が添付された別のバージョンがあります。 例: 

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

最初の例は配列全体を評価するために使用され、**[\*]** エイリアスでは配列の各要素が評価されます。

例として、ポリシー規則を見てみましょう。 このポリシーは、ipRules が構成されていて、かつ ipRules のいずれも値が "127.0.0.1" で**ない**ストレージ アカウントを**拒否**します。

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

**IpRules** 配列の例は次のとおりです。

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

この例の処理方法を次に示します。

- `networkAcls.ipRules` - 配列が null 値 でないことを確認します。 true に評価されるので、評価を続けます。

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` - **ipRules** 配列内の各 _value_ プロパティを確認します。

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - 配列として、各要素が処理されます。

    - "127.0.0.1" != "127.0.0.1" は false として評価されます。
    - "127.0.0.1" != "192.168.1.1" true として評価されます。
    - **ipRules** 配列内の少なくとも 1 つの _value_ プロパティが false と評価されるので、評価は停止します。

条件が false に評価されるので、**拒否**の効果はトリガーされません。

## <a name="initiatives"></a>イニシアティブ

イニシアティブを使用すると、複数の関連するポリシー定義をグループ化できます。グループを単一の項目として操作するので、割り当てと管理が単純になります。 たとえば、関連するすべてのタグ付けポリシー定義を 1 つのイニシアティブとしてグループ化できます。 それぞれのポリシーを個別に割り当てるのではなく、イニシアティブを適用することになります。

次の例は、2 つのタグ (`costCenter` および `productName`) を扱うためのイニシアティブの作成方法を示しています。 2 つの組み込みポリシーを使用して、既定のタグの値を適用しています。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認する
- [ポリシーの効果について](effects.md)確認する
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解する
- [コンプライアンス データを取得する](../how-to/getting-compliance-data.md)ための方法を学びます。
- [非準拠のリソースを修復する](../how-to/remediate-resources.md)方法を見つける
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。

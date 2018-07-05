---
title: Azure Policy の効果について
description: Azure Policy の定義には、コンプライアンスが管理および報告される方法を決定するさまざまな効果があります。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 1566cf2b61749121c4eaff5a32b0a940f3341f7e
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751780"
---
# <a name="understanding-policy-effects"></a>Policy の効果について

Azure Policy の各ポリシー定義には効果が 1 つ指定されており、スキャン時にポリシー規則の **if** セグメントがスキャン対象のリソースに適合すると評価された場合の動作が決定されます。 新しいリソース、更新されたリソース、または既存のリソースに対して、異なる方法で効果を動作させることもできます。

現在、ポリシー定義では次の 5 つの効果がサポートされています。

- 追加
- Audit
- AuditIfNotExists
- 拒否
- DeployIfNotExists

## <a name="order-of-evaluation"></a>評価の順序

Azure Resource Manager を通してリソースを作成または更新する要求が行われると、要求が適切なリソース プロバイダーに渡される前にいくつかの効果が処理されます。
それによって、リソースが意図された Policy のガバナンス コントロールを満たさない場合に、リソース プロバイダーによって不要な処理が行われるのを防止します。 Policy は、ポリシーまたはイニシアティブの割り当てごとに、リソースにスコープ (マイナス除外) が適用される、割り当て済みのすべてのポリシー定義の一覧を作成し、各定義に対してリソースを評価する準備をします。

- **Append** は最初に評価されます。 Append によって要求が変更される可能性があるため、Append で加えられた変更によって、Audit または Deny の効果がトリガーされるのが止められる場合があります。
- 次に、**Deny** が評価されます。 Audit の前に Deny を評価することによって、不要なリソースの二重のログ記録が回避されます。
- 次に、要求がリソース プロバイダーに移動する前に **Audit** が評価されます。

要求がリソース プロバイダーに提供され、リソース プロバイダーが成功状態コードを返すと、フォローアップ コンプライアンスのログ記録またはアクションが必要かどうかを決定するために、**AuditIfNotExists** と **DeployIfNotExists** が評価されます。

## <a name="append"></a>追加

Append は、作成中または更新中に要求されたリソースにフィールドを追加するために使用します。 コスト センターなどのリソースにタグを追加したり、ストレージ リソースに対して許可される IP を指定したりする場合に便利です。

### <a name="append-evaluation"></a>Append の評価

前述のように、リソースの作成中または更新中に、リソース プロバイダーで要求が処理される前に Append が評価されます。 Append は、ポリシー規則の **if** 条件が満たされた場合、リソースにフィールドを追加します。 Append 効果によって元の要求の値が別の値でオーバーライドされる場合、Append は Deny 効果として機能し、要求を拒否します。

Append 効果を使用するポリシー定義が評価サイクルの一部として実行される場合、既存のリソースに対する変更は行われません。 代わりに、**if** 条件を満たすリソースが非準拠とマークされます。

### <a name="append-properties"></a>Append のプロパティ

Append 効果には必須の **details** 配列が 1 つだけあります。 **details** は配列なので、1 つまたは複数の **field/value** のペアをとることができます。 許容可能なフィールドの一覧については、[ポリシー定義](policy-definition.md#fields)を参照してください。

### <a name="append-examples"></a>Append の例

例 1: 1 つのタグを追加する単一の **field/value** のペア。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

例 2: タグのセットを追加する複数の **field/value** のペア。

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

例 3: 配列 **value** の[別名](policy-definition.md#aliases)を使用して、ストレージ アカウントに IP 規則を設定する単一の **field/value** のペア。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

## <a name="deny"></a>拒否

Deny は、ポリシー定義を通じて目的の基準と一致せず、要求が失敗したリソース要求を止めるために使用します。

### <a name="deny-evaluation"></a>Deny の評価

リソースを作成または更新する場合、Deny は、リソース プロバイダーに送信される前に要求を止めます。 要求は 403 (Forbidden) として返されます。 ポータルでは、ポリシー割り当てのために失敗した展開の状態として Forbidden を表示できます。

評価サイクルでは、リソースを照合する Deny 効果のあるポリシー定義は非準拠としてマークされますが、そのリソースにはアクションは実行されません。

### <a name="deny-properties"></a>Deny のプロパティ

Deny 効果には、ポリシー定義の **then** 条件で使用するためのその他のプロパティはありません。

### <a name="deny-example"></a>Deny の例

例: Deny 効果を使用します。

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

非準拠のリソースが評価された場合、Audit 効果を使用して監査ログに警告イベントが作成されますが、要求は停止されません。

### <a name="audit-evaluation"></a>Audit の評価

Audit 効果は、リソースがリソース プロバイダーに送信される前に、リソースの作成または更新の最後に実行されます。 Audit は、リソース要求および評価サイクルに対して同様に機能し、アクティビティ ログに対して `Microsoft.Authorization/policies/audit/action` 操作を実行します。 どちらの場合も、リソースは非準拠としてマークされます。

### <a name="audit-properties"></a>Audit のプロパティ

Audit 効果には、ポリシー定義の **then** 条件で使用するためのその他のプロパティはありません。

### <a name="audit-example"></a>Audit の例

例: Audit 効果を使用します。

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists は、**if** 条件に一致するリソースで監査を有効にしますが、**then** 条件の **details** で指定されるコンポーネントはありません。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists の評価

AuditIfNotExists は、リソース プロバイダーがリソースに対する作成または更新の要求を処理し、成功状態コードを返した後に実行されます。 効果は、関連するリソースが見つからない場合、または **ExistenceCondition** で定義されるリソースが true と評価されない場合にトリガーされます。 効果がトリガーされた場合は、Audit 効果と同様に、アクティビティ ログに対する `Microsoft.Authorization/policies/audit/action` 操作が実行されます。 トリガーされた場合、**if** 条件を満たしているリソースは、非準拠としてマークされているリソースです。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists のプロパティ

AuditIfNotExists 効果の **details** プロパティは、照合する関連リソースを定義するすべてのサブプロパティを含みます。

- **Type** [必須]
  - 照合する関連リソースの型を指定します。
  - 最初に **if** 条件リソースの下にあるリソースを取得しようとし、次に **if** 条件リソースと同じリソース グループ内を検索します。
- **Name** (省略可能)
  - 照合するリソースの正確な名前を指定して、指定した型のすべてのリソースではなく 1 つの特定のリソースを取得します。
- **ResourceGroupName** (省略可能)
  - 別のリソース グループに由来する関連リソースを照合できるようにします。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - 既定値は、**if** 条件リソースのリソース グループです。
- **ExistenceScope** (省略可能)
  - 使用できる値は _Subscription_ と _ResourceGroup_ です。
  - 照合する関連リソースを取得する範囲を設定します。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - _ResourceGroup_ の場合は、**if** 条件リソースのリソース グループまたは **ResourceGroupName** で指定されたリソース グループに制限されます。
  - _Subscription_ の場合は、関連リソースのサブスクリプション全体を検索します。
  - 既定値は _ResourceGroup_ です。
- **ExistenceCondition** (省略可能)
  - 指定しない場合、**type** の関連リソースは効果を満たし、監査はトリガーされません。
  - **if** 条件のポリシー規則と同じ言語が使用されますが、それぞれの関連リソースに対して個別に評価されます。
  - 照合する関連リソースのいずれかが true に評価された場合、効果は条件を満たし、監査はトリガーされません。
  - [field()] を使用して、**if** 条件の値と等しいことを確認できます。
  - たとえば、(**if** 条件内の) 親リソースが照合する関連リソースと同じリソースの場所にあることを検証できます。

### <a name="auditifnotexists-example"></a>AuditIfNotExists の例

例: 仮想マシンを評価してマルウェア対策の拡張機能が存在するかどうかを判定し、ない場合に監査します。

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

AuditIfNotExists と同様に、DeployIfNotExists は条件が満たされたときにテンプレートのデプロイを実行します。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists の評価

DeployIfNotExists も、リソース プロバイダーがリソースに対する作成または更新の要求を処理し、成功状態コードを返した後に実行されます。 効果は、関連するリソースが見つからない場合、または **ExistenceCondition** で定義されるリソースが true と評価されない場合にトリガーされます。 効果がトリガーされると、テンプレートのデプロイが実行されます。

評価サイクルでは、リソースを照合する DeployIfNotExists 効果のあるポリシー定義は非準拠としてマークされますが、そのリソースにはアクションは実行されません。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists のプロパティ

DeployIfNotExists 効果の **details** プロパティは、照合する関連リソースおよび実行するテンプレートのデプロイを定義するすべてのサブプロパティを含みます。

- **Type** [必須]
  - 照合する関連リソースの型を指定します。
  - 最初に **if** 条件リソースの下にあるリソースを取得しようとし、次に **if** 条件リソースと同じリソース グループ内を検索します。
- **Name** (省略可能)
  - 照合するリソースの正確な名前を指定して、指定した型のすべてのリソースではなく 1 つの特定のリソースを取得します。
- **ResourceGroupName** (省略可能)
  - 別のリソース グループに由来する関連リソースを照合できるようにします。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - 既定値は、**if** 条件リソースのリソース グループです。
  - テンプレートのデプロイを実行する場合は、この値のリソース グループにデプロイされます。
- **ExistenceScope** (省略可能)
  - 使用できる値は _Subscription_ と _ResourceGroup_ です。
  - 照合する関連リソースを取得する範囲を設定します。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - _ResourceGroup_ の場合は、**if** 条件リソースのリソース グループまたは **ResourceGroupName** で指定されたリソース グループに制限されます。
  - _Subscription_ の場合は、関連リソースのサブスクリプション全体を検索します。
  - 既定値は _ResourceGroup_ です。
- **ExistenceCondition** (省略可能)
  - 指定しない場合、**type** の関連リソースは効果を満たし、デプロイはトリガーされません。
  - **if** 条件のポリシー規則と同じ言語が使用されますが、それぞれの関連リソースに対して個別に評価されます。
  - 照合する関連リソースのいずれかが true に評価された場合、効果は条件を満たし、デプロイはトリガーされません。
  - [field()] を使用して、**if** 条件の値と等しいことを確認できます。
  - たとえば、(**if** 条件内の) 親リソースが照合する関連リソースと同じリソースの場所にあることを検証できます。
- **Deployment** [必須]
  - このプロパティは `Microsoft.Resources/deployments` PUT API に渡されるため、すべてのテンプレートのデプロイを含める必要があります。 詳細については、[Deployments REST API](/rest/api/resources/deployments) をご覧ください。

  > [!NOTE]
  > **Deployment** プロパティ内のすべての関数が、ポリシーではなくテンプレートのコンポーネントとして評価されます。 例外は、ポリシーからテンプレートに値を渡す **parameters** プロパティです。 このセクションのテンプレート パラメーター名の **value** は、この値渡しを実行するために使用されます (DeployIfNotExists の例の _fullDbName_ を参照)。

### <a name="deployifnotexists-example"></a>DeployIfNotExists の例

例: SQL Server データベースを評価して、transparentDataEncryption が有効になっているかどうかを判定します。 有効になっていない場合は、有効にするためのデプロイが実行されます。

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>階層化ポリシー

リソースは複数の割り当ての影響を受ける可能性があります。 これらの割り当ては、同じスコープ (特定のリソース、リソース グループ、サブスクリプション、または管理グループ) である場合も異なるスコープである場合もあります。 これらの各割り当てにもさまざまな効果が定義されている可能性があります。 しかしながら、(直接、またはイニシアチブの一環として割り当てられている) 各ポリシーの条件および効果は個別に評価されます。 たとえば、Deny 効果でサブスクリプション A のリソースの場所が "westus" にのみ作成されるよう制限する条件が設定されたポリシー 1 と、Audit 効果で (サブスクリプション A 内にある) リソース グループ B のリソースの場所が "eastus" のみに作成されるよう制限する条件が設定されたポリシー 2 の両方が割り当てられている場合、結果は次のようになります。

- 'eastus' 内のリソース グループ B の既存のリソースは、ポリシー 2 に準拠しますが、ポリシー 1 に対しては非準拠とマークされます。
- 'eastus' 内にないリソース グループ B の既存のリソースは、ポリシー 2 に対して非準拠とマークされ、'westus' でない場合はポリシー 1 にも非準拠とマークされます。
- 'westus' 内にないサブスクリプション A の新しいリソースは、ポリシー 1 によって拒否されます。
- 'westus' 内のサブスクリプション A/リソース グループ B の新しいリソースは、ポリシー 2 で非準拠とマークされますが、作成されます (ポリシー 1 に準拠し、ポリシー 2 は監査されますが拒否されません)。

ポリシー 1 とポリシー 2 の両方に Deny 効果がある場合、拒否の状況は次のように変わります。

- 'eastus' 内にないリソース グループ B の既存のリソースは、ポリシー 2 に対して非準拠とマークされます。
- 'westus' 内にないリソース グループ B の既存のリソースは、ポリシー 1 に対して非準拠とマークされます。
- 'westus' 内にないサブスクリプション A の新しいリソースは、ポリシー 1 によって拒否されます。
- サブスクリプション A/リソース グループ B 内の新しいリソースは、(場所がポリシー 1 とポリシー 2 の両方を満たすことができないため) 拒否されます。

それぞれの割り当ては個別に評価されるので、スコープの違いによりリソースがギャップを通過する可能性はありません。 したがって、階層化ポリシーまたはポリシーの重複の最終的な結果は、**累積的に最も制限が厳しい**と見なされます。 つまり、ポリシー 1 とポリシー 2 の両方に Deny 効果がある場合、上記の例のようなポリシーの重複および競合によって、作成するリソースがブロックされる場合があります。 リソースを対象のスコープ内に作成する必要がある場合は、それぞれの割り当ての除外を見直して、適切なポリシーが適切なスコープに影響を与えていることを確認します。

## <a name="next-steps"></a>次の手順

これでポリシー定義の効果をより深く理解できました。次のポリシーのサンプルを確認してください。

- 他の例については、「[Azure Policy のサンプル](json-samples.md)」をご確認ください。
